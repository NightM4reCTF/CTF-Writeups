Name: The hungry ?Catipillar? | BlueHens CTF 2026 Writeup

Solved by: 0xMeshGh4rib

Hello folks, now i'm going to explain how i solved `The hungry ?Catipillar?` OSINT challenge where we are given an image and asked to determine the exact location using **what3words**.

<img width="548" height="524" alt="01" src="https://github.com/user-attachments/assets/235f13d4-d1cd-427d-bbdf-46f76f1ef061" />

Goal: Identify the location shown in the image and convert it into a **what3words** address.

<img width="382" height="219" alt="img" src="https://github.com/user-attachments/assets/80f756af-7067-4b4f-b5db-3399b0c1c2a3" />

Tools Used:
- Google Reverse Image Search
- Google Maps
- what3words

> [!NOTE] What is the **what3words.com**?
> **what3words** is a website/app that turns any location on Earth into **3 simple words**.

I started by performing a reverse image search using Google.

<img width="1464" height="706" alt="02" src="https://github.com/user-attachments/assets/6b613110-782f-4be7-bf25-b1a7f55c88fd" />

We will find the extracted info from the google AI which it is a **hair salon** located at **271 US-6, Mahopac, NY 10541**. 
And we will find this resource: https://www.fresha.com/lvp/haven-hair-studio-2-u-s-6-mahopac-wrRjyX
by navigating to this website, we will find the location of this hair salon on google maps.

<img width="1584" height="752" alt="03" src="https://github.com/user-attachments/assets/315d5911-bdf3-490b-9e25-a7ad3060d26e" />

But is the **hair salon** is the intended and right place?

<img width="500" height="345" alt="thinking" src="https://github.com/user-attachments/assets/bf7026b9-f1c6-4f2a-b87c-56e628b7df24" />

<img width="1466" height="746" alt="04" src="https://github.com/user-attachments/assets/532913cb-cad2-4158-b4b3-e592fc5dc22b" />

After checking the location on Google Maps and browsing available images, I confirmed that the building matches the challenge image.

<img width="1519" height="751" alt="05" src="https://github.com/user-attachments/assets/e20d0ed8-792e-476b-b9f3-e0e757f24107" />

The challenge name **"hungry Caterpillar"** suggested that the target might be a restaurant in the same building.
So instead of stopping at the hair salon, I looked for nearby restaurants in the same building.

There is a restaurant called **The Blue Hen**. Hmmmmm, it's the same name as this CTF competition.
Nice, i think we found it.

<img width="1446" height="752" alt="06" src="https://github.com/user-attachments/assets/1de01e57-2f4a-40ea-a5f4-7b6eebed67f8" />

From Google Maps, I obtained the coordinates of the restaurant:

```
41.35329961840348, -73.74763825830397
```

<img width="1287" height="749" alt="07" src="https://github.com/user-attachments/assets/ac68758a-382f-44d4-aca2-a8bb1ea1d2ab" />

Using the coordinates on what3words, I got:
```
thought.promising.cavalier
```

<img width="1286" height="746" alt="08" src="https://github.com/user-attachments/assets/138d55f8-a09c-4b35-8060-768a5771e2ce" />

The final flag will be:

```
UDCTF{thought.promising.cavalier}
```

