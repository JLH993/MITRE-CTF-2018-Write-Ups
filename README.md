# MITRE-CTF-2018-Write-Ups

These are some write ups of the keys I acquired during the MITRE CTF team based event 2018. I will try to keep this as short and simple as possible while still including interesting bits I found along the way. My team was in the professional category (not a highschool/college student) so we were unable to compete from prizes. In the end we placed **28th out of 309** in the professional category so I am very pleased with the experience. This being my first CTF event I had doubts going into it and I am now only more interested in continuing these in the future. 
***





## Its All In The Past - Linux [100 Points]
##### Description:
*There is a flag stored in /flag.txt but only root can read it. Figure out how to get root access to read the flag.
To connect: ssh ctf@138.247.115.163*

##### Starting Point:
When you login, you are presented with an empty home dir and a hidden file (which I later found to me a little misleading) that is titled '.sudo_as_admin_successful'... Other than that there is not much to go off of. We know that we need to view the contents of the file called 'flags.txt' which is located in /. 

After digging around a bit, I decided to reference the title of the challenge again, *Its All In The Past*, which as you can imagine gives one a hint to view the history for the user, which looks like this:

```
ctf@178a4668f77e:~$ history
    1  vim myscript.sh
    2  vi myscript.sh
    3  sudo apt install vim-tiny
    4  sudo apt install update
    5  sudo apt update
    6  sudo apt install vim-tiny
    7  ls
    8  vi myscript.sh
    9  ./myscript.sh
   10  chmod +x myscript.sh
   11  vi myscript.sh
   12  ./myscript.sh
   13  vi myscript.sh
   14  ./myscript.sh
   15  vi myscript.sh
   16  ./myscript.sh
   17  vi myscript.sh
   18  ./myscript.sh
   19  vi myscript.sh
   20  ./myscript.sh
   21  ls
   22  cat myscript.sh
   23  sh ./myscript.sh
   24  vi myscript.sh
   25  ./myscript.sh
   26  vi myscript.sh
   27  ./myscript.sh
   28  vi myscript.sh
   29  ./myscript.sh
   30  vi myscript.sh
   31  ./myscript.sh
   32  bash -x ./myscript.sh
   33  rm myscript.sh
   34  sudo ./myscript.sh
   35  vi myscript.sh
   36  sufo ./myscript.sh
   37  tomatosoup
   38  sudo ./myscript.sh
   39  vi mycrypt.sh
   40  sudo ./myscript.sh
   41  vi mycrypt.sh
   42  sudo ./myscript.sh
   43  vi mycrypt.sh
   44  ./myscript.sh
   45  rm myscript.sh
   46  vi myscript.sh
   47  chmod +x myscript.sh
```


The answer is clearly shown here, there was a command mistype which led to a clear text password dump. I know, its humiliating, but we have all done it at one time. Since we have our password we can go ahead and grab that key now...

![alt text](https://i.imgur.com/P8l5B8L.png "Key")






## How do I exit vim? - Linux [150 Points]
##### Description:
*Ive opened vim and cant exit! Can you help me?
To connect: ssh ctf@138.247.115.162*

##### Starting Point:
When you ssh into the server, you are stuck in an open vi instance. If you close the session, you close your connection to the server. The goal here is to break free from your session and gain access to a useable shell. 

My starting point was to view the '/etc/passwd' file to see what our default shell was set to so we could confirm that this was the case, which was pretty obvious to begin with. (You can browse the filesystem within vi by using ':Xe /path/to/directory' and use ':e /path/to/filename' to actually open a file for editing should you have permissions to do so. 

![alt text](https://i.imgur.com/bB9oC11.png "/etc/passwd")

Having this information, I knew everything I needed to know to get out of vi and gain access to a shell instance. I edited my /home/ctf/.vimrc and added the following: ':set shell=/bin/bash'... Now were able to run the following to break free... ':sh' or alternatively ':! bash'.

We now have a shell, The hard part is over! Since there was no context given about where the flag was or what it was called, I just simply ran a recursive find for a file that contained the word 'flag'.

```
ctf@9f1098d20e47:~$ find /* -type f -name *flag*
/etc/super_secret_flag.txt
find: '/etc/ssl/private': Permission denied
/proc/sys/kernel/acpi_video_flags
/proc/sys/kernel/sched_domain/cpu0/domain0/flags
/proc/sys/kernel/sched_domain/cpu1/domain0/flags
find: '/proc/tty/driver': Permission denied
/proc/kpageflags
find: '/proc/1/task/1/fd': Permission denied
find: '/proc/1/task/1/fdinfo': Permission denied
find: '/proc/1/task/1/ns': Permission denied
find: '/proc/1/fd': Permission denied
find: '/proc/1/map_files': Permission denied
find: '/proc/1/fdinfo': Permission denied
find: '/proc/1/ns': Permission denied
find: '/proc/5/task/5/fd': Permission denied
find: '/proc/5/task/5/fdinfo': Permission denied
< redacted >
```

We found it! Thats all it took. 

```
ctf@9f1098d20e47:~$ cat /etc/super_secret_flag.txt
MCA{reiP8boagoozobu}
```






## Adverse Reaction - Web [100 Points]
##### Description:
*We see youre running an ad-blocker. To view "this content" consider opening yourself up to malware. You can also subscribe for $9.99/month and still receive ads!*

##### Starting Point:
When you go to the link (this content) you get redirected to their web page containing a few paragraphs of nothing and a pop-up that requires you to click out of it before you do anything. 

![alt text](https://i.imgur.com/U4wzxwd.png "Dank")

So I first think to go check in the cookies within chrome developer tools which led me to the key, this one was really simple and didn't take long to figure out. 

![alt text](https://i.imgur.com/ThfxsAf.png "Dank Cookies")

Yay! Now on to my favorite flag of the event that I got to work on. (We had a lot of fun with this one.)









## CTF Jams - Grab Bag [150 Points]
##### Description:
*The white cell loves to discover and share new music. Check out our new compilation album! We put a lot of work into the album art.*

##### Starting Point:
You are presented with a link to an .mp3 file stored on AWS which begins playing a mashup of songs that I can only describe as an audible meme. (This was amazing btw, link: https://s3.amazonaws.com/mitre-stem-ctf/gb15_e3b7421c5a8f4bf88521c0f53b7b07a15424bca4.mp3)

![alt-text](https://i.imgur.com/N3lt5I2.png "s3 mp3")

So to get started, I pull the mp3 file down to my kali system using wget, where I can begin to un-pack it. (At this point I am thinking there has definately got to be files hidden within the mp3 itself.) I decided to use [eyeD3](https://eyed3.readthedocs.io/en/latest/) to do this, which essentially just allows me to extract album art, track information, or any metadata that I might need here. I create a directory for any images I might be able to pull out of this and I go ahead and send it. `eyeD3 --write-images=./secret_images/ gb15_e3b7421c5a8f4bf88521c0f53b7b07a15424bca4.mp3`

![alt text](https://i.imgur.com/0TzFQ8H.png "Extraction")

![alt text](https://i.imgur.com/3eiYpA7.png "Extraction2")

Here is our ***wonderful*** image:

![alt text](https://i.imgur.com/DCYNCRb.png "Mountain Dew")

Cool, so I got what I needed out of this file, now what? I actually struggled with this one for a bit, After I opened the image, I didn't see anything obvious, so I started trying to use gocr to view bitpatterns, to view text, to get ANYTHING I can out of this thing. (or ting in this case.)

```
$ gocr -m 130 FRONT_COVER.png
 DB ./db/db.lst not found

# show box + environment
# show box     x=  320  471 d=  21  23 r= 7 1
# show pattern x=  295  469 d=  71  27 t= 1 1
OOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOO,O,,,,,OOOOOOO,,,,,OOOO,
OOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOO,OOO,,,,OOOOOO,O,,,OOOO,
OOOOOOOOOOOOOOOOOOOOOOOOO#####################O,,,OOO,,OOOOOO,OOO,,OO,O -
OOOOOOOOOOOOOOOOOOOOOOOOO########.......######OOO,,,OOO,OOOOOO,,OO,,,,O
OOOOOOOOOOOOOOOOOOOOOOOOO####.###........#####O,OO,,,OO,,OOOO,,,,,OOO,,
OOOOOOOOOOOOOOOOOOOOOOOOO####..###........####O,,O,,O,,,,OOOO,,,O,,OOOO
OOOOOOOOOOOOOOOOOOOOOOOOO#.###............####OO,,,,,,,,,,OOO,,,OO,,,,,
OOOOOOOOOOOOOOOOOOOOOOOOO#...#.............###,OOO,O,,,O,,,OO,,,,OO,O,,
OOOOOOOOOOOOOOOOOOOOOOOOO#.#.....##........###,,,O,O,,,OOO,OOOOO,,,,O,,
OOOOOOOOOOOOOOOOOOOOOOOOO#.###..####....##..##,O,,,,,,,,,OO,OOO,OO,,,OO
OOOOOOOOOOOOOOOOOOOOOOOOO##..##.#####...###.##,OOO,,,,,O,,,,,OO,,OO,,OO
OOOOOOOOOOOOOOOOOOOOOOOOO##...#.#######.....##,,,OO,,,,OOO,,,OOO,,,O,OO
OOOOOOOOOOOOOOOOOOOOOOOOO##.#....######......#,,,,O,,,,,,OOO,,OOOO,,,OO
OOOOOOOOOOOOOOOOOOOOOOOOO##.###..######......#,OOO,,,,,,,,OOO,,O,OOO,,O
OOOOOOOOOOOOOOOOOOOOOOOOO###..##.######......#,,O,,,,,OOO,,,OO,O,,,,,,O
OOOOOOOOOOOOOOOOOOOOOOOOO###.....######......#O,,,,OOOOOOO,,,,,,OO,OOOO
OOOOOOOOOOOOOOOOOOOOOOOOO###.##...#####......#OOO,,OOOOOOOO,,,,OOOOOOOO
OOOOOOOOOOOOOOOOOOOOOOOOO####.##..####......##,,O,,OOOOOOOOOOOOOOOOOOOO
OOOOOOOOOOOOOOOOOOOOOOOOO####..##.###.###...##O,,,,OOOOOOOOOOOOOOOOOOOO
OOOOOOOOOOOOOOOOOOOOOOOOO####.......#..##..###,,,,,OOOOOOOOOOOOOOOOOOOO
OOOOOOOOOOOOOOOOOOOOOOOOO####.##...###....####OOOOOOOOOOOOOOOOOOOOOOOOO
OOOOOOOOOOOOOOOOOOOOOOOOO#####.###.......#####OOOOOOOOOOOOOOOOOOOOOOOOO
OOOOOOOOOOOOOOOOOOOOOOOOO#####.........#######OOOOOOOOOOOOOOOOOOOOOOOOO
OOOOOOOOOOOOOOOOOOOOOOOOO#####......##########OOOOOOOOOOOOOOOOOOOOOOOOO
OOOOOOOOOOOOOOOOOOOOOOOOO#####################OOOOOOOOOOOOOOOOOOOOOOOOO -
OOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOO
OOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOO
The above pattern was not recognized.
Enter UTF8 char or string for above pattern. Leave empty if unsure.
Press RET at the end (ALT+RET to store into RAM only) :

# show box + environment
# show box     x=  334  234 d=  32  48 r= 7 0
# show pattern x=  319  232 d=  62  52 t= 1 2
,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,OOOOOOOO
,,,,,,,,,,,,,,,.....###..............##########OOOOOOOOOOOOOOO -
,,,,,,,,,,,,,,,...####...............##########OOOOOOOOOOOOOOO
,,,,,,,,,,,,,,,..#####................#########OOOOOO,,OOOOOOO
,,,,,,,,,,,,,,,.#####..........................,,,,,,,,OOOOOOO
,,,,,,,,,,,,,,,######..........................,,,,,,,,,,OOOOO
,,,,,,,,,,,,,,,######..........................,,,,,,,,,OOOOOO
,,,,,,,,,,,,,,,######..........................,,,,,,,OOOOOOOO
,,,,,,,,,,,,,,,.#####..........................,,OOOOOOOOOOOOO
,,,,,,,,,,,,,,,.######.......................##OOOOOOOOOOOOOOO
,,,,,,,,,,,,,,,..#####.......................##OOOOOOOOOOOOOOO
,,,,,,,,,,,,,,,...#####......................##OOOOOOOOOOOOOOO
,,,,,,,,,,,,,,,....#####.......................OOOOOOOOOOOOOOO
,,,,,,,,,,,,,,,.....#####......................,,,,,,,,,,,,,OO
,,,,,,,,,,OO,,,......#####.....................,,,,,,,,,,,,,,,
,,,,,,,,,OOOO,,........#####...................,,,,,,,,,,,,,,,
,,,,,,,,,,OOOO,.........######.................,,,,,,,,,,,,,,,
O,,,,,,,,,,OOOO..........#######...............,,,,,,,,,,,,,,,
OO,,,,,,,,,,OOO##..........#######.............,,,,,,,,,,,,,,,
OOO,,,,,,,,,,,O###...........#######...........,,,,,,,,,,,,,,,
OOOOO,,,,,,,,,,####...........########.........,,,,,,,,,,,,,,,
OOOOOO,,,,,,,,,.#####...........########.......,,,,,,,,,,,,,,,
,,OOOOOO,,,,,,,...####............########.....,,,,,,,,,,,,,,,
,,,,OO,,,,,,,,,.....................########...,,,,,,,,,,,,,,,
,,,,,,,,,,,,,,,........................########,,,,,,,,,,,,,,,
,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,
The above pattern was not recognized.
Enter UTF8 char or string for above pattern. Leave empty if unsure.
Press RET at the end (ALT+RET to store into RAM only) :

# show box + environment
# show box     x=  720  418 d=  43  33 r= 5 1
# list box char:  g(76)
# show pattern x=  710  416 d=  63  37 t= 1 1
OOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOO
OOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOO
OOOOOOOOOO###########################################OOOOOOOOOO -
OOOOOOOOOO######...##################################OOOOOOOOOO
OOOOOOOOOO#####...........###########################OOOOOOOOOO
OOOOOOOOOO####...............########################OOOOOOOOOO
OOOOOOOOOO####.................######################OOOOOOOOOO
OOOOOOOOOO####....####..........######........#######OOOOOOOOOO
OOOOOOOOOO###....#########.......####..........######OOOOOOOOOO
OOOOOOOOOO###....###########....................#####OOOOOOOOOO
OOOOOOOOOO###....####...#####....................####OOOOO,,,,,
OOOOOOOOOO###...####......####..........####.....####OOOO,,,,,,
OOOOOOOOOO###...####.......####........######....####OOOO,,,,,,
OOOOOOOOOO###...###........####........#######....###OOOO,,,OO,
OOOOOOOOOO##....###.........###.......###.####....###OOO,,,,OOO
OOOOOOOOOO##....###.........###.......###..###....###OOO,,,,OOO
OOOOOOOOOO##....###.........####.....####..###....###OOOO,,,,,,
OOOOOOOOOO##....###.........####.....###...###....###OOOOO,,,,,
OOOOOOOOOO##...####.........###.....###....####...###OOOOOO,,,,
OOOOOOOOOO##...####.........###.....###....####....##OOOOOOO,,,
OOOOOOOOOO##...###.........####....###......###....##OOOOOOOO,,
OOOOOOOOOO#....###.........####....###......###....##OOOOOOOO,,
OOOOOOOOOO#....###........####....######...####....##OOOOOOOO,,
OOOOOOOOOO#....####......#####....#############....##OOOOOOOO,,
OOOOOOOOOO#...###############....###############....#OOOOOOOO,,
OOOOOOOOOO#...##############....#####.....######....#OOOOOOOO,,
OOOOOOOOOO#.......#######.......####........####....#OOOOOOO,,,
OOOOOOOOOO##....................###..........###....#OOOOOOO,,,
OOOOOOOOOO####.................###...........####...#OOOOOOO,,,
OOOOOOOOOO#########...................###....####...#OOOOOOO,,,
OOOOOOOOOO###################........#####....##....#OOOOOOO,,,
OOOOOOOOOO######################...#######..........#OOOOOOO,,,
OOOOOOOOOO#################################........##OOOOOOO,,,
OOOOOOOOOO####################################....###OOOOOOO,,,
OOOOOOOOOO###########################################OOOOOOO,,, -
OOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOO,,
OOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOO
The above pattern was not recognized.
Enter UTF8 char or string for above pattern. Leave empty if unsure.
Press RET at the end (ALT+RET to store into RAM only) :
```

I get to a point where I think I am onto something, and I am SO close but the text is not quite where it needs to be. Below I am just using gocr to read input from our .png file... (Keep in mind the format for a key is, MCA(xxxxxx_xxxx_xxxxxxx_xxx_xxxxxx))

```
$ gocr -i FRONT_COVER.png
_   ___0_   _  __  0_  _____(lJR_c_9__o_
_AT'S_HAT
N_  mcA(sangname7 _he nng goes v_ras  4ll
_ mEmESVoL
1      __f+_
_ 9       l___  ',  ,_ _??_??^_,,,
__   '   f_     '  __^-__^'_'v_
_\               _^_-


_ __
__ _ 0 _,   :   _  f Eg_  _,
0        JN_
__?_e   __      t    _   _
___,,_J__  0        __
SKR44
/
/
```

Finally, after discussing with a friend we decide to convert the image to a .jpg using GIMP to get rid of the transparent background that we have in the current .png image. Once we do this, it reveals our flag.

![alt-text](https://i.imgur.com/C0fOxJS.png "Dank MEMES")



