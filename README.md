# 2018 NCA F2F Quals "Operation Cyberstrike" | Write-up
**by sYNC @ UK-CTF** | UK-based CTF community

**Published:** 23 August 2018

**Website:**  [uk-ctf.org](http://uk-ctf.org)\
**Discord:**  [discord.uk-ctf.org](http://discord.uk-ctf.org)

---

## Introduction

>The 2018 NCA F2F Qualifier is an event hosted by Cyber Security Challenge (CSC) UK. CSC hosts various CTF events sponsored by companies
with a variety of skill levels. For the 2018 NCA F2F event, we see a forensics-based theme which contains the analysis of a suspect's
hardware, and the retrieval of open source information (OSI). The qualifier is a typical CSC-style quiz, where participants have to
select the correct answer based off the information gathering stage.

>**Website:** [https://www.cybersecuritychallenge.org.uk/](https://www.cybersecuritychallenge.org.uk/)

## Prerequisites

A basic understanding of computer forensics is required, and familiarity of related forensic tools are needed to extract information.
For this write-up, we will be using the following tools:

Tool | Type | Link
--- | --- | ---
FTK Imager 3.4.3 | Software (Free) | [https://accessdata.com/product-download/ftk-imager-version-3.4.3](https://accessdata.com/product-download/ftk-imager-version-3.4.3)
HxD 2.0 | Software (Free) |  [https://mh-nexus.de/en/hxd/](https://mh-nexus.de/en/hxd/)
Get-metadata | Online website | [https://www.get-metadata.com](https://www.get-metadata.com)
GPS-coordinates | Online website | [https://www.gps-coordinates.net](https://www.gps-coordinates.net)

A facebook account will also be needed when looking at the suspect's social media profile.

# Task

Firstly, we are given Op_CyberStrike.E01 to download, the MD5 hash is then confirmed so we can start loading the file into FTK Imager and examine the contents. The imaged drive appears to be a USB pen-drive or similar removable media, and has NTFS file allocation. The highest level in folder hierarchy shows [orphan], [unallocated space] (two common system folders found within NTFS structures) and **[root]**, where user data will be typically stored.

## Examining the _[root]_ folder

Initially we can see **[root]** containing the system folder $extend (which we can ignore) and many system files beginning with the '$' prefix. These files are currently non-pertinent.

**[root]** also contains the folders **Homework**, **Pictures**, **Project** and **World Pictures**. In the same level, we also have the files:

- Hiding Place.jpg
- Img_2017212.jpg
- My_Boat.jpg.jpg
- New Text Document.txt
- Secret_Passwords_HIDEME.txt

We'll log any metadata relating to these image files, such as the Date Created property. The images can be processed further to gather any EXIF data they may contain by using Get-metadata. Any coordinates found can be used with GPS-coordinates to establish a location and logged. Text documents will be opened and metadata examined to see if any relevant information can be pulled.

### Hiding Place.jpg Analysis

**Date created:** 21/07/2010 10:10:10\
**GPS data:**     53° 24' 18.06" N, 2° 59' 48.25" W\
**GPS location:** Canada Blvd, Liverpool, L3, UK

### Img_2017212.jpg Analysis

**Date created:** 12/02/2017 13:03:00\
**GPS data:** n/a\
**GPS location:** n/a\
**Notes:** Image file shows a piece of paper with the string '!3aFC@7'. This may come in useful later.

### My_Boat.jpg.jpg Analysis

**Date created:** 21/07/2010 10:09:10\
**GPS data:**     53° 24' 26.52" N, 3° 0' 0.53" W\
**GPS location:** 8 Princes Parade, Liverpool, L3 1DL, UK\
**Metadata:**     Metadata analysis yields the image description 'Meet me in Cabin 203 at 15:15 hrs'. It also contains the subject 'BRINGS THE CASE'.

### New Text Document.txt Analysis

**Date created:** 23/07/2010 10:07:10\
**Notes:** Text file contains ASCII art of Pacman, and the words 'TRY LOOKING AT METADATA'. '203' can also be seen in the Pacman art, which relates to the metadata found in **My_Boat.jpg.jpg**.

### Secret_Passwords_HIDEME.txt Analysis

**Date created:** 09/07/2005 10:12:55\
**Notes:** Text file contains a series of strings on multiple lines, including a set of strings below the 'HIDDEN_PASSWORD' header. This may come in useful later.

## Examining the _Homework_ folder


Highest level of this folder contains 2 files; a image file named **NCA-logo.jpg**, and a compressed file named **Sample Menu.7z**. Also within this folder is a subfolder named **Maths**.

### NCA-logo.jpg Analysis

**Date created:** 21/01/2000 20:11:51\
**Notes:** Image file shows the logo of the UK National Crime Agency (NCA).

### Sample Menu.7z Analysis

This zip file contains a text document named **Very important stuff.txt**. However to unzip the file we require a password. On first attempt, using the string '!3aFC@7' worked, gathered from the file **Img_2017212.jpg**. The document **Very important stuff.txt** contains information about a bank account and safe deposit box located in Switzerland:

_546839-2874 SC: 576999\
Bank of Switzerland, Bern_

_320111287469-a\
Bank of Switzerland, Bern\
Safe deposit box 1981-01c_

### Customers.png Analysis

Opening this file as an image gives an error, so firstly we can examine the metadata to identify what type of file header **Customers.png** has. Examining the file using HxD, we can see that the header is of an SQLite database file. Therefore, we can simply rename the file type to '.sqlite3' and open with a database viewer (DB-Browser is used here). We are shown a table 'Stuff' which contains the columns 'Name', 'Tool' and 'Price'. The contents of the table shows the following:

Name | Tool | Price
--- | --- | ---
Paul | Stresser | 3000
Mick | RAT | 1000
Leanne | RAT | 750
Shoggzy | Rootkit | 335
Lynne | RDP_Backdoor | 3250

## Examining the _Pictures_ folder

This is the largest folder of the event so far. Due to the amount of images contained, we will log each and note down Date Creation data. Data for this will be left out of the write-up, however may be important for the quiz. Other files to note are another compressed file **Dog.7z**, and a document named **Instructions.docx**. Another file of interest is the image **Bare_Money.jpeg**.

### Dog.7z Analysis

This compressed file contains 12 jpg files, however once again requires a password. We will come back to this file once we feel we can attempt to solve the password.

### Instructions.docx Analysis

When opened, this document appears to have no text. However, when highlighting the document we can see that string 'Bring these in the case. Don't be late.' is found. An image contained can also be resized, showing a table with an assault rifle, two pistols and accompanying ammo.

### Bare_Money.jpeg Analysis

The image appears to be a stock image of a bitcoin, however when examined in FTK we can see the file has an alternative data stream pointing to a file named BTC. When opened in a text editor, BTC contains the string '1PZ5ebvdt43dvRRgRNgBhsq2PwAkN4X6W'. Since this looks like a BTC wallet address, we can search for this string in Bitcoin.info. From this, we find that the bitcoin address is for RNLI donations.

## Examining the _Project_ folder

This folder contains two files, **Mummy.jpg** and **Mummy.contact**. The image file will be logged, and we will examine further the contact file.

### Mummy.contact Analysis

When opened, this file contains the details of a contact:

**Nickname:** My Best Mummy\
**Given name:** Mummy\
**Phone number:** 555-2568\
**URL:** https://www.facebook.com/gary.kettlethorne.7

We can now use our open source intelligence to see if any meaningful information can be extracted. To note, when examining the **World Pictures** folder, it contains a dead shortcut link to a TOR browser application.

## Examining OSI

When entering the URL retrieved from Mummy.contact, we're given a facebook profile of a person named 'Gary KettleThorne'. Gary has one friend, 'Sarah Short' and one acquiantance in 'Banner Blackstone', who is a friend of Sarah. When inspecting Banner Blackstone’s profile, it appears to contain an image of a facebook post from Gary, alleging their infiltration into a system and stealing funds. The photo contained in the facebook post is found on the suspects computer under the file **bare money.jpg** in the **Dog.7z** compressed file.

We also find a mobile number posted by Gary (078912345678), and also find out that he has a dog named 'Skye' by looking at his personal information. On the first attempt, using 'Skye' as the password to unzip **Dog.7z** is unsucessful, however the string 'skye' is successful! The contents of the **Dog** folder isn't too interested, any scanning of strings in file metadata proved that there was no data hidden. However, useful information such as Date Creation and giving a general desciption of the image was collected.

# Conclusion

The quiz overall was quite tame in difficulty. Basic questions about NCA documentation and ACPO guidelines were given, as well as questions about metadata and hidden information found within the files. Overall the event was quite enjoyable, however some things could have been changed/included such as editing file headers (instead of simply changing the extension of the sqlite3 file). It would have also been nice to see some registry analysis instead of trawling solely through file metadata, however a USB pendrive's E01 size may be considerably smaller than a hard drive so we can see why CSC/NCA decided to go with this type of media. That being said it was a well assembled event and are looking forward to seeing other attendees in August!

**– sYNC**

**Difficulty:** 2/10\























