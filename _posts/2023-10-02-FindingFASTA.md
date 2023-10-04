---
layout: post
title:  Finding FASTA is my favorite game
categories: [fasta, protein knowledgebases, NCBI datasets, UniProtKB, Ensembl]
excerpt: I like playing the does-this-have-an-annotated-genome game, but I rarely document it. So here you go, me finding a black widow.
---

This is going to be super messy and fraught with typos, but here you go. I like playing the "does this have an annotated genome" game, but I rarely document it. So I am going to use a recent one as a demo. Colleague has a black widow that I have been encouraging proteomic analysis on. Problem, how do they search the mass spec data? [This older talk](https://zenodo.org/record/3687161) goes into how I did this way back when with the CA sea lion, and specifically how using related species annotations can work, albeit not great, and also how do you know if it is good? But for right now, I just want to show how and where I look for the genome annotation (feel free to check the first post on this blog about databases)

&nbsp; 

**Start - Does it have an annotation?**
I am not talking about, "does it have sequences on UniProt, but does this species specifically have a complete genome that has been annotated. So I started at [NCBI datasets](https://www.ncbi.nlm.nih.gov/datasets/) and didn't see any annotations of Latrodectus spp. (6923) specifically. (likewise no annotation on UniProt, which isn't surprising since rarely if ever is something not on NCBI but is on UniProt, which is why I start at NCBI).

&nbsp;

**What related species are annotated?**

Sticking on NCBI datasets cause you can browse the taxonomy and then filter the table by annotations (not just assemblies). Within Araneae (6893) there are something like 15 spider genome annotations [https://www.ncbi.nlm.nih.gov/datasets/genome/?taxon=6893&annotated_only=true&refseq_annotation=true&genbank_annotation=true](https://www.ncbi.nlm.nih.gov/datasets/genome/?taxon=6893&annotated_only=true&refseq_annotation=true&genbank_annotation=true)

But, the Lactrodectus spp. (aka black widows) are under Theridiidae (34643; aka cobweb weavers), and so of that list of 15 annotations, only one cobweb weaver is annotated, [the house spider](https://www.ncbi.nlm.nih.gov/datasets/taxonomy/114398/).

&nbsp;
For comparison to NCBI, let's check out UniProt. Unsurpringly, it is kind of a mess imo since [this one spider annotation](https://www.uniprot.org/proteomes/UP000499080) is like 250k entries (super sus), and drives the appearance of tons of spider sequences. Note, this is why I focus on species with proteomes, not how many sequences per taxon  ID.

Specific UniProt links down the taxonomic tree:
[Spider Order](https://www.uniprot.org/taxonomy/6893
[Cobweb Family](https://www.uniprot.org/taxonomy/34643)
[Blackwidow genus](https://www.uniprot.org/taxonomy/6923)

A typical method is that at this point you can either take the uniprot (sp + tr) and search the data three times (1. all entries in genus, 2. family, and then 3. order to see your "best" search space). Alternatively, do a series of searches against NCBI house spider (114398), then all 15 spider annotations. Now this last part is a PITA since you would need to download and then concatonate. 

&nbsp; 

**But what is different about spider sequences on NCBI v UniProt?**
There are more "complete" proteomes in NCBI (the 15 mentioned above) than is underlying Araneae in UniProt, which [looks more like 7 species only](https://www.uniprot.org/proteomes?query=Araneae+AND+%28taxonomy_id%3A6893%29) and then a bunch of rando sequences, which jives with what I normally see in that UniProt is easier to use, but is lagging behind in most proteomes. So UniProt Araneae has half the spider species actually available.

&nbsp; 

**But what about the genome assemblies? or other non-NCBI annotations?** 

There *are* [three black widow genomes unannotated on NCBI](https://www.ncbi.nlm.nih.gov/datasets/genome/?taxon=6923). You could annotate these with TOGA or MAKER, which do appear to be about 1.5 Gb. Based on the dates here, these are not [this older 15K pilot project](https://www.hgsc.bcm.edu/arthropods/western-black-widow-spider-genome-project) at Baylor (anything <2015 would be sus due to sequencing tech changes).

When I am looking for mammals I will also look over on DNA Zoo (as their in-house MAKER run on assemblies works very well in my hands), or other things like [this random TOGA repository](https://genome.senckenberg.de/download/TOGA/human_hg38_reference/). 

You can sometimes find annotations in papers, but imo, this is gamble. Why people insist on making their own annotations and hiding them in weird places is beyond me. NCBI RefSeq or UniProt will annotate and distribute better than you ever could. Still, here is a cool [2022 paper](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC9154082/) that insists there are black widow genome annotations somewhere. I got tired just looking around. **Come back to this and find the actual fasta... it is another treasure hunt and should still be compared to the other available species with regards to performance on the data.**

And lastly, what about ole [Ensembl](https://useast.ensembl.org/index.html)? This is what mostly feeds into UniProt, but also crossrefs with NCBI. The oldness of many non-model genomes is troubling to say the least (yep, there is a 2012 dolphin genome assembly). That said, using [Ensembl's new rapid release portal](https://rapid.ensembl.org/info/about/species.html) isn't as soul crushing, and will occasionally provide something not on NCBI. Spoiler, those spider genome assemblies aren't showing up here either.

&nbsp;  
&nbsp;  
&nbsp;  
