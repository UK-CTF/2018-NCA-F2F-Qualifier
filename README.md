# 2018 NCA F2F Quals "Operation Cyberstrike" | Write-up
**by sYNC @ UK-CTF** | UK-based CTF community


**Website:**  [uk-ctf.org](http://uk-ctf.org)\
**Discord:**  [discord.uk-ctf.org](http://discord.uk-ctf.org)

---

## Introduction

>The 2018 NCA F2F Qualifier is an event hosted by Cyber Security Challenge (CSC) UK. CSC hosts various CTF events sponsored by companies
with a variety of skill levels. For the 2018 NCA F2F event, we see a forensics-based theme which contains the analysis of a suspect's
hardware, and the retrieval of open source information (OSI). The qualifier is a typical CSC-style quiz, whereby participants have to
select the correct choice based off the information gathering stage.

>**Website:** [https://www.cybersecuritychallenge.org.uk/](https://www.cybersecuritychallenge.org.uk/)

## Prerequisites

A basic understanding of computer forensics is required, and the familiarity of related forensic tools are needed to extract information.
For this write-up, we will be using the following tools:

Tool | Type | Link
--- | --- | ---
FTK Imager 3.4.3 | Software (Free) | [https://accessdata.com/product-download/ftk-imager-version-3.4.3](https://accessdata.com/product-download/ftk-imager-version-3.4.3)
HxD 2.0 | Software (Free) |  [https://mh-nexus.de/en/hxd/](https://mh-nexus.de/en/hxd/)
Get-metadata | Online website | [https://www.get-metadata.com](https://www.get-metadata.com)
GPS-coordinates | Online website | [https://www.gps-coordinates.net](https://www.gps-coordinates.net)

A facebook account will also be needed when looking at the suspect's social media profile.

# Task

Firstly, we are given Op_CyberStrike.E01 to download, MD5 hash is then confirmed so we can start loading the file into FTK Imager and examine the contents. The imaged drive appears to be a USB pen-drive or similar removable media, and has NTFS file allocation. The highest level in folder hierarcy shows [orphan], [unallocated space] (two common system folders found within NTFS structures) and **[root]**, where typically data will be stored.

## Examining [root] folder


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

## Examining **Homework** folder


Highest level of this folder contains 2 files; a image file named **NCA-logo.jpg**, and a compressed file named **Sample Menu.7z**. Also within this folder is a subfolder named **Maths**.

### NCA-logo.jpg Analysis

### Sample Menu.7z Analysis












