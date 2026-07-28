---
layout: post
title: "It is going good, until it isn't"
date: 2026-07-28
tags: [mascot, metaproteomics, fasta]
excerpt: "Alright, so I have been working on something for the last couple months, basically doing some analysis of these stool samples samples."
---

Alright, so I have been working on something for the last couple months, basically doing some analysis of these stool samples samples. Part of it is showing the "normal" [Minnesota two-step](https://doi.org/10.1002/pmic.201200352) in action (search everything then take peptide hits and their species, and search that subset search space in a second pass), but then also making an updated second step where I dump search results from first pass into [UniPept](https://unipept.ugent.be/mpa) (with confidence), then click on [Peptonizer](https://doi.org/10.1021/acs.jproteome.5c00567), and then export those results (btw, I tried doing this completely programmatically and the juice just wasn't worth the squeeze for 8 samples). Added bonus I even have a script take that and winnow the species down to things that "make sense". This is the difference between second pass searching 120 species or 428 species, via improved or classic two-step. ("makes sense" = if you search all species in human poop you are going to get hits to other primates. since I know it is a human and I don't think they were eating gorilla, I don't need gorilla to come along in second pass).

&nbsp;

Now if you are like me, you read this great paper from [Vande Moortele et al. ](https://www.biorxiv.org/content/10.64898/2026.02.24.707692v1.full) and realized the shrinking of the UniProtKB space to just whole genomes may not be great for metaP.

&nbsp;

Well, I got my hands on the 2026_01 TrEMBL, Sprot and varsplic and used it without species for my first pass (that TrEBML is like 100 gb). On my aging Mascot server this took 2 days for each file (more on that one day with antiSage).

&nbsp;

For the second pass, how do you use your identified taxonIDs to construct the fasta? Well, you can modify the taxonomy file in Mascot to do up to 140 (ish) taxon parsed out on the fly. I did this way back when ([2015 2D gel paper](https://doi.org/10.1371/journal.pone.0123295)) to make a Panda, Dog, Human, Pig, Cow, Mouse database to search sea lion data (just put 9646, 9615, 9606, 9823, 9913, 10090 in the taxonomy file). Worked great ([more discussion](https://zenodo.org/records/3687161) on searching back then), so I thought I could use it for my second pass here. Turns out I can only add 140 (or so) ideas to my version of Mascot, but I thought it'll work if under 140, right. Right?! And it did. Gave me results.

&nbsp;

I cranked on this for a few more weeks and was at the final stage and looking at my results... which no longer made sense. I had a ton of dietary proteins (peanuts and almonds and stuff) in my first pass but it was not only 7 species in my second pass. Finally realized that something was happening in Mascot not my code or post-processing. From what I can tell, it wasn't parsing the fasta on the fly, likely cause TrEMBL is 100 gb and I was asking for 100+ taxonIDs and that is too much to ask, but it never threw an error at me.

&nbsp;

So how do I do my two-step if I can't use that one trick. Well, just make your own fasta via UniProt rest.

&nbsp;

This is the [script](https://gist.github.com/neely/fb3aa9bb3107c4c4dfe9afe5f8ec48cd), which has the taxonIDs hard coded (and file paths), sorry. If you want to mod it, make it take in a txt file or something as an input arg. Note the higher level taxonIDs produce way more sequences and are handled differently so no time out.

&nbsp;

So I lost some weeks but am back on it to wrap this up.
