
UMASS 2026 CTF Bricktator Reactor Override
**Solved by:** Wa3r


### Welcome to the Nuclear Control Center
**Category:** Web Exploitation (Medium-Hard)

**Flag:** `UMASS{stUx_n3T_a1nt_g0T_n0th1nG_0N_th15}`

---

![](https://cdn-images-1.medium.com/max/1067/1*_LgMi9pNEiNHodzW4BoqNg.png)

  

### 🧠 The Initial Recon

Welcome to the Nuclear Control Center. When you first open the challenge, you are greeted by a very slick, classified authentication terminal.

![](https://cdn-images-1.medium.com/max/1067/1*s0zo51deerwTRas_kOe4Yw.png)

  

The mission? The “Bricktator” must be stopped, and we need to trigger a reactor override to shut the system down.

Looking at the provided Spring Boot source code, we find the core objective: the `/command/override` endpoint initiates the shutdown. But there is a catch. The `OverrideService` requires **5 separate approvals** from sessions that hold the elite `YANKEE_WHITE` clearance role.

We are given one set of credentials (`bricktator:goldeagle`), which gives us our first `YANKEE_WHITE` session.

**Our goal:** We need to forge, steal, or find 4 more.

---

### 🔍 Step 0: The Hacker’s Checklist

Before we start guessing passwords, let’s map out the attack surface.

- What framework is this? **Spring Boot.**
- Are there any management endpoints exposed? **Yes, Spring Actuator is wide open.**
- How are session IDs created? **Mathematically, using Shamir’s Secret Sharing.**

When an application exposes its internal metrics and uses custom cryptography for session management, the bugs are going to be wild.

---

### ⛈️ The Perfect Storm: Unpacking the Bugs

This exploit requires chaining a configuration leak, finite-field mathematics, and a classic side-channel attack. Let’s break them down.

### Bug 1: The Actuator Leak

Checking the Spring security config, we find that `/actuator/sessions` is exposed. This endpoint allows us to query active sessions for specific users. We can instantly grab the session IDs for known users like `John_Doe` and `Jane_Doe`.

### Bug 2: Shamir’s Secret Sharing (The Math Exploit)

Looking at `SessionSeeder.java`, the app generates 5,000 valid sessions at startup. The session IDs aren't random UUIDs; they are generated using **Shamir's Secret Sharing** (SSS).

The IDs are formatted as hex coordinates: `String sessionId = "%05d-%08x".formatted(share.x(), share.y());`

> **_🧠 Hacker Insight:_** _Shamir’s Secret Sharing splits a secret into parts using a mathematical polynomial. The code reveals the threshold is $k=3$. This means if we have exactly 3 coordinate pairs $(x, y)$, we can reconstruct the entire polynomial over the finite field $\mathbb{Z}_{2147483647}$!_

Since we have 3 users (John, Jane, and Bricktator), we can query the Actuator endpoint, decode their session IDs into $(x,y)$ coordinates, and mathematically generate the valid session IDs for **all 5,000 seeded users.**

### Bug 3: The BCrypt Timing Attack (The Side-Channel)

Now we have 5,000 valid session IDs, but the app randomly assigned the `YANKEE_WHITE` role to only 7 of them. If we just guess, we'll be here all day.

Reviewing `CommandWorkFilter.java`, we spot a fatal flaw:

Java

if (session != null && "YANKEE_WHITE".equals(session.getAttribute("role"))) {  
    accessLog.record(bcrypt.encode(rawId + PEPPER));   
}

If a session has the `YANKEE_WHITE` role, the server hashes the ID using **BCrypt with a cost/strength of 13** before logging it.

BCrypt cost 13 is computationally expensive! It takes roughly **0.8 to 1.2 seconds** for the server to process. If the session is _not_ `YANKEE_WHITE`, it skips the hash entirely and responds in `< 0.5 seconds`.

_(Note: Initially, I tried to just read_ `_/actuator/accesslog_` _to see who hit the endpoint. But the CTF admins had a background bot generating random traffic that flooded the logs with false positives. So, we ignore the logs and trust the timing!)_

---

### 🎯 Building the Exploit Chain

We don’t need to guess passwords, and we don’t need to read the logs. We are going to use math to forge the keys, and time to find the locks.

### Step 1: Fetch the Shares

We log in as `bricktator` and hit `/actuator/sessions` to grab the session coordinates for `John_Doe` ($x=1$), `Jane_Doe` ($x=5$), and `bricktator` ($x=5001$).

### Step 2: Crack the Polynomial

Using a simple Python script with Lagrange interpolation, we feed it our 3 coordinates. The script reconstructs the hidden polynomial and spits out the exact session IDs for all 5,000 seeded users.

### Step 3: The Multithreaded Timing Attack

We load the 5,000 forged session IDs into a fast Python scanner and fire them at the `/command` endpoint.

We aren’t looking at the HTTP response codes; we are looking at the clock.

- Response time **0.1s** ➡️ Normal user (Ignore).
- Response time **1.1s** ➡️ `**YANKEE_WHITE**` **user! (Save it!)**

### Step 4: Nuclear Override

Once our scanner isolates 4 slow-responding session IDs, the game is over.

We initiate the override via `POST /command/override` using our Bricktator session. Then, we loop through our 4 stolen `YANKEE_WHITE` session IDs and submit them to `POST /override/{token}`.

---

### 💥 Step 5: Capture the Flag

Upon the 5th collective approval, the reactor shutdown sequence is authorized, and the server dumps the flag into the HTML template:

`UMASS{stUx_n3T_a1nt_g0T_n0th1nG_0N_th15}`

😏 _nice._ (Stuxnet really ain’t got nothing on this).

---

### 🤯 Conclusion: The Anatomy of a Chain

This challenge is a masterpiece of chaining completely different vulnerability classes:

- An exposed Actuator endpoint is just an information leak.
- A low threshold in Shamir’s Secret Sharing is useless without the shares.
- A BCrypt timing side-channel is useless without valid session IDs to test.

But chained together? We use the leak to get the shares, the shares to break the math, and the math to feed the timing attack. **Total Reactor Meltdown.**

> **_🎤 Final Thought:_** _In Web Exploitation, logs can be spammed, and outputs can be hidden. But the server’s CPU can’t lie to you about how hard it’s working._ **_Don’t trust the logs; trust the time._**

Happy hacking!

صلي عالنبي
