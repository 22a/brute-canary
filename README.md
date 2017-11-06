# Brute Canary

A pure rust [burstcoin](https://www.burst-coin.org/) wallet private-key random-brute-forcing program. An attempt at making a speedy test of burstcoin's [`canary` early warning system](https://forums.burst-team.us/topic/4766/the-canary-burst-early-warning-system).

## Premise

> The auto-generated random passphrase of 12 Words out of a 1626 Words dictionary is considered by many as not safe enough. Let's see if these doubts are appropriate or not.

> That's why I set up this little game and early warning system. Each Account has funds in it and uses an auto generated passphrase by the Burst Wallet with 12 and less words. All words in the used dictionary are openly available: https://github.com/burst-team/burstcoin/blob/master/html/ui/js/crypto/passphrasegenerator.js#L29

The 12 test wallets are:
```
1 Word   BURST-GMVF-Z5L4-LGWZ-8BW6W (entropy of 10.66 bits)
2 Words	 BURST-ADFP-EN99-24FD-44QA7 (entropy of 21.334 bits)
3 Words	 BURST-UP6D-R28A-67XL-DYBJL (entropy of 32 bits)
4 Words	 BURST-24M6-5CWP-CPPZ-D9PVS (entropy of 42.67 bits)
5 Words	 BURST-RT5Y-YLDA-AZ5R-6T6MN (entropy of 53.34 bits)
6 Words	 BURST-AYQF-7YUJ-A88H-D32VQ (entropy of 64 bits)
7 Words	 BURST-ND84-WUE8-L9EZ-C27NB (entropy of 74.67 bits)
8 Words	 BURST-K8GV-VEAA-LRLS-F5C58 (entropy of 85.33 bits)
9 Words	 BURST-YRZY-WDWF-XQ35-AURDX (entropy of 96 bits)
10 Words BURST-RGGF-SJ88-272C-G3RXG (entropy of 106.67 bits)
11 Words BURST-P7SA-89F9-62F4-53MEG (entropy of 117.34 bits)
12 Words BURST-6L9L-LULB-XVAZ-463RB (entropy of 128 bits)
```

which have the following public keys(respectively):
```
59dd8b0472c2f634f7bfbf6082e82a0aad59840340beea37a0d40a00a3502241
873b1b266ed4bb08f37dc2741ed4a8c021f45de1bde57397dcc3f9171cb68f69
1cdfe1e44baffc8416dc88ff9e182be5316aef49b0d888f03059d41521c1dd68
79846519098b7eed2516de591251bcc5a2b4a78be61e58ec3b011309e6459e48
600c3133f9d40786a7df83be279db7f2a9478f94edc4a4b1d6b8f78438a45b30
0f8759d21cffaa4631bd41738d6da44700e35f469b7fd38d99f3d3e230e0e700
155a83cb7fd3584fb9d09c7e6a468ba78c94b2ffffaf6f793a9c769c714d272a
c93e749961389ef25c7b204212762ead069e50c5be8e940c88f2a5b040b18b78
05225bb64286ffc70701f1bbf8fc5ce8d282ab50c8d7b5e96116f09276116325
8ffe3298e186ce7d329208ca522081785fcac59ff1852cf6aee3243284581547
33b7f0b03ea5c0d08a5f9b50158b15b0cddd090341ccdf75ea2849f335c6cf55
72d693d0e3eb17ab484cbb68e40edb9cb19c0e3e04a684b6325a479908451948
```

Essentially the idea here is to squeeze as many guesses per second out of your hardware and get lucky by finding the right word combinations.

This is more of an excuse for me to mess with rust than a serious attempt at stealing some cryptocoinspheres, the rewards are diminishingly small for a successful hit and the difficulty increases exponentially with each added word, but why not give it a shot.

## Example

So, for example, the first wallet's passphrase (`princess`) turns into `59dd8b0472c2f634f7bfbf6082e82a0aad59840340beea37a0d40a00a3502241` after hash + salt.

![wallet 1 + 2 priv keys](https://user-images.githubusercontent.com/7144173/32420472-af5e62b6-c282-11e7-9b83-2ae00917ca90.png)

## This attempt

I've never written any rust before and I barely understand it so this is probably terribly inefficient.
Nonetheless, 

#### Single threaded
* **0.1.0:** I forget, perhaps ~1k guesses/sec

#### Multi threaded (4 logical cores, base 13" mbp early 2015 {2.7GHz i5})
* **0.2.0:** ~6k guesses/sec
* **0.3.0:** ~9.5k guesses/sec
* **0.3.1:** ~14.8k guesses/sec

#### Multi threaded (32 logical cores, digital ocean big box^)
^ don't worry I only provisioned this box for an hour
* **0.3.1:** ~174.7k guesses/sec
<img width="200" alt="big box" src="https://user-images.githubusercontent.com/7144173/32420803-5e91e780-c288-11e7-8375-a377a6d3dc5e.png">

## Room for improvement
Plenty.

* The [ring crate](https://github.com/briansmith/ring) looks like it should be quicker than the one I'm currently using, assembly etc, it's also being actively maintained which is probably good, I just didn't have time to go making the curve25519 methods inside it public
* Perhaps the random word selection could be made slightly less random to make better use of cache locality 
* Perhaps the crossbeam scope could be eliminated, something tells me there's some overhead involved in it
* Something that periodically outputs the rate of guesses/sec
