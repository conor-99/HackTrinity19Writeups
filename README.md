# Hack Trinity '19 Writeups

I had a lot of fun participating in the [Hack Trinity '19 CTF](https://hacktrinity.me/) competition this month. It was  my first time every trying anything like this so I wasn't at all prepared and I had to learn a lot of new skills on-the-fly. Nonetheless, I was fairly pleased with my result and I'll definitely be competing again next here!

Here are some *(brief)* writeups for the challenges I managed to solve:

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

## Welcome to Hack Trinity

Topic(s) | Points | Difficulty (in my opinion)
---------|--------|-----------
`intro` | `50` | Very Easy

#### Challenge:

	If you've never played a Capture-The-Flag competition before, worry not!
	The aim of the game is to find a string of text called a flag
	Usually the flag will be in the form `HackTrinity{}`, but sometimes it can be a different piece of text
	To receive a free 50 points, enter `HackTrinity{well_that_was_a_freebie}` in the box below, and click Check.

#### Solution:

Simple enough. Free points.

## RTFR

Topic(s) | Points | Difficulty (in my opinion)
---------|--------|-----------
`intro` | `75` | Very Easy

#### Challenge:

> Oooh, we Trinity students love our rules and procedures.

> We love them so much we included a flag in the rules for this competition. Can you find it?

#### Solution:

Another simple one - no need for an explanation here.

## Say no to phishing

Topic(s) | Points | Difficulty (in my opinion)
---------|--------|-----------
`misc` | `77` | Very Easy

#### Challenge:

> What's our real Twitter handle?

#### Solution:

The last of the "zero-effort" problems, kind of.

##  Denovo 1

Topic(s) | Points | Difficulty (in my opinion)
---------|--------|-----------
`reversing` | `100` | Easy

#### Challenge:

> Denovo is a new startup which develops DRM for games.

> Version 1 is to be released soon, and you've been called in to test its effectiveness...

> Note: Denovo have decided that serial keys for v1 and all future versions look like "XXXXXX-XXXXXX-XXXXXX-XXXXXX-XXXXXX", where 'X' is a capital letter.

> Both binaries below are generated from the same code, one is built for ARM platforms and one for x86-64. You can choose whichever you're most comfortable with :)

#### Solution:

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

	k = "\x??\x??\"
	o = ""
	for c in k:
		out += c << 1
	print o

This gives us `//here`. If we execute the program and enter this key we receive our flag:

`here`

## Bunny

Topic(s) | Points | Difficulty (in my opinion)
---------|--------|-----------
`forensics` | `100` | Medium

#### Challenge:

> TCD Disinformation Services have been hard at work making sure that any flag-containing media is fully censored.

> Apparently one slipped through... Can you find it?

#### Solution:

The reason I've given this challenge a difficulty of 'medium' rather than 'easy' is because of the sheer amount of time I spent trying to use steganography and other techniques to search for the flag.

I extracted strings, looked through metadata, put it through an MKV decoder, ran a `binwalk` for hidden files, ran colour plane analysis using steganography tools, etc ...

Turns out the answer was right in front of me the whole time.

The title of the file is:

`SGFja1RyaW5pdHl7MXRzX24wdF9qVXNUX2FiMHV0X3RoM192MWRlMH0K`

If we run it through a base64 decoder out pops our flag:

`HackTrinity{1ts_n0t_jUsT_ab0ut_th3_v1de0}`

This challenge took me way longer than it should have ...

## Book of Kells 1

Topic(s) | Points | Difficulty (in my opinion)
---------|--------|-----------
`darkweb/recon` | `113` | Very Easy

#### Challenge:

> Oh noes! Someone's stole the Book of Kells and is auctioning it off on the Dark Web!

> Download the Tor Browser and visit the auction site at http://uxpkyayer253dtcl.onion to find out who's been making bids on this Trinity treasure :O

#### Solution:

This was a simple one. If we visit the hidden service and go to the only thread on the forum we'll find the flag sitting at the bottom:

![Forum](images/03_bok_1.png)

## LUAS

Topic(s) | Points | Difficulty (in my opinion)
---------|--------|-----------
`blockchain/recon` | `114` | Easy

#### Challenge:

 > The Luas website has been hacked again, and a threatening ransom message posted. Can you track down the culprit's real full name? https://luas.hacktrinity.me 

#### Solution:

Visiting the website gives us this Zcash address: `t1gok64PM8APnrSWXgBxboqHrszFYqP5v3L`.

I looked it up on a [Zcash blockchain explorer](https://explorer.zcha.in/) and found an account with only one or two transactions. If we follow their transactions we'll find another account or two with only a handful of transactions. Eventually we reach a account that isn't entirely barren, if we Google their Zcash address we'll find a blog containing the culprit's full name:

`img ehre`

And that's our flag!

## Trinity Ball 1

Topic(s) | Points | Difficulty (in my opinion)
---------|--------|-----------
`web` | `119` | Very Easy

#### Challenge:

> Tickets for Trinity Ball have finally been released! Due to high demand, the organisers have limited tickets to one per person. Can you bypass the restrictions and checkout two tickets at once?

> https://trinityball1.hacktrinity.me

#### Solution:

If we visit the page and click the button we'll be taken to an order page:

![Order page](images/05_tb1_1.png)

We can add an item and then proceed to the checkout. We won't be given our flag, however.

By taking a look at the page's source code we can see that the following GET request is made when we purchase a ticket:

	fetch("addItemToBasket.php?id=" + id).then(function(res) {
		res.json().then(function(json) {
			refreshBasket();
		})
	});

If we try and make the following request `https://trinityball1.hacktrinity.me/addItemToBasket.php?id=2` then we'll be told our request is invalid. But if we replace the `2` with a `1` and make the same request multiple times then we'll be able to add as many tickets as we want to our basket.

If we proceed to the checkout we'll be awarded our flag:

`HackTrinity{client_side_is_best_side}`

## Welcome

Topic(s) | Points | Difficulty (in my opinion)
---------|--------|-----------
`networking` | `120` | Easy (although setting up can be tricky)

#### Challenge

> Each challenge has a dedicated OpenVPN server ... (instructions for setting up OpenVPN, etc) ... that challenge will be destroyed (and re-created next time you connect).

> Once you're all set up, this challenge should be fairly easy: you should be able to nmap your allocated subnet to find a web server hosting the flag.

#### Solution

If we install OpenVPN, download the config file and follow the setup instructions closely then we should be able to get connected easily enough.

We can find the IP we need to scan as well as our allocated subnet by running `ifconfig`:

`img here`

Using a [subnet calculator](http://www.subnet-calculator.com/) we can convert the `255.255.255.240` into `28` mask  bits.

Now we can run our scan using nmap `nmap -v -sn 10.0.0.0/28`:

`img here`

If we visit the resulting IP we'll find a web page containing our flag:

`flag`


## Dust in your eyes?

Topic(s) | Points | Difficulty (in my opinion)
---------|--------|-----------
`crypto/steganography` | `121` | Easy

#### Challenge

> Apparently the flag is in the file hidden.txt but I can't find it, good luck to you!
