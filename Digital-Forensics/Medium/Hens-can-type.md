Name: Hens can type? | BlueHens CTF 2026

Solved by: Z3DX

In this write-up, we will solve a forensics challenge involving the analysis of USB traffic data.
The goal is to investigate the captured file and uncover the hidden information within it.


<img width="575" height="513" alt="Screenshot 2026-04-22 045349" src="https://github.com/user-attachments/assets/f42b3405-c55c-4394-bff5-02732e0dc2ad" />


From the challenge description, we can infer that a USB traffic capture was recovered from a suspicious machine, 
and it is suspected that a user typed something important. 
The objective is to carefully analyze the provided data and reconstruct what was entered, in order to uncover any hidden information left behind.


<img width="540" height="292" alt="aniyuki-gif-naruto-83" src="https://github.com/user-attachments/assets/077e6155-36dc-4a46-8b48-c5ce1f9f4fca" />



I started by opening the provided file using Wireshark to inspect the captured packets and understand the USB traffic in more detail. 
The initial review shows raw USB communication that requires further investigation to determine its purpose.


<img width="1917" height="596" alt="Screenshot 2026-04-22 045555" src="https://github.com/user-attachments/assets/5d35d02d-713f-4079-8e9c-adcaf42ef678" />


I then applied a filter to narrow down the captured packets and focus on relevant USB traffic: ==> usb.capdata 
This helped in isolating the data related to USB device activity for further analysis


<img width="1916" height="785" alt="Screenshot 2026-04-22 050106" src="https://github.com/user-attachments/assets/9fe95562-2957-4b1e-8bd8-3405489bdc49" />



After applying the usb.capdata filter, I could see the raw keystroke codes in a 8-byte format. 
I noticed that the 3rd byte was changing every time a key was pressed, confirming that this was a keyboard capture

Therefore, I decided to use Tshark to simplify the process:

Why I used Tshark?

1: To quickly pull the hex bytes into a clean text file.
2: To strip away all network headers and keep only the keystrokes.
3: To prepare the data for my Python script to translate it into the final flag


<img width="220" height="122" alt="one-piece-anime" src="https://github.com/user-attachments/assets/cd557cf8-afd9-4cdf-811b-528a0b5b3d30" />


I then used the following command to extract the relevant USB data into a separate file:

$: tshark -r capture.pcapng -Y "usb.capdata" -T fields -e usb.capdata > usbdata1.txt 

This command reads the capture file and filters only packets containing USB keyboard data (usb.capdata). 
It then extracts the raw hexadecimal values from those packets and saves them into usbdata.txt,
making the data easier to analyze in a structured format.


<img width="1901" height="170" alt="Screenshot 2026-04-22 051408" src="https://github.com/user-attachments/assets/b32b79ff-0b89-411b-a2d0-bcfbc47e8e39" />


After opening the extracted file usbdata1.txt, we were presented with raw hexadecimal data representing USB HID keyboard activity.
At first glance, the data did not resemble readable text,
as it consisted of continuous hex values corresponding to low-level keyboard events.

<img width="1150" height="789" alt="Screenshot 2026-04-22 051550" src="https://github.com/user-attachments/assets/f5c2ff6c-8fbc-4e44-a09c-25e3d4ca1704" />


This type of data cannot be interpreted directly, 
so it requires processing to translate it into human-readable input. For this reason,
a custom Python script was used: 


import sys
mapping = {
 0x04:"a", 0x05:"b", 0x06:"c", 0x07:"d", 0x08:"e", 0x09:"f", 0x0a:"g", 0x0b:"h", 0x0c:"i", 0x0d:"j", 0x0e:"k", 0x0f:"l", 0x10:"m", 0x11:"n", 0x12:"o", 0x13:"p", 0x14:"q", 0x15:"r", 0x16:"s", 0x17:"t", 0x18:"u", 0x19:"v", 0x1a:"w", 0x1b:"x", 0x1c:"y", 0x1d:"z", 0x1e:"1", 0x1f:"2", 0x20:"3", 0x21:"4", 0x22:"5", 0x23:"6", 0x24:"7", 0x25:"8", 0x26:"9", 0x27:"0", 0x28:"\n", 0x2c:" ", 0x2d:"-", 0x2e:"=", 0x2f:"[", 0x30:"]", 0x33:";", 0x34:"'", 0x36:",", 0x37:".", 0x38:"/"
}
shift_mapping = {
 0x1e:"!", 0x1f:"@", 0x20:"#", 0x21:"$", 0x22:"%", 0x23:"^", 0x24:"&", 0x25:"*", 0x26:"(", 0x27:")", 0x2d:"_", 0x2e:"+", 0x2f:"{", 0x30:"}", 0x33:":", 0x34:'"', 0x36:"<", 0x37:">", 0x38:"?"
}
with open("keyboard_only.txt", "r") as f:
 for line in f:
 data = line.strip().replace(":", "")
 if not data or len(data) != 16: continue
 
 modifier = int(data[0:2], 16)
 key_code = int(data[4:6], 16)
 
 if key_code == 0: continue
 
 if modifier == 0x02 or modifier == 0x20: 
 if key_code in shift_mapping:
 print(shift_mapping[key_code], end="")
 elif key_code in mapping:
 print(mapping[key_code].upper(), end="")
 else:
 if key_code in mapping:
 print(mapping[key_code], end="")
print()



The script was chosen because it allows structured parsing of each USB packet,
extracting key components such as the keycode and modifier state. 
This approach makes it possible to systematically convert raw HID input into meaningful characters.


so after run script :

<img width="1871" height="219" alt="Screenshot 2026-04-22 052242" src="https://github.com/user-attachments/assets/4212df8e-7a97-4c3f-a6fc-8c5d730a74c2" />

.

so The Flag Is : UDCTF{k3y_StR0K3E_1S_7he_wAy} 


<img width="540" height="400" alt="aniyuki-gif-naruto-87" src="https://github.com/user-attachments/assets/e0f64c01-b17d-4244-82e9-db86d4c69177" />

happy hacking !!






























