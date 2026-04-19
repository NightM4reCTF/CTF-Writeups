🕵️ OSINT Challenge Writeup: Glasses 2 | BlueHens CTF 2026

This writeup explores my approach to solving the "Glasses 2" OSINT challenge from BlueHens CTF 2026, breaking down the investigation journey, the clues analyzed, and the reasoning behind each step.


<img width="610" height="524" alt="Screenshot 2026-04-18 182924" src="https://github.com/user-attachments/assets/dec9d007-e5d9-4126-97b2-61a76cbc5717" />

-- The challenge revolves around the CTFd profile picture -- 🐔🕶️
The objective is to locate the same image without the glasses using OSINT techniques 🔍


<img width="400" height="484" alt="Screenshot 2026-04-20 000522" src="https://github.com/user-attachments/assets/346076cd-3ca3-4cc6-9d2e-250c16fa7b5c" />

I first tried submitting the image to multiple reverse image search engines such as
Google Images and Yandex, but I was unable to find any useful or matching results.



<img width="1481" height="822" alt="Screenshot 2026-04-20 001107" src="https://github.com/user-attachments/assets/e74c4131-4383-435a-86e8-980df1aa771f" />


I also tried using additional reverse image search tools commonly used in OSINT investigations, such as TinEye and similar platforms, to gather more possible matches and trace the source of the image, but I still didn’t manage to find anything useful or relevant.


<img width="1072" height="862" alt="Screenshot 2026-04-20 001528" src="https://github.com/user-attachments/assets/b32e229a-bec5-4450-9ee5-a411b8cfbd8c" />


-- After an in-depth investigation, an idea came to me -- 💡

<img width="500" height="270" alt="aniyuki-gif-naruto-77" src="https://github.com/user-attachments/assets/6fea73f6-8e47-4ce5-be39-3fb658bf2f52" />


I decided to send the image to an AI tool and ask it to remove the glasses from the chicken 🐔➡️
This was done to make the search process easier and improve matching accuracy.



<img width="602" height="697" alt="Screenshot 2026-04-20 001953" src="https://github.com/user-attachments/assets/17901ee3-5238-4e00-9692-e88867480213" />



I then uploaded the modified image to TinEye, and it successfully returned relevant matches 📌

<img width="1203" height="831" alt="Screenshot 2026-04-18 184123" src="https://github.com/user-attachments/assets/33bbd1b2-ab35-4f96-adc3-16ce542c8e2f" />


Then, when clicking on the result, I noticed that the image had been removed from the original source. However, I only needed the direct image URL, which was still accessible — and this was exactly what I was originally looking for.


<img width="1296" height="513" alt="Screenshot 2026-04-18 184327" src="https://github.com/user-attachments/assets/35b9522f-9a07-4811-bf31-fc307343def5" />



🏁 Flag : UDCTF{https://www.flickr.com/photos/24049533@N00/4560602175}

<img width="599" height="592" alt="Screenshot 2026-04-18 185612" src="https://github.com/user-attachments/assets/cef45971-f76f-43a6-acad-ef57554f9260" />




