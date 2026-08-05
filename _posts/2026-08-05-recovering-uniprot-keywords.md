---
layout: post
title: "Recovering UniProt Keywords for deleted accessions"
date: 2026-08-05
tags: [metaproteomics, uniprot, python, keywords]
excerpt: "If you searched against a pre-June 2026 UniProt database, some of your identified proteins no longer exist in the current release — and that means no keywords."
---

If you searched against a pre-June 2026 UniProt database, some of your identified proteins no longer exist in the current release... and that means no keywords among other things. If you are using 2026_02 release as the good lord intended then none of this applies to you.

&nbsp;

But if you are like me, you read [Vande Moortele et al.](https://www.biorxiv.org/content/10.64898/2026.02.24.707692v1.full) and decided to use the full 2026_01 TrEMBL (and associated Swiss-Prot and varsplic) for first-pass searching of metaproteomic data to avoid missing anything in the shrinking UniProtKB space. Great idea, right. Right?! Well, the [June 2026 reorganization](https://www.uniprot.org/release-notes/2026-06-10-release) removed ~43% of TrEMBL entries (and to be fair, I knew this was happening, they told us). Those accessions still exist in your search results, but when you try to pull their UniProt Keywords for functional analysis, the live API returns an empty stub.

&nbsp;

Good news as pointed out to me by Elisabeth at UniProt, the keywords are not gone, they just live in [UniSave](https://www.uniprot.org/help/unisave), which archives every version of every entry UniProt has ever published including deleted ones. So I wrote a small pipeline to get them back.

&nbsp;

Step one is [kw_fetch.py](https://github.com/neely/kw_rescue), which maps your full accession list against the live UniProt REST id-mapping API and pulls keywords for anything still in the current release. It also writes out an orphan list of anything that came back empty. Fair warning: the id-mapping endpoint silently truncates results at some value (maybe ~2000 rows per job), and there is a bonus boundary bug at around 1000 accessions per job that returns nothing. The script handles both by chunking at 900 and retrying with escalating backoff, but if you are still having trouble just split your list manually and merge the outputs. I think it starts to timeout on you if you do too many in rapid succession. This wait in the code may not work and maybe you manually split it and do it seperately.

&nbsp;

Step two is [unisave_keywords.py](https://github.com/neely/kw_rescue), which takes that orphan list and pulls the last archived version of each deleted entry from UniSave. If the archived entry has no keywords of its own, it falls back to finding an identical-sequence sibling in UniParc and inheriting from that. If you are really committed, there is a third step that runs EBI BLAST on whatever is left, but that is slow and the results are weaker, and you can even manually BLAST whatever is left over. Again, using the 2026_02 means you don't live in this lovely annotation hellscape.

&nbsp;

On my list of 1145 deleted accessions I got 1136 keyworded (99.2%): 1064 direct from UniSave, 28 from siblings, 44 from BLAST. Nine came back with nothing. I will take it.