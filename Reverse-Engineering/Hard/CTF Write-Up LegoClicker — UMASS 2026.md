
### Reversing a Custom VM & Dodging Red Herrings 🚀

**Solved by:** Wa3r

**Category:** Android Native Reverse Engineering (Medium-Hard)

**Flag:** `UMASS{br1ck_by_br1ck_y0u_r3ach3d_th3_t0p}`

---

### 🧠 The Initial Recon

When you first unpack this Android APK, you won’t find the logic written in friendly Java or Kotlin. The entire core of the challenge lives inside a stripped ARM64 native shared library: `liblegocore.so`.

Loading it into Radare2 (`r2 liblegocore_arm64.so`), we immediately hit a wall. There are no obvious exported Java functions (like `Java_com_umass_...`).

> **_🧠 Hacker Insight:_** _When an Android native library hides its exports, it’s using_ **_Dynamic JNI Registration_**_. The library calls_ `_RegisterNatives_` _inside_ `_JNI_OnLoad_` _to map memory addresses to Java methods at runtime. By tracing_ `_JNI_OnLoad_`_, we can find the hidden endpoints:_ `_verifyScore()_`_,_ `_getFlag()_`_, and a score helper._

Our goal is simple: Figure out what `getFlag()` is doing. But the developer laid out a minefield of red herrings to waste our time.

---

### 🔍 Step 0: The Hacker’s Checklist

Before writing scripts or forcing emulation, we map out the attack surface and the developer’s traps:

- Is it a standard crypto algorithm? **No, it’s a Custom Bytecode Virtual Machine (VM).**
- Are the keys dynamically generated? **The code _wants_ you to think they are.**
- Can we just emulate it with `angr` or `ESIL`? **No, they intentionally broke emulation using empty jump tables.**

When an author tries this hard to break automated tools, you have to stop fighting the code and start looking at the _data_.

---

### 🤡 Trap 1: The Troll Flag

While analyzing the `getFlag()` path, you quickly stumble upon a function that builds a string using a simple modulo lookup. Decoding it yields:

`BHREV{fAk3_flAG_wr0ng_s3ss10n}`

If you submit this, you just got trolled 💀. Notice that `BHREV` is just a ROT-7 cipher of `UMASS`. This is a classic CTF trap meant to bait early submitters. We need to dig deeper.

---

### ⚙️ Trap 2: The Fake Timing Key (The Master Illusion)

The most brilliant misdirection in this challenge happens at startup. Inside `JNI_OnLoad`, the code calls `clock_gettime(CLOCK_REALTIME)` and `CLOCK_MONOTONIC`. It mixes these unpredictable timestamps through crazy shifts and XORs, saving the result as a key (`key_d07e`).

If the key is based on the exact millisecond the app launched, how can we ever decrypt the flag statically?

> **_🧠 Hacker Insight:_** _Don’t trust the math; do the math. The timing computation ultimately collapses to this equation:_ `_orn(x, x) + 1 XOR x_`_._

> _In binary logic,_ `_orn(x, x)_` _(Bitwise OR NOT) results in all_ `_1_`_s (-1). Add 1 to it, and you get_ `_0_`_._

> `_0 XOR x = x_`_. Wait, looking closer at the full loop, both clock readings actually zero themselves out entirely. The "dynamic" key is literally just_ `_[0, 0, 0, 0]_`_._

Furthermore, tracing the initialization function reveals that the _real_ keys (`key_dead`, `key_cafe`, `key_aced`) are copied directly from `.rodata` into memory. **The keys are completely static!**

---

### 🧩 Bug 3: The Custom VM & Interleaved Data

The real flag engine is a custom VM. It loops 41 times (for our 41-byte flag), taking a byte of ciphertext and passing it through a pipeline of handlers.

Instead of a normal string, the ciphertext is sliced into three separate arrays (`arr0`, `arr1`, `arr2`) and interleaved. The VM fetches a byte based on `i % 3`, then runs it through its custom opcodes:

1. `**DEAD**`: `byte XOR key_dead[(i+1) & 7]`
2. `**C0DE**`: `byte - key_dead[i & 7]`
3. `**CAFE**`: `byte XOR key_cafe[i & 7]`
4. `**F00D**`: `rotl8(byte, 5)` _(Rotate left 5 bits)_
5. `**ACED**`: `byte XOR key_aced[i & 7]`
6. `**D07E**`: `byte XOR key_d07e[i % 4]` _(Which we know is just 0)_

**The Obfuscation Trick:** The developer tried to hide the XOR operations by writing them as `(A | B) - (A & B)`. Nice try, but algebraically, `(A OR B) minus (A AND B)` is the exact definition of `A XOR B` 😏.

---

### 🎯 Building the Exploit Chain

Since the keys are static in `.rodata` and the VM logic is fully understood, we don't need to emulate ARM64 code. We can just write a clean Python script to extract the static data from the binary and reverse the VM pipeline!

Python

with open("liblegocore_arm64.so", "rb") as f:  
    data = f.read()  
# 1. Extract static keys from .rodata  
key_aced = list(data[0x140c0:0x140c8])  
key_cafe = list(data[0x140f0:0x140f8])  
key_dead = list(data[0x140e0:0x140e8])  
key_d07e = [0, 0, 0, 0] # The fake timing key 😂  
# 2. Extract interleaved ciphertext arrays  
arr0 = list(data[0x14108:0x14108+14])  
arr1 = list(data[0x141ba:0x141ba+14])  
arr2 = list(data[0x141c8:0x141c8+13])  
def get_byte(i):  
    q = i // 3  
    return [arr0, arr1, arr2][i % 3][q]  
# The un-obfuscated XOR logic  
def xor_op(byte, key):  
    return ((key | byte) - (key & byte)) & 0xFF  
flag = []  
for i in range(41):  
    b = get_byte(i)  
    b = xor_op(b, key_dead[(i+1) & 7])      # DEAD  
    b = (b - key_dead[i & 7]) & 0xFF        # C0DE  
    b = xor_op(b, key_cafe[i & 7])          # CAFE  
    b = ((b << 5) | (b >> 3)) & 0xFF        # F00D (rotate left 5)  
    b = xor_op(b, key_aced[i & 7])          # ACED  
    b ^= key_d07e[i % 4]                    # D07E  
    flag.append(b)  
print(''.join(chr(c) for c in flag))

### 💥 Step 4: Capture the Flag

Run the script, and the custom VM’s encryption completely falls apart:

`UMASS{br1ck_by_br1ck_y0u_r3ach3d_th3_t0p}`

😏 _nice._

---

### 🤯 Conclusion: The Anatomy of Misdirection

This challenge wasn’t hard because the crypto was complex; it was hard because the developer was a master of psychological warfare. Let’s review the traps:

**The TrapThe Developer’s GoalThe RealityDynamic JNI**Hide endpoints from basic string searchesBypassed by tracing `JNI_OnLoad`**Troll Flag**Bait you into submitting earlyIt was ROT-7 encoded garbage**Empty Jump Tables**Crash automated emulators (angr/ESIL)Bypassed by statically analyzing the math**clock_gettime()**Make keys look runtime-dependentThe math zeroes out; keys are static in `.rodata(A|B) - (A&B)`Hide XOR operationsStandard boolean logic identity for XOR

---

### ⚡ TL;DR

- Unpack APK and locate dynamic JNI endpoints in the ARM64 `.so`.
- Ignore the `BHREV` troll flag.
- Realize the `clock_gettime` timing logic zeroes itself out.
- Extract the _real_ static keys directly from the `.rodata` section.
- Reconstruct the Custom VM’s opcodes (`DEAD`, `C0DE`, `CAFE`, etc.) in Python.
- Decrypt the interleaved arrays.
- **booom 💥 flag**

> **_🎤 Final Thought:_** _In Reverse Engineering, code can lie to you. Variable names can lie to you. Emulators can be tricked. But memory mapping and raw data never lie._ **_Follow the data, not the code._**

Happy hacking!

صلي عالنبي 🤍

- **LinkedIn:** [linkedin.com/in/wa3r](https://www.google.com/search?q=https://linkedin.com/in/wa3r&authuser=1)
- **GitHub:** [Wa3r-OffSec-Kit](https://github.com/Abdowaer098/Wa3r-OffSec-Kit)
