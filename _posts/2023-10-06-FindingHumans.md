---
layout: post
title:  Fasta game part deux - Humans
categories: [fasta, protein knowledgebases, NCBI datasets, UniProtKB, Ensembl]
excerpt: but wait, what about human, surely that is the same fasta everywhere
---

**Bonus: Humans!**

The [previous post](https://neely.github.io/FindingFASTA/) about a fasta finding exercise should have emphasized that there can be more than one fasta for a given species (not black widow), and in some species there can also be more than one genome assembly for a species (spoiler: there isn't just one for human, and this is going to get worse in the coming year(s)). So what about ole 9606 (you know them also as Homo sapiens)? NCBI currently has GRCh38.p14 has the reference genome assembly [for Human](https://www.ncbi.nlm.nih.gov/datasets/taxonomy/9606/)(of 1090 genome assemblies!). If we click on GRCh38.p14, we can see [a lot more on this assembly](https://www.ncbi.nlm.nih.gov/datasets/genome/GCF_000001405.40/), including assembly stats, BUSCO stats, etc. Within this page you will see ["See full annotation report"](https://www.ncbi.nlm.nih.gov/genome/annotation_euk/Homo_sapiens/GCF_000001405.40-RS_2023_03), which is super handy for any RefSeq annotation (this also tells you what data was used for the annotation, and will normally compare to the last annotation). Most non-human species releases used to be numbered starting at 100 (so the second release is 101, and so on), but this is changing to "Assembly-releaseDate". You can see the current release versions for all the Euks on [Eukaryotic genomes annotated at NCBI](https://www.ncbi.nlm.nih.gov/genome/annotation_euk/all/). This dashboard is also where you could find previous releases, which can be fun to see how your analysis changes with updates (did you know things *can* change!).

&nbsp; 
*This method may change as NCBI updates their site, but you can click the FTP link for a species, and this will take you to a directory that you can browse previous releases. Ex. Giant panda release [102 and 103 are here](https://ftp.ncbi.nlm.nih.gov/genomes/all/annotation_releases/9646/). In some cases this gets really confusing, like the dog which has [5 seperate genome assemblies, each with their own annotation releases](https://ftp.ncbi.nlm.nih.gov/genomes/all/annotation_releases/9615/106/). Funny, you get at [4 of these 5 over on UniProt](https://www.uniprot.org/proteomes?query=Canis+lupus+familiaris).*

&nbsp; 

Back to the current reference NCBI human [annotation report](https://www.ncbi.nlm.nih.gov/genome/annotation_euk/Homo_sapiens/GCF_000001405.40-RS_2023_03), we see they now call versions based on the underlying assembly (GCF_000001405.40; the F in GCF means RefSeq v GCA which is GenBank) and a date of the most recent annotation (2023_03). For the eagle eye, you will see the T2T assembly is also listed, and you can click on it and boom, now you've got the [T2T based annotation](https://www.ncbi.nlm.nih.gov/datasets/genome/GCF_009914755.1/), also performed March 2023. The main organisms are re-annotated by RefSeq every 6 to 9 months, so think about that. 

&nbsp;

*Aside on knowing the annotation version/date for each species versus UniProt "releases" or Ensembl "versions": the latter don't imply every species on the site is updated, and often can be difficult to understand what species were updated. Yes, the NCBI approach seems more complicated, but this granular information provides complete provenance on fasta versioning, which affects proteomic results, and so is pretty key.*

&nbsp; 

Anyways, so now we know at least two NCBI human genome assemblies and their annotations. **And OMG, [did you see it is being re-annotated right at this moment!!!](https://www.ncbi.nlm.nih.gov/genome/annotation_euk/status/)** So I have written about March 2023 and it is being outdated as I type. 

&nbsp; 

So let's go over to UniProt. Using the [proteomes search feature](https://www.uniprot.org/proteomes?query=9606), let's find ole 9606. And we have a [full proteome of human](https://www.uniprot.org/proteomes/UP000005640) (one). Like on NCBI Datasets above we have some BUSCO scores and it list the underlying assembly is "GCA_000001405.28 from Ensembl" (as opposed to the GCA_000001405.29 assembly the RefSeq is based on). Again, for the eagle eye, you will note that the BUSCO scores for this assembly are not the same as that for the two RefSeq assemblies. And there are even different numbers of sequences (protein coding entries for [NCBI reference 9606](https://www.ncbi.nlm.nih.gov/datasets/genome/GCF_000001405.40/), [T2T](https://www.ncbi.nlm.nih.gov/datasets/genome/GCF_009914755.1/), and [UniProt](https://www.uniprot.org/proteomes/UP000005640) are 20 080, 20 154, and 20 594). You can figure out what is different, or even how certain sequences are different (I have blasted whole fasta against each other just to prove to myself they are different). It may be inconsequential... unless those changes are the thing that is important or that it changes your identity thresholds, etc. But back to the [full proteome of human](https://www.uniprot.org/proteomes/UP000005640), there is no version listed here or date. We can click out to [the Ensembl page](http://useast.ensembl.org/Homo_sapiens/Info/Annotation). Here we see some good stuff like it is GRCh38.p14 Build 38 (and going back over to the [NCBI page](https://www.ncbi.nlm.nih.gov/datasets/genome/GCF_000001405.40/), I see hg38 as a synonym, so these are the same thing). We also see the gene build is March 2023, so that is version. I won't continue to go in how this relates to NCBI and vice versa, or getting into the Golden Path ([UCSC Genome Browser](https://genome.ucsc.edu/)) where you can get hg 38, hg19, and T2T. It is another hole and I don't do humans much.

&nbsp; 

*Aside, when using UniProt, don't do sprot or trembl anymore, but instead use the whole proteome feature and unless you have a reason to otherwise, use the "Download one protein sequence per gene (FASTA)" option.*

&nbsp; 

The point is, things are different across data sources, even for a human. These differences become more pronounced in other species. Moreover, you can't assume whether UniProt is taking in the Ensembl versions or the RefSeq genome assembly version. Ex. The [*Tursiops truncatus* genome on Ensembl](http://useast.ensembl.org/Tursiops_truncatus/Info/Annotation) was annotated in April 2013, of an assembly from 2008(!!!!). On [UniProt](https://www.uniprot.org/proteomes/UP000245320) they are using a [RefSeq build](https://www.ncbi.nlm.nih.gov/datasets/genome/GCF_011762595.1/) (maybe even their second or third update, which is commendable!), and this fortunately is the most up to date version on NCBI from 2020, and since the dates line up with [RefSeq Release 102](https://www.ncbi.nlm.nih.gov/genome/annotation_euk/Tursiops_truncatus/102/), March 2020, and the date on UniProt.... oh wait, there isn't one. BUT even more important to note again, even these fasta are NOT the same. 


&nbsp;  
&nbsp;  
&nbsp;  
