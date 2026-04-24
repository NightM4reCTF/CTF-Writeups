# Storytime| BlueHens CTF 2026
> Solved by: Z3DX 

This is a misc challenge that revolves around interpreting hints and thinking through a sequence of transformations. At first glance,
it may seem a bit confusing, but with a structured approach,
 it becomes much more manageable. The key is to stay patient and follow the logic behind each step.
 

<img width="617" height="429" alt="Screenshot 2026-04-24 012927" src="https://github.com/user-attachments/assets/b7ec36ed-2aae-40c0-8623-2de933f0373b" />

The challenge came with two files: storytime.txt and ciphertext.txt, which were used throughout the analysis process.
After opening the first file, story.txt, I found the following text:

-------------------------------------------------------------------------------------------------------------------------------

"Here's the average day of the life of me, your average UD student! First thing I do is wake up in the morning and get ready the day ahead. 
I talk to my roommate and just randomly blurt out, "The position of my first plus one times the position of my second equals the position of my third!".
I sincerely apologized to him and explained that I got cursed a few years back. Next,
I played a little bit of Minecraft with him, but we had to end a little early, and our base was just 4 chunks away! Next,
I head over to the gym to get a quick workout in. Ever since I became UD's mascot, I've been able to exorcise a lot more! 
It's nice having lenience from my classes. I continue to work on a project that I've been working on for a little less than two weeks.
It's really been rotting my social battery. I started to walk back to my dorm when I saw these two people who I have some bad blood with.
Let's just say they stabbed me in the back before. When I got back to my dorm after a long day, I realized I'm still nowhere near finished with my project.
At some points, it feels like I'm bashing my head into a wall. After everything is done, I take a shower,
do my nightly routine and head to sleep. And that's my day!"

-------------------------------------------------------------------------------------------------------------------------


<img width="540" height="304" alt="704677530891f0602c5f9a86d7a78f8c" src="https://github.com/user-attachments/assets/2780c5ac-3361-441e-b1c9-d3349d8245dc" />



After opening the second file, cipher.txt, I found an encrypted text that appeared to be the main ciphertext for the challenge.
The content was not directly readable and clearly required further analysis and decoding.


---------------------------------------------------------------------------------------------------------------------------

4e5451315954517a4e5751315a6a55794e5749774d4455314e5451304d6a42694e5755314d6a55344e4745314e5455354e4459314e6a566d4d474531596a41774e5455314e4451774e544131596a55304e5467304e5455304d444d304d7a45304e5759314e6a566a4e4449314d54566d4e4449784e44566c4e574d3d

---------------------------------------------------------------------------------------------------------------------------

<img width="320" height="175" alt="209216" src="https://github.com/user-attachments/assets/e29cf507-dde2-4227-8e28-8e9b5e622663" />


At the beginning, while reading the storytime file, 
I was able to extract several hints that helped guide the solution process and eventually led to decrypting the ciphertext.


# hint1 
"The position of my first plus one times the position of my second equals the position of my third! I sincerely apologized to him and explained that I got cursed a few years back."
This hint indicates that the positions of certain parts of the ciphertext are important. It suggests a relationship between different positions within the encoded data,
pointing to the use of a structured encoding method. The mention of being "cursed" is a subtle reference to hexadecimal format.
From this hint, we can deduce that Hex decoding is applied at specific positions in the cipher chain, namely positions 1, 2, and 6.
 This is derived from the sentence:
 "The position of my first plus one times the position of my second equals the position of my third", 
 which implies a relationship between specific indexed parts of the data. When combined with the mention of being "cursed," it points toward hexadecimal formatting, confirming that these referenced positions correspond to Hex-encoded segments that need to be decoded.


<img width="1553" height="653" alt="Screenshot 2026-04-24 020536" src="https://github.com/user-attachments/assets/e07a7543-d6d0-45d4-ab03-a6a752163dc2" />


# hint2

"Next, I played a little bit of Minecraft with him, but we had to end a little early, and our base was just 4 chunks away!"
This hint refers to Minecraft mechanics, where one chunk equals 16×16 blocks. Therefore, 4 chunks correspond to 64 units. 
This directly points toward Base64 encoding.
From this, we can conclude that the second stage in the cipher chain requires Base64 decoding, 
as the value 4 chunks is a clear reference to the number 64 used in Base64 representation.


<img width="1541" height="684" alt="Screenshot 2026-04-24 020742" src="https://github.com/user-attachments/assets/72374f5e-e3f8-405f-9d49-903153a28c6a" />


We notice that after applying Base64 decoding, the output was not yet in its final usable form. Instead, it still appeared as another layer of encoded data. 
This is why we applied Hex decoding once again to properly convert the result into readable bytes before moving to the next step in the decryption process.


<img width="1538" height="627" alt="Screenshot 2026-04-24 021059" src="https://github.com/user-attachments/assets/e93b3b3a-e14e-45e4-ab01-dbe6de44522b" />

# hint3

"Ever since I became UD's mascot, I've been able to exorcise a lot more!"
This hint is a wordplay on the term "exorcise", which sounds like "XOR", indicating that this step involves an XOR operation.
The reference to the university mascot is used to determine the key, which is "bluehens".
When applying XOR, the operation is performed on the UTF-8 encoded data, meaning the text must first be interpreted as bytes in UTF-8 format rather than Hex.
This is important because XOR operates at the byte level, not on hexadecimal strings directly.
Therefore, the correct process at this stage is:
1: Convert data to UTF-8 bytes
2: Apply XOR using the key bluehens


<img width="1546" height="771" alt="Screenshot 2026-04-24 021247" src="https://github.com/user-attachments/assets/ef62e006-51f4-48dc-8c90-c87b0c8ac818" />

# hint4

"I continue to work on a project that I've been working on for a little less than two weeks. It's really been rotting my social battery."
This hint refers to the word "rotting", which suggests the use of ROT cipher. The mention of "a little less than two weeks" points to the number 13 (since two weeks is 14 days), which directly indicates ROT13.
From this, we deduce that this step requires applying ROT13 decoding to the data.

<img width="1549" height="777" alt="Screenshot 2026-04-24 021434" src="https://github.com/user-attachments/assets/1e21961a-d350-4b80-abe7-945426445fbb" />

However, we notice again that after applying the XOR operation, 
the resulting output is still not directly readable. Instead, it appears as another layer of encoded data.
This indicates that the data was not fully decoded yet,
and an additional Hex decoding step is required to properly convert the XOR output into a readable format before continuing with the next stage of the chain.

<img width="1535" height="746" alt="Screenshot 2026-04-24 022504" src="https://github.com/user-attachments/assets/712556c7-52a5-4619-bb37-4856e19ffa3c" />



# hint5

"I started to walk back to my dorm when I saw these two people who I have some bad blood with. Let's just say they stabbed me in the back before."
This hint refers to the historical event of Julius Caesar being betrayed and stabbed, which is associated with Caesar ciphers.
The mention of "two people" also suggests a structural shift in the encoding process.
From this, we deduce that this step uses a Caesar Box Cipher with a shift of 2 to rearrange the characters and reveal the hidden message.


<img width="1537" height="757" alt="Screenshot 2026-04-24 025320" src="https://github.com/user-attachments/assets/f7125340-c71a-4a42-b205-e37aff737d70" />

# hint6

"At some points, it feels like I'm bashing my head into a wall."
This hint directly refers to the idea of reflection or inversion. 
The phrase "bashing my head into a wall" suggests a mirrored or reversed transformation, which is a clear reference to the Atbash cipher.
Atbash is a substitution cipher where each letter is mapped to its opposite in the alphabet (A ↔ Z, B ↔ Y, etc.). 
This means the entire message is transformed by flipping the alphabet, effectively reversing the character mapping.
From this hint, we conclude that the final step of the decryption process is applying the Atbash cipher to obtain the final readable output.


<img width="1538" height="766" alt="Screenshot 2026-04-24 025522" src="https://github.com/user-attachments/assets/65b98356-9cc3-498a-aba7-04c0d71789c8" />

So The Flag Is : udctf{c1ph3rs_ar3_Fun!}

<img width="540" height="405" alt="bf7c90a3a09728ef031c7e7958ff37f3" src="https://github.com/user-attachments/assets/e69c4132-11b6-4dc1-b045-18cb92cd34f0" />

See you in the next hack 🔐🚀











































