
This writeup explores my approach to solving the "Glasses 2" OSINT challenge from BlueHens CTF 2026
, breaking down the investigation journey, the clues analyzed, and the reasoning behind each step.


<img width="610" height="524" alt="Screenshot 2026-04-18 182924" src="https://github.com/user-attachments/assets/dec9d007-e5d9-4126-97b2-61a76cbc5717" />

This challenge revolves around the CTFd profile picture is an image of a chicken wearing glasses.
The objective of this challenge is to locate the same image without the glasses using OSINT techniques

<img width="712" height="854" alt="Screenshot 2026-04-18 183436" src="https://github.com/user-attachments/assets/553a208c-2536-43ce-94d3-650f9e11b994" />

I first tried submitting the image to multiple reverse image search engines such as
Google Images and Yandex, but I was unable to find any useful or matching results.


Then, after some consideration, I decided to use an AI tool to edit the image by
removing the glasses from the chicken, making it easier to perform further searches.

<img width="747" height="868" alt="Screenshot 2026-04-18 183821" src="https://github.com/user-attachments/assets/e60dc06c-dbe9-4d23-a3e0-ac9b0606a5eb" />

Then I uploaded the modified image to TinEye, and it successfully returned relevant matches that helped me continue the investigation.

<img width="1203" height="831" alt="Screenshot 2026-04-18 184123" src="https://github.com/user-attachments/assets/33bbd1b2-ab35-4f96-adc3-16ce542c8e2f" />

Then, when clicking on the result, I noticed that the image had been removed from the original source. However, I only needed the direct image URL, 
which was still accessible and useful for continuing the search.

<img width="1296" height="513" alt="Screenshot 2026-04-18 184327" src="https://github.com/user-attachments/assets/35b9522f-9a07-4811-bf31-fc307343def5" />

so The Flag Is : UDCTF{https://www.flickr.com/photos/24049533@N00/4560602175}

<img width="599" height="592" alt="Screenshot 2026-04-18 185612" src="https://github.com/user-attachments/assets/cef45971-f76f-43a6-acad-ef57554f9260" />








