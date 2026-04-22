Name: The hungry ?Catipillar? | BlueHens CTF 2026 Writeup

Solved by: 0xMeshGh4rib

Hello folks, now i'm going to explain how i solved `The hungry ?Catipillar?` OSINT challenge where we are given an image and asked to determine the exact location using **what3words**.

![[01.png]]

Goal: Identify the location shown in the image and convert it into a **what3words** address.

![[img.png]]

Tools Used:
- Google Reverse Image Search
- Google Maps
- what3words

> [!NOTE] What is the **what3words.com**?
> **what3words** is a website/app that turns any location on Earth into **3 simple words**.

I started by performing a reverse image search using Google.

![[02.png]]

We will find the extracted info from the google AI which it is a **hair salon** located at **271 US-6, Mahopac, NY 10541**. 
And we will find this resource: https://www.fresha.com/lvp/haven-hair-studio-2-u-s-6-mahopac-wrRjyX
by navigating to this website, we will find the location of this hair salon on google maps.

![[03.png]]

But is the **hair salon** is the intended and right place?

![[thinking.gif]]

![[04.png]]

After checking the location on Google Maps and browsing available images, I confirmed that the building matches the challenge image.

![[05.png]]

The challenge name **"hungry Caterpillar"** suggested that the target might be a restaurant in the same building.
So instead of stopping at the hair salon, I looked for nearby restaurants in the same building.

There is a restaurant called **The Blue Hen**. Hmmmmm, it's the same name as this CTF competition.
Nice, i think we found it.

![[06.png]]

From Google Maps, I obtained the coordinates of the restaurant:

```
41.35329961840348, -73.74763825830397
```

![[07.png]]

Using the coordinates on what3words, I got:
```
thought.promising.cavalier
```

![[08.png]]

The final flag will be:

```
UDCTF{thought.promising.cavalier}
```

