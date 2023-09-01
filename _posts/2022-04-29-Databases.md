---
layout: post
title: Databases
categories: [fasta, protein knowledgebases, mass spec-based proteomics]
excerpt: A deep dive into sources of fasta (for mass spec-based proteomics) and some recommendations
---

Note: I wrote this partly for myself, and have moved my "fasta naming cheat sheet" to the top, and partly for a paper Jesse Meyer organized and published through [GitHub](https://github.com/jessegmeyerlab/proteomics-tutorial) using Manubot, later compiled [here](https://jessegmeyerlab.github.io/proteomics-tutorial/). The pre-paper version here also is the result of valuable feedback and iterations with Phil Wilmarth, who I consider an authority and tried to be on the same page as him. Also note that this was written spring of 2022 and since then UniProt updated their website, and some changes to an accelerated Ensembl release portal. I will attempt to update any truly broken links or concepts. And apologies on typos.

---

# Ben's fasta naming cheat sheet

## UniProtKB
```
[common or scientific name]-[taxon id]-uniprot-[swiss-prot/trembl/proteome]-[UP# if used]-[canonical/canonical plus isoform]-[release]
```
example of a human protein fasta from UniProtKB:
```
Human-9606-uniprot-proteome-UP000005640-canonical-2022_01.fasta
```

## RefSeq 
```
[common or scientific name]-[taxon id]-refseq-[release number]
```
example of a horse protein FASTA from RefSeq:
```
Equus_caballus-9796-refseq-103.fasta
```

## Ensembl
```
[common or scientific name]-[taxon id]-ensembl-[abinitio/all]-[rapid]-[release number]
```
example of a pig protein FASTA from Ensembl:
```
Pig-9823-ensembl-all-106.fasta
```
---


# Protein Databases


Many mass spectrometry-based proteomic techniques use search algorithms that require a defined theoretical search space to identify peptide sequences based on precursor mass and fragmentation patterns, which are used to infer the presence and abundance of a protein. The search space is calculated from the potential proteins in a sample, which includes the proteome (often a single species) and expected contaminants. This is called database searching and the flat file of protein sequences in FASTA format is acting as a protein database. In this section, we will describe major resources for proteome FASTA files (protein sequence collections), how to retrieve them, and suggested best practices for preserving FASTA file provenance and improving reproducibility. 


FASTA sequence collections can be retrieved from three central clearing houses: UniProt, RefSeq, and Ensembl. These will be discussed separately below as they each have specific design goals, data products, and unique characteristics. It is important to learn the following three points for each resource: the source of the underlying data, canonical versus non-canonical sequences, and how versioning works. These points, along with general best practices, such as using a taxonomic identifier, are essential to understand and communicate search settings used in analyses of proteomic datasets. Finally, it is critical to understand that sequence collections from these three resources are not the same, nor do they offer the same sets of species.


Key terminology may vary between resources, so these terms are defined here. The term “taxon identifier” is used across resources and is based on the NCBI taxonomy database. Every taxonomic node has a number, e.g., Homo sapiens (genus species) is 9606 and Mammalia (class) is 40674. This can be useful when retrieving and describing protein sequence collections. Another term used is “annotation”, which has different meanings in different contexts. Broadly, a “genome annotation” is the result of an annotation pipeline to predict coding sequences, and often a gene name and symbol if possible. Two examples are MAKER (PMID: 22192575) and the RegSeq annotation pipeline, [link](https://www.ncbi.nlm.nih.gov/genome/annotation_euk/process/). Alternatively, “protein annotation” (or gene annotation) often refers to the annotation of proteins (gene products) using names and ontology (i.e., protein names, gene names, gene symbols, functional domains, gene onotology, keywords, etc.). Protein annotation is termed “biocuration” and described in detail by UniProt, [link](https://www.uniprot.org/help/biocuration). Lastly, there are established minimum reporting guidelines for FASTA files established in MIAPE: Mass Spectrometry Informatics that are taxon identifier and number of sequences ([link](https://www.psidev.info/sites/default/files/2018-03/MIAPE_MSI_1.1.pdf); PMID: 23500130). The FASTA file naming suggestions below are not official but are suggested as a best practice.


# UniProt


The Universal Protein Resource (UniProt; PMID: 14681372), has three different products: UniProt Knowledgebase (UniProtKB), the UniProt Reference Clusters (UniRef), and the UniProt Archive (UniParc). The numerous resources and capabilities associated with the UniProt are not explored in this section, but these are well described on UniProt’s website. UniProtKB is the source of proteomes across the Tree of Life and is the resource we will be describing herein. There are broadly two types of proteome sequence collections: Swiss-Prot/TrEMBL and designated proteomes. The Swiss-Prot/TrEMBL type can be understood by discussing how data is integrated into UniProt. Most protein sequences in UniProt are derived from coding sequences submitted to EMBL-Bank, GenBank and DDBJ. These translated sequences are initially imported into TrEMBL database, which is why TrEMBL is also termed “unreviewed”. There are other sources of sequences, as described by UniProt, [link](https://www.uniprot.org/help/sequence_origin). These include the Protein Data Bank (PDB), direct protein sequencing, sequences derived from literature, gene prediction (from sources such as Ensembl) or in-house by UniProt itself. Protein sequences can then be manually curated into the Swiss-Prot database using multiple outlined steps (described in detail by UniProt [here](https://www.uniprot.org/help/manual_curation) and is why Swiss-Prot is also termed “reviewed”. Note that more than one TrEMBL entry may be removed and replaced by a single Swiss-Prot entry during curation. A search of “organism:9606” at UniProtKB will retrieve both the Swiss-Prot/reviewed and TrEMBL/unreviewed sequences for Homo sapiens. The entries do not overlap, so users often either use just Swiss-Prot or Swiss-Prot combined with TrEMBL, the latter being the most exhaustive option. With ever-increasing numbers of high-quality genome assemblies processed with robust automated annotation pipelines, TrEMBL entries will contain higher quality protein sequences than in the past. In other words, if a mammal species has 20 000 to 40 000 entries in UniProtKB and many of these are TrEMBL, users should be comfortable using all the protein entries to define their search space. Determining the expected size of a well-annotated proteome requires additional knowledge, but tools to answer these questions continue to improve. As more and more genome annotations are generated, the backlog of manual curation continues to increase. However, automated genome annotations are also rapidly improving, blurring the line between Swiss-Prot and TrEMBL utility.


The second type of protein sequence collections available at UniProtKB are designated proteomes, with subclasses of “proteome”, “reference proteome” or “pan-proteome”. As defined by UniProt, a proteome is the set of proteins derived from the annotation of a completely sequenced genome assembly (one proteome per genome assembly). This means that a proteome will include both Swiss-Prot and TrEMBL entries present in a single genome annotation, and that all entries in the proteome can be traced to a single complete genome assembly. This aids in tracking provenance as assemblies change, and metrics of these assemblies are available. These metrics include Benchmarking Universal Single-Copy Ortholog (BUSCO) score, and “Completeness” as Standard, Close Standard or Outlier based on the Complete Proteome Detector (CPD). Given the quality of genome annotation pipelines, using a proteome as a FASTA file for a species is the preferred method of defining search spaces now. Outside of humans, no higher eukaryotic Swiss-Prot sequence collections are complete enough for use in proteomics analyses, but this does not mean that the available Swiss-Prot plus TrEMBL protein sequence collection precludes accurate proteomic data analysis. Lastly, the difference between reference proteome and proteome is used to highlight model organisms or organisms of interest, but not to imply improved quality. UniProt also has support for the concept of “pan proteomes” (consensus proteomes for a closely related set of organisms) but this is mostly used for bacteria (e.g., strains of a given species will share a pan proteome). 


When retrieving protein sequence collections as Swiss-Prot/TrEMBL or designated proteomes, there is an option of downloading “FASTA (canonical)” or “FASTA (canonical & isoform)”. The later includes additional manually annotated isoforms for Swiss-Prot sequences. Each Swiss-Prot entry has one canonical sequence chosen by the manual curator. Any additional sequence variants (mostly from alternative slicing) are annotated as differences with respect to the canonical sequence. Specifying canonical will select only one protein sequence per Swiss-Prot entry while specifying canonical plus isoforms will download additional protein sequences by including isoforms for Swiss-Prot entries. Recently, an option to “download one protein sequence per gene (FASTA)” has been added. These FASTA files include Swiss-Prot and TrEMBL sequences to achieve about 20 000 protein sequences for a wide range of higher eukaryotic organisms. **If there was one universal recommendation from this review/ramble, it is that in most cases using the UniProt Proteome and “download one protein sequence per gene (FASTA)” is the recommended fasta for searching. This includes Swiss-Prot plus TrEMBL for each gene (no isoforms), which is considered advantageous for most search strategies (briefly described below), especially DIA (even if the software says large seearch space is fine), and lastly, for someone like me who needs to map orthologs between species, this makes post-searching mapping less complex as there won't be multiple isoforms to merge or map. YMMV, but you could do worse using anything else.**


The number of additional isoforms varies considerably by species. Human and mouse have large numbers of isoforms, while most other higher eukaryotes have far fewer. Simpler organisms with smaller or less characterized genomes often have few isoforms. The choice of including isoforms is related to the search algorithm and experimental goals. For instance, if differentiating isoforms is relevant, they should be included otherwise they will not be detected. Non-redundant FASTA files can also be created using post-processing software (i.e., “remove_duplicates.py” in [https://github.com/pwilmart/fasta_utilities](https://github.com/pwilmart/fasta_utilities) ). Though FASTA files are the typical input of many search algorithms, UniProt also offers an XML and GFF format download. In contrast to the flat FASTA file format, the XML format includes sequence information as well as associated information like PTMs, which is used in some search algorithms like MetaMorpheus. 


Once a protein sequence collection has been selected and retrieved, there is the evergreen question of how to name and report this to others in a way that allows them to mimic the retrieval. The minimum reporting information is the taxon identified and number of sequences used. The following naming format is suggested for UniProtKB FASTA files (the use of underscores or hyphens is not critical): 
```
[common or scientific name]-[taxon id]-uniprot-[swiss-prot/trembl/proteome]-[UP# if used]-[canonical/canonical plus isoform]-[release]
```
example of a human protein fasta from UniProtKB:
```
Human-9606-uniprot-proteome-UP000005640-canonical-2022_01.fasta
```


The importance of the taxon identifier has already been described above and is a consistent identifier across time and shared across resources. The choices of Swiss-Prot and TrEMBL in some combination was discussed above, and Proteome can be “proteome”, “reference proteome” or “pan-proteome”. The proteome identifier (‘UP’ followed by 9 digits) is conserved across releases, and release information should also be included. A confusing issue to newcomers is what the term “release” means. This is a year_month format (e.g., 2022_01), but it is not the date a FASTA file was downloaded or created, nor does it imply there are monthly updates. This release “date” is a traceable release identifier that is listed on UniProt’s website. Including all this information ensures that the exact provenance of a FASTA file is known and allows the FASTA file to be regenerated.


# RefSeq


NCBI is a clearing house of numerous types of data and databases. Specific to protein sequence collections, NCBI Reference Sequence Database (RefSeq) provides annotated genomes across the Tree of Life. The newly developed “NCBI Datasets” portal, [link](https://www.ncbi.nlm.nih.gov/datasets/) is the preferred method for accessing the myriad of NCBI data products, though protein sequence collections can also be retrieved from RefSeq directly. Like UniProt above, most of the additional functionality and information available through NCBI Datasets and RefSeq will not be described here, although the RefSeq annotation dashboard is a noteworthy resource to monitor the progress of new or re-annotations. We recommend exploring the resources available from NCBI, [link](https://www.ncbi.nlm.nih.gov/guide/training-tutorials/), utilizing their tutorials and help requests.


RefSeq is akin to the “proteome” sequence collection from UniProtKB, where a release is based on a single genome assembly. If a more complete genome assembly is deposited or additional secondary evidence (RNA sequencing) is deposited, RefSeq can update the annotation with a new release. Every annotation release will have an annotation report that contains information on the underlying genome assembly, the new genome annotation, secondary evidence used, and various statistics about what was updated. The current annotation release is referred to as the “reference annotation”, but each annotation is numbered sequentially starting at 100 (the first release). Certain species are on scheduled re-annotation, like human and mouse, while other species are updated as needed based on new data and community feedback (ex. release 100 of taxon 9704 was in 2018, but a more contiguous genome assembly resulted in re-annotation to release 101 in 2020). This process for new and existing species is described in Heck and Neely (PMID: 32786681).


Since RefSeq is genome assembly-centric, its protein sequence collections are retrieved for each species. This contrasts with being able to use a higher-level taxon identifier like 40674 (Mammalia) in UniProt to retrieve a single FASTA. To accomplish this same search in NCBI Datasets requires a Mammalia search, followed by browsing all 2083 genomes and then filtering the results to reference genomes with annotations, and those resulting 188 could be bulk downloaded, though this will still be 188 individual FASTA files. It is possible to download a single FASTA from an upper-level taxon identifier using the NCBI Taxonomy Browser, though this service may be redundant with the new Datasets portal. Given the constant development of NCBI Datasets, this functionality may change, but the general philosophy of single species FASTA should be kept in mind. Likewise, when retrieving genome annotations there is no ability to specify canonical entries only, but it is possible to use computational tools to remove duplicate entries (“remove_duplicates.py” in [https://github.com/pwilmart/fasta_utilities](https://github.com/pwilmart/fasta_utilities) ). 


Similar to the UniProtKB FASTA file naming suggestion, the following naming format is suggested for RefSeq protein sequence collection FASTA (the use of underscores or hyphens is not critical): 
```
[common or scientific name]-[taxon id]-refseq-[release number]
```
example of a horse protein FASTA from RefSeq:
```
Equus_caballus-9796-refseq-103.fasta
```


The release number starts at 100 and is consecutively numbered. Note, the human releases only recently began following this consecutive numbering for Release 110, and previously had a much longer number to be included (e.g., NCBI Release 109.20211119). Also, in a few species (Human and Chinese hamster currently), there is a reference and an alternate assembly with an available annotation. In these cases, including the underlying assembly would be needed. Note that when you retrieve the protein FASTA from NCBI it will include two more identifiers that aren’t required in the file name since it can be determined from the taxon identifier and release number. These are the genome assembly used (this is user generated and follows no naming scheme) and the RefSeq identifier (GCF followed by a number string). These aren’t useful for naming, but are for comparing between UniProt, RefSeq and Ensembl when the same underlying assembly is used (or not, indicating how up to date one is versus the other).


# Ensembl


There are two main web portals for Ensembl sequence collections: the Ensembl genome browser (ensembl.org) has vertebrate organisms and the Ensemble Genome project (ensemblgenomes.org) has specific web portals for different non-vertebrate branches of the Tree of Life. This contrasts with NCBI and UniProt where all branches are centrally available. Recently, Ensembl has created a new portal “Rapid Release” focusing on quickly making annotations available (replacing the “Pre-Ensemble” portal), albeit without the full functionality of the primary Ensembl resources. Ensembl provides diverse comparative and genomic tools that should be explored, but, specific to this discussion, they provide species-specific genome annotation products similar to RefSeq. 


To retrieve a protein sequence collection from Ensemble at any of the portals, a species can be searched using a name, which will then have taxon identifier displayed (but searching by identifier is not readily apparent). From the results you can select your species and follow links for gene annotation. Caution should be used when browsing the annotation products since the cds (protein coding sequence) annotations are nucleic acid sequences (useable via 3-frame translation), while actual translated peptide sequences are in the “pep” folders. The pep folders contain file names with “ab initio” and “all” in the FASTA file names (file extensions are “fa” for FASTA and “gz” indicating compression algorithm), while there may only be one pep product for certain species in the “Rapid Release” portal. The “ab initio” FASTA files contain mostly predicted gene products. The “all” FASTA files are the usable protein sequence collections. Ensembl FASTA files usually have some protein sequence redundancy.


Ensembl provides a release number for all the databases within each portal. Similar to the UniProt file naming suggestion, the following naming format is suggested for Ensembl protein sequence collection FASTA (the use of underscores or hyphens is not critical):
```
[common or scientific name]-[taxon id]-ensembl-[abinitio/all]-[rapid]-[release number]
```
example of a pig protein FASTA from Ensembl:
```
Pig-9823-ensembl-all-106.fasta
```
Similar to the download from RefSeq, the downloaded file name can include additional identifying information related to the underlying genome assembly. Again, this is not required for labeling, but is useful to easily compare assembly versions. 


Since much of the data from Ensembl is also regularly processed into UniProt, using UniProt sequence collections instead may be preferred. That said, they are not on the same release schedule nor will the FASTA files contain the same proteins. Ensembl sequences still must go through the established protein sequence pipeline at UniProt to remove redundancy and conform to UniProt accession and FASTA header formats. Moreover, the gene-centric and comparative tools built into Ensembl may be more experimentally appropriate and using an Ensembl protein sequence collection can better leverage those tools.


# Other resources


There are other locations of protein sequence collections, and these will likewise have different FASTA file formatting (sequences may have unusual characters, and formats of accessions and FASTA header lines may need to be reformatted to be compatible with search software). These alternatives include institutes like the Joint Genome Institute’s microbial genome clearing house, species-specific community resource (e.g., PomBase, FlyBase, WormBase, TryTrypDB, etc.), and one-off websites tenuously hosting in-house annotations. The FASTA header differences may require changes to the FASTA itself before use in many search algorithms. It is preferred to use protein sequence collection from the main three sources described here, since provenance can be tracked, and versions maintained. It is beyond the scope of this discussion to address other genome annotation resources, how they are versioned, or the best way to describe FASTA files retrieved from those sources. In these cases, defaulting to the minimum requirements of listing number of entries and supplying the FASTA along with data are necessary.


# Contaminants


Samples are rarely comprised of only proteins from the species of interest. There can be protein contamination during sample collection or processing. This may include proteins from human skin, wool from clothing, particles from latex, or even porcine trypsin itself, all of which can be digested along with the intended sample and analyzed in the mass spectrometer. As early as 2004, The Global Proteome Machine was providing a protein sequence collection of these common Repository of Adventitious Proteins (cRAP), while another contaminant list was published in 2008 (PMID: 18790129). The current cRAP version (v1.0) was described in 2012 , [link](https://www.thegpm.org/crap/), and is still widely in use today. The cRAP is the contaminant protein list used in nearly all modern database searching software, though the documentation, versioning or updating of many of these contaminant sequence collections is difficult to follow. For instance, there is another contaminant sequence collection distributed with MaxQuant. The cRAP and MaxQuant contaminant protein sequence collections are found in some form across most software, including MetaMorpheus (a modified blend of cRAP and MaxQuant) and Philosopher (uses cRAP). This list of known frequently contaminating proteins can either be automatically included by the software or can be retrieved as a FASTA to be used along with the primary search FASTA(s). Recently the Hao Lab has revisited these common contaminant sequences in an effort to update the protein sequences, test their utility on experimental data, and add or remove entries (doi: https://doi.org/10.1101/2022.04.27.489766). In addition to these environmentally unintended contaminants, there are known contaminants that also have available protein sequence collections (or can be generated using the steps above) and should be included in the search space. These can include the media cells were grown in (e.g., fetal bovine serum), food fed to cells/animals (e.g., Caenorhabditis elegans grown on Escherichia coli) or known non-specific binders in affinity purification (i.e., CRAPome; PMID: 23921808). As emphasized throughout this section, accurately defining the search space is essential for accurate results and, especially in the case of contaminants, requires knowledge of the experiment and sample processing to adequately define possible background proteins.



&nbsp;  
&nbsp;  
&nbsp;  
