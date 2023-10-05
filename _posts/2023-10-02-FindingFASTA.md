---
layout: post
title:  Finding FASTA is my favorite game
categories: [fasta, protein knowledgebases, NCBI datasets, UniProtKB, Ensembl]
excerpt: I like playing the does-this-have-an-annotated-genome game, but I rarely document it. So here you go, me finding a black widow.
---

This is going to be super messy and fraught with typos, but here you go. I like playing the "does this have an annotated genome" game, but I rarely document it. So I am going to use a recent one as a demo. Note that normally I am looking for mammals, but once you get into insects, fish, birds, plants, etc. this process gets to be harder. I am not describing prokaryotes since that could follow these same steps but then should also consult with JGI and other prok-centric resources. 


Case study: Colleague has a black widow that I have been encouraging proteomic analysis on. Problem, how do they search the mass spec data? [This older talk](https://zenodo.org/record/3687161) goes into how I did this way back when with the CA sea lion, and specifically how using related species annotations can work, albeit not the best but workable, and also how do you know if it is good? But for right now, I just want to show how and where I look for the genome annotation (feel free to check the first post on this blog about databases).

&nbsp; 

**Start - Does it have an annotation?**

I am not talking about, "does it have sequences on UniProt", but does this species specifically have a complete genome that has been annotated. So I started at [NCBI datasets](https://www.ncbi.nlm.nih.gov/datasets/) and searched Black Widows. Fortunately this name pulls up [the genus *Latrodectus*](https://www.ncbi.nlm.nih.gov/datasets/taxonomy/6923/), but searching species names is always better, and taxon ID is best (more on that later). So I don't see any annotations of [*Latrodectus* spp. (taxon ID 6923)](https://www.ncbi.nlm.nih.gov/datasets/taxonomy/6923/) specifically, just 3 genomes listed. What would this look like if it had been annotated? [Here is an example spider](https://www.ncbi.nlm.nih.gov/datasets/taxonomy/327109/) that does have an annotated genome, the lovely hackled orbweaver, genome assembly by Hopkins. I also like to consult the [RefSeq Eukaryotic Genome Annotation Status page](https://www.ncbi.nlm.nih.gov/genome/annotation_euk/status/), since not only will this clue me in on what is around, but what version there is. Honestly, I kind of stalk this page to watch the in-progress runs. (likewise there is no black widow annotation on UniProt, which isn't surprising since rarely if ever is something not on NCBI but is on UniProt, which is why I start at NCBI. More on how I am checking UniProt below).

&nbsp;

**What related species *are* annotated?**

Lets stay on NCBI Datasets and browse the taxonomy (it's an option to click). So I went up to the Araneae order (taxon ID 6893; aka spiders), clicked the 43 genomes, and then and then filtered the resulting table by annotations (so not just assemblies), and there are something like [15 spider species with genome annotations](https://www.ncbi.nlm.nih.gov/datasets/genome/?taxon=6893&annotated_only=true&refseq_annotation=true&genbank_annotation=true). This is a good sign that at least some spider work is being done. If you get to higher taxon levels like order and nothing is turning up, you could be in for a very long road.

&nbsp;

I wanted to know if something was closer than just Order-level related. The *Lactrodectus* genus (aka black widows) is under the Theridiidae family (taxon ID 34643; aka cobweb weavers), and so of those 15 Araneae annotations, only one Theridiidae family spider is annotated, [the house spider](https://www.ncbi.nlm.nih.gov/datasets/taxonomy/114398/). This is important as this house spider should be the most closely related to our black widow, and especially in insects/birds/plants/fish/non-mammmals, there is so much diversity that we can't safely assume moving beyond family level (or even genus level in some cases) that we will have sequence conservation to lean on for mass spec data searching (this is a gamble on mammals that normally pays off; again, see that talk in first paragraph). Maybe not great, but it is something to check performance on and at least know we are in the ballpark.

&nbsp;

For comparison to NCBI, let's check out UniProt. You can either browse using the taxonomy search (which will pull up all sequences under a taxon ID parent, or the "proteomes" which will just show species with their genomes annotated (more on this later).

&nbsp;

Specific UniProt links down the taxonomic tree:
* [Spider Order](https://www.uniprot.org/taxonomy/6893) - 614 093 total sequences/entries
* [Cobweb Family](https://www.uniprot.org/taxonomy/34643) - 18 234 total sequences/entries
* [Blackwidow genus](https://www.uniprot.org/taxonomy/6923) - 794 total sequences/entries

&nbsp;

Unsurprisingly, that top order with 615k entries is kind of a mess imo since [this one spider annotation](https://www.uniprot.org/proteomes/UP000499080) is like 250k entries (super sus), and drives the appearance of tons of spider sequences. **Note, this is why I focus on species with proteomes, not how many sequences per taxon  ID.**

&nbsp;

**Which fasta is "good"?**

In that [talk](https://zenodo.org/record/3687161) (around slide 22, is about elephant seal), my "easy" method is that at this point you can take two approaches, which assume you have some mass spec data to search.

* *Approach 1:* take the UniProt (sp + tr) sequences for each taxon ID and search the data three ways:
	+ all entries in genus
	+ all entries in family
	+ all entries in order

&nbsp;

* *Approach 2:* use the species-specific genome annotations on NCBI retrieved from Datasets or RefSeq Pipeline Page (or [UniProt proteomes](https://www.uniprot.org/proteomes?query=Araneae+AND+%28taxonomy_id%3A6893%29)) and search the data three ways:
	+ all species in genus (there isn't one in our black widow example, so skip this)
	+ all entries in family
	+ all entries in order

&nbsp;

The idea is to see the number of PSMs change with each search space addition. Your "best" search space will be the highest number. This is nice as well since if your search algorithm penalizes larger spaces (that is normal) then you should see diminishing returns as you increase your fasta size. Finally, a rule of thumb for DDA data: in tissues I would like to see 30-50 % of MS/MS get IDs, and for blood it can be closer to 20 - 30 %. Overall, worst case these non-species specific fasta should be getting at least 10 % for me not to feel horrible about the life choices that brought us to this situation (i.e., think about annotating an available genome assembly myself).

&nbsp;

There are more elegant ways of evaluating a fasta for mass spec data searching such as the method proposed by [Johnson et al.](https://pubmed.ncbi.nlm.nih.gov/31732549/) or by [me](https://pubmed.ncbi.nlm.nih.gov/37761836/), but making this table of PSM count v fasta composition works well enough (again, see [talk](https://zenodo.org/record/3687161) around slide 22 for a dated elephant seal example).

&nbsp; 

**But what is different about those spider sequences on NCBI v UniProt?**

There are more "complete" spider proteomes in NCBI ([the 15 mentioned above](https://www.ncbi.nlm.nih.gov/datasets/genome/?taxon=6893&annotated_only=true&refseq_annotation=true&genbank_annotation=true)) than is underlying the 615k entries in the Araneae Order in UniProt, which [looks more like 7 species only](https://www.uniprot.org/proteomes?query=Araneae+AND+%28taxonomy_id%3A6893%29) and then a bunch of rando sequences, which jives with what I normally see in that UniProt is easier to use, but is lagging behind in most proteomes. So UniProt Araneae has half the spider species genome annotations actually available. **To re-iterate, if something isn't on UniProt, that doesn't mean it doesn't exist.**

&nbsp; 

**But what about the genome assemblies? or other non-NCBI annotations?** 

There *are* [three black widow genomes on NCBI, which aren't annotated](https://www.ncbi.nlm.nih.gov/datasets/genome/?taxon=6923) (can we call these Lactrodectii?), and each genome assembly appears to be about 1.5 Gb. You could annotate these yourself with [TOGA](https://github.com/hillerlab/TOGA) or MAKER, or maybe even [write RefSeq a nice letter asking for it to be annotated](https://support.nlm.nih.gov/support/create-case/) (I bet there is RNAseq data out there, and this could be a viable approach).

&nbsp; 

Based on the genome dates here, these are not [this older 15K pilot project](https://www.hgsc.bcm.edu/arthropods/western-black-widow-spider-genome-project) at Baylor (anything <2015 to 2018 would be less than great compared to modern sequencing), and so should be considered "good enough" to annotate if there is RNAseq data.

&nbsp;

When I am looking for mammals I will also look over on [DNA Zoo](https://www.dnazoo.org/) (as their [in-house vanilla MAKER run on assemblies](https://www.dnazoo.org/post/announcing-the-release-of-updated-genome-annotations) works very well in my hands; it can be found on the data dump for each species, and the file will say something ending in HiC.fasta with or without a v2, [example](https://dnazoo.s3.wasabisys.com/index.html?prefix=Lagorchestes_conspicillatus/)), or other things like [this random TOGA repository](https://genome.senckenberg.de/download/TOGA/human_hg38_reference/). I don't tend to check VGP since their public versions are already on NCBI, and if it isn't public then it is embargoed and I philosophically disagree with using something not public and think this practice stifles science.

&nbsp;

You can sometimes find annotations in papers, but imo, this is gamble. Why people insist on making their own annotations and hiding them in weird places is beyond me. NCBI RefSeq or UniProt will annotate and distribute better than you ever could. Still, here is a cool [2022 paper](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC9154082/) that insists there are Lactrodectii genome annotations somewhere. I got tired just looking around. Update: I tried some more and it seems maybe things are on [GigaScience's own data repository thing](http://gigadb.org/search/new?keyword=Latrodectus) (cause who would want to use NCBI). The file ending with gff3.cds looks promising, but is a fasta of 20k untranslated genes. **Come back to this and find the actual fasta... it is another treasure hunt and should still be compared to the other available species with regards to performance on the data.**

&nbsp;

And lastly, what about ole [Ensembl](https://useast.ensembl.org/index.html)? This is what mostly feeds into UniProt, but also crossrefs with NCBI. The oldness of many non-model genomes is troubling to say the least (yep, there is a 2012 dolphin genome assembly). That said, using [Ensembl's new rapid release portal](https://rapid.ensembl.org/info/about/species.html) isn't as soul crushing, and will occasionally provide something not on NCBI. Spoiler, those spider genome assemblies aren't showing up here either.

&nbsp;  
&nbsp;  
&nbsp;  
