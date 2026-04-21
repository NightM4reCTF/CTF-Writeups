🕵️ Forensics Challenge Writeup: Name Calling  | BlueHens CTF 2026

This write-up covers the "Name Calling" challenge from BlueHens CTF 2026. 
It shows how a small clue in network traffic can lead to hidden data and eventually the flag through simple forensic analysis.


<img width="622" height="392" alt="Screenshot 2026-04-21 224422" src="https://github.com/user-attachments/assets/12effd18-78f9-43ec-96df-fd7ee0a71a51" />

I started by downloading the challenge file. It was a PCAPNG file,
so the first step was to open it using Wireshark to analyze the captured network traffic and look for any useful clues.

<img width="1902" height="837" alt="Screenshot 2026-04-21 230139" src="https://github.com/user-attachments/assets/169612fe-ecd9-4235-a875-dbc694aa3cea" />

After opening the PCAPNG file in Wireshark, I started analyzing the captured traffic and noticed multiple protocols
such as ARP, TCP, and HTTP, which indicated a mixed network communication that required deeper inspection.
I began filtering and exploring the packets to understand the flow of data and identify anything unusual. During the analysis,

<img width="622" height="392" alt="Screenshot 2026-04-21 224422" src="https://github.com/user-attachments/assets/091ca954-c6e4-4f72-a721-e8ced02886d6" />

I came across a hint in the challenge description mentioning the word "chicken,"
which gave me a clear direction to focus on specific traffic related to that keyword.

<img width="540" height="292" alt="aniyuki-gif-naruto-83" src="https://github.com/user-attachments/assets/d70b7321-5ba2-4764-9472-2d4b50dbc2f6" />


To narrow down the search and focus on relevant traffic,
I used the display filter frame contains "chicken" in Wireshark.
This filter searches through the raw packet data for any occurrence of the word "chicken" inside the frame content. 
It helped quickly isolate the packets related to the hint,
making it easier to locate the suspicious HTTP request and continue the investigation.

The frame keyword refers to the entire packet data, including headers and payload By using contains



<img width="1918" height="711" alt="Screenshot 2026-04-21 231458" src="https://github.com/user-attachments/assets/88008ac5-5fad-46a0-8439-3aea5b15e53d" />

After applying the filter, I was able to isolate the relevant traffic and found an HTTP request where a client was trying to retrieve a file named chicken.jpg.
This indicated that the keyword "chicken" was directly linked to a transferred image,
which became the next focus of the investigation.



To extract the file, I used Wireshark's built-in feature by going to File → Export Objects → HTTP. From there, 
I located the requested image and saved it locally for further analysis. 
This allowed me to retrieve the transferred content directly from the captured traffic.



<img width="639" height="612" alt="Screenshot 2026-04-21 231825" src="https://github.com/user-attachments/assets/676f7b57-aa11-4cab-ae27-16e28837b7e6" />

after save:

<img width="1641" height="228" alt="Screenshot 2026-04-21 232034" src="https://github.com/user-attachments/assets/37d2e63c-6bb0-4998-b642-a5d3c3f73f28" />


After saving the extracted files, I listed the directory contents and noticed several decoy text files along with multiple images.
However, two files immediately caught my attention: an image chicken.jpg  and a ZIP archive.
These stood out as they seemed more relevant to the challenge compared to the rest of the decoy files,
so I decided to focus my analysis on them next.


lets to open image :


<img width="1217" height="778" alt="Screenshot 2026-04-21 232248" src="https://github.com/user-attachments/assets/d2a5c850-ab68-498d-a994-56d0bd02037f" />



After opening the image, it appeared to be just a normal picture of a chicken. To gather more information, I used the file command to check file



<img width="1903" height="216" alt="Screenshot 2026-04-21 232532" src="https://github.com/user-attachments/assets/405b5565-7fb8-4865-9420-8e3e61f5f8f9" />



After using the file command on the image, I noticed that it contained additional metadata labeled as Exif information, 
which is automatically included in many JPEG files. Inside this metadata,
there was a field called copyright that contained a long hexadecimal string: 6e6f626f64792063616c6c73206d6520636869636b656e21. 
This indicated that hidden data was embedded within the file's metadata rather than the visible image itself. 
I then converted this hex value into plain text using a hex-to-string conversion tool,
which revealed a hidden message useful for the next step of the challenge.


<img width="320" height="175" alt="209216" src="https://github.com/user-attachments/assets/37512fef-0f80-4ff0-8498-a628240855cf" />

To decode the hidden hexadecimal string, I used the command echo 6e6f626f64792063616c6c73206d6520636869636b656e21 | xxd -r -p.
This command takes the hex value and converts it back into readable ASCII text. As a result, the hidden message was revealed,
confirming that the data was intentionally encoded within the image metadata as part of the challenge.




<img width="1196" height="139" alt="Screenshot 2026-04-21 232925" src="https://github.com/user-attachments/assets/b8ceb42b-0900-456e-87f3-c7248391086c" />


The decoded output was: "nobody calls me chicken!". After seeing this message,
I suspected that it might be a password related to the ZIP file, since challenges often reuse extracted hints as keys for protected archives. 
Based on this idea, I decided to test it as the password for the compressed file in the next step.



<img width="928" height="216" alt="Screenshot 2026-04-21 233111" src="https://github.com/user-attachments/assets/57554a03-6fed-45d1-a1b6-191bbd9da0d2" />


After using the extracted phrase as the password, the ZIP file was successfully opened.
Inside, I found a text file named whoareyoucalling.txt, which contained the final flag of the challenge.



<img width="593" height="170" alt="Screenshot 2026-04-21 233206" src="https://github.com/user-attachments/assets/4b39daac-abf5-45a3-baf8-fb3905b3c810" />


so The Flag Is : UDCTF{wh4ts_wr0ng_mcf1y} 



<img width="500" height="281" alt="1_BWvfX2atOMRxAs9vPg5YwA" src="https://github.com/user-attachments/assets/a0ab3f85-c473-4091-b3b1-3341973a85ab" />

happy hack ! 













































