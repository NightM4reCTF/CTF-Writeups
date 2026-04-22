Freakquency | BlueHens CTF 2026

Solved By : Z3DX

This write-up covers a digital forensics challenge involving hidden data inside an audio file.
By analyzing the file and using the right tools, I was able to uncover the hidden message and move toward the final flag.


<img width="604" height="379" alt="Screenshot 2026-04-22 223816" src="https://github.com/user-attachments/assets/32220962-69db-4398-8a3f-65e59b9006cc" />


At the beginning, we notice that the challenge provides an audio file. From the description, "You have the audacity to match my freak?",
we can infer a hint toward using an audio analysis tool, specifically Audacity.
This suggests that the solution likely involves inspecting the audio more deeply, such as analyzing its spectrogram to uncover hidden data.

<img width="540" height="405" alt="c92183fa8a79f88986ff44daebb2fe55" src="https://github.com/user-attachments/assets/124b504b-8a53-46df-a392-bf4814687aa2" />


When I used the file command on the audio file, it returned:

<img width="1480" height="145" alt="Screenshot 2026-04-22 230100" src="https://github.com/user-attachments/assets/cfde0ae6-0250-414e-9444-573cbc1b6280" />


This indicated that the file is a normal WAV audio file. At first, I suspected that it might contain Morse code hidden in the sound, 
so I decided to play the audio. However, it did not sound like Morse code at all, which led me to consider other analysis techniques.
However, I paused for a moment and reconsidered the challenge description. The phrase "audacity" seemed like a clear hint toward using Audacity.
This led me to think that the hidden data might be embedded within the spectrogram of the audio file rather than in the sound itself.



<img width="500" height="285" alt="5a4060b8efbfcd53c3ec55ea706fbad4" src="https://github.com/user-attachments/assets/513da65e-7b63-4d7a-b169-c4970e139922" />



Instead, I chose to use Sonic Visualiser rather than Audacity, as it provides more detailed visualization for analyzing audio data.



<img width="1851" height="372" alt="Screenshot 2026-04-22 230439" src="https://github.com/user-attachments/assets/3c74c3e7-abce-42b8-b38f-4611761b5f7a" />



I then loaded the file into Sonic Visualiser. From the top menu bar, I selected Layer → Add Spectrogram to visualize the audio data.


<img width="823" height="608" alt="Screenshot 2026-04-22 230644" src="https://github.com/user-attachments/assets/b7ad75e0-9d2e-4e43-8a9c-c518a5b0f9e6" />



Indeed, I was able to spot hidden text within the audio file. It appeared to be encoded in Base64, 
which indicated that further decoding would be required to reveal the actual message.



<img width="1253" height="494" alt="Screenshot 2026-04-22 230703" src="https://github.com/user-attachments/assets/b3efe228-8294-4a62-a9f3-8ea0bdc64f1d" />


To make the text clearer, I adjusted the spectrogram settings accordingly.



<img width="896" height="431" alt="Screenshot 2026-04-22 230742" src="https://github.com/user-attachments/assets/caddb79f-2202-4912-96d0-504207fd9574" />

after :


<img width="1240" height="345" alt="Screenshot 2026-04-22 230751" src="https://github.com/user-attachments/assets/8850cc79-fdba-4870-8661-c4c8dedd7a4e" />


The extracted text :
VURDVEZ7dzB3X3kwdV9jNG5faDM0cl80X2YxbDM/fQ

After decoding the extracted string using the following command:

$ echo "VURDVEZ7dzB3X3kwdV9jNG5faDM0cl80X2YxbDM/fQ" | base64 -d

we successfully retrieved the flag.


<img width="1189" height="140" alt="Screenshot 2026-04-22 231405" src="https://github.com/user-attachments/assets/905604c8-4732-4b97-8dbe-1d6fdeaa2cff" />


So the Flag Is : UDCTF{w0w_y0u_c4n_h34r_4_f1l3?}




<img width="500" height="280" alt="bfec72c6d80f7ad70f135ea8df5c4e45" src="https://github.com/user-attachments/assets/673f1222-b9e8-4eb7-8b50-92cf3c57d512" />



happy hacking !!























