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

#### Challenge

> If you've never played a Capture-The-Flag competition before, worry not!

> The aim of the game is to find a string of text called a flag

> Usually the flag will be in the form `HackTrinity{}`, but sometimes it can be a different piece of text

> To receive a free 50 points, enter `HackTrinity{well_that_was_a_freebie}` in the box below, and click Check.

#### Solution

Simple enough. Free points.

## RTFR

Topic(s) | Points | Difficulty (in my opinion)
---------|--------|-----------
`intro` | `75` | Very Easy

#### Challenge

> Oooh, we Trinity students love our rules and procedures.

> We love them so much we included a flag in the rules for this competition. Can you find it?

#### Solution

Another simple one - no need for an explanation here.

## Say no to phishing

Topic(s) | Points | Difficulty (in my opinion)
---------|--------|-----------
`misc` | `77` | Very Easy

> What's our real Twitter handle?

#### Solution

The last of the "zero-effort" problems, kind of.

##  Denovo 1

Topic(s) | Points | Difficulty (in my opinion)
---------|--------|-----------
`reversing` | `100` | Easy

#### Solution

> Denovo is a new startup which develops DRM for games.

> Version 1 is to be released soon, and you've been called in to test its effectiveness...

> Note: Denovo have decided that serial keys for v1 and all future versions look like "XXXXXX-XXXXXX-XXXXXX-XXXXXX-XXXXXX", where 'X' is a capital letter.

> Both binaries below are generated from the same code, one is built for ARM platforms and one for x86-64. You can choose whichever you're most comfortable with :)

#### Challenge

On to the real problems. I chose the x86-64 binary because I can execute it on my machine.

I quickly executed it to see how the program worked.

// img here

I then extracted all of the relatively long strings from the binary using this command:

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
`forensice` | `100` | Medium

#### Challenge

> TCD Disinformation Services have been hard at work making sure that any flag-containing media is fully censored.

> Apparently one slipped through... Can you find it?

#### Solution

The reason I've given this challenge a difficulty of 'medium' rather than 'easy' is because of the sheer amount of time I spent trying to use steganography and other techniques to search for the flag.

I extracted strings, looked through metadata, put it through an MKV decoder, ran a `binwalk` for hidden files, ran colour plane analysis using steganography tools, etc ...

Turns out the answer was right in front of me the whole time.

The title of the file is:

`...`

If we run it through a base64 decoder out pops our flag:

`...`

This challenge too me way longer than it should have.

## Book of Kells 1

Topic(s) | Points | Difficulty (in my opinion)
---------|--------|-----------
`darkweb/recon` | `113` | Very Easy

#### Challenge

> Oh noes! Someone's stole the Book of Kells and is auctioning it off on the Dark Web!

> Download the Tor Browser and visit the auction site at http://uxpkyayer253dtcl.onion to find out who's been making bids on this Trinity treasure :O

#### Solution

This was a simple one. If we visit the hidden service and go to the only thread on the forum we'll find the flag sitting at the bottom:

`img here`

## LUAS

Topic(s) | Points | Difficulty (in my opinion)
---------|--------|-----------
`blockchain/recon` | `114` | Easy

#### Challenge

 > The Luas website has been hacked again, and a threatening ransom message posted. Can you track down the culprit's real full name? https://luas.hacktrinity.me 

#### Solution

Visiting the website gives us this Zcash address: `t1gok64PM8APnrSWXgBxboqHrszFYqP5v3L`.

I looked it up on a [Zcash blockchain explorer](https://explorer.zcha.in/) and found an account with only one or two transactions. If we follow their transactions we'll find another account or two with only a handful of transactions. Eventually we reach a account that isn't entirely barren, if we Google their Zcash address we'll find a blog containing the culprit's full name - that's the flag!

## Trinity Ball 1

Topic(s) | Points | Difficulty (in my opinion)
---------|--------|-----------
`web` | `119` | Very Easy

#### Challenge

> Tickets for Trinity Ball have finally been released! Due to high demand, the organisers have limited tickets to one per person. Can you bypass the restrictions and checkout two tickets at once?

> https://trinityball1.hacktrinity.me

#### Solution

...