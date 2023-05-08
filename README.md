# POS tag conversion for LA Murre

This script coverts the POS tags of the [*Lauseopin arkiston murrekorpus / Finnish Dialect Corpus of the Syntax Archive*](https://www.kielipankki.fi/aineistot/la-murre/) to the [UPOS](https://universaldependencies.org/u/pos/) tagset.
It was used for the paper *Does manipulating tokenization aid cross-lingual transfer? A study on POS tagging for non-standardized languages* (Blaschke ea, VarDial 2023, [link](https://aclanthology.org/2023.vardial-1.5/)).

# Usage

```
# Retrieve the corpus
wget https://korp.csc.fi/download/la-murre/vrt/la-murre-vrt.zip
unzip la-murre-vrt.zip
rm la-murre-vrt.zip

python3 convert.py --infiles "LA-murre-vrt/lam_*.vrt --outdir "." --groupby "region"
```

This results in one or more TSV files where each line contains a word form and its POS tag, and sentence boundaries are indicated through empty lines.

# Details

## POS tags

References:
- University of Turku & Institute for the Languages of Finland. *The Finnish Dialect Corpus of the Syntax Archive, Downloadable VRT* Version [text corpus]. Kielipankki. Retrieved from http://urn.fi/urn:nbn:fi:lb-2019092001. The corpus is released under a [CC BY-ND 4.0](https://creativecommons.org/licenses/by-nd/4.0/legalcode) license, which allows modifying the data, but not sharing the modified data.
- [Lauseopin arkiston murrekorpuksen annotaatioiden kuvaus](https://www.kielipankki.fi/aineistot/la-murre/la-murre-annotaatiot/) -- description of LA-murre's POS tags
- [Turku Dependency Treebank -järjestelmällä (TDT) annotoitujen korpusten morfosyntaktisen analyysin (msd)
koodit selitettyinä](https://blogs.helsinki.fi/fennistic-info/files/2020/12/2.-Sananmuodot-morfologia-morfosyntaksi.pdf) -- provides additional examples for LA-murre's annotations (right-most column)
- UD annotation information for [Finnish](https://universaldependencies.org/fi/index.html) in general and [UD Finnish TDT](https://universaldependencies.org/treebanks/fi_tdt/index.html) in particular (UD Finnish TDT is the largest and most diverse (in terms of genres) Finnish UD treebank).


| UPOS | LA-Murre | Notes |
| -- | -- | -- |
| ADJ | a, a:pron, a:pron:dem, a:pron:int, a:pron:rel, num:ord, num:ord_pron, q(\*) | see note below |
| ADP | p:post, p:pre |
| AUX | v(\*), neg | see note below |
| ADV | adv, adv:pron, adv:pron:dem, adv:pron:int, adv:pron:rel, adv:q, p:adv |
| CCONJ | cnj:coord |
| DET | | not used in UD F. TDT |
| INTJ | intj |
| NOUN | n |
| NUM | num:card, num:murto | *murto* "fraction" |
| PART |  | not used in UD F. TDT |
| PRON | pron, pron:dem, pron:int, pron:pers, pron:pers12, pron:ref,  pron:rel, q(\*) |
| PROPN | n:prop, n:prop:pname |
| PUNCT | punct |
| SCONJ | cnj:rel, cnj:sub |
| SYM |  | There don't appear to be any symbols in the data -- but the data are transcribed conversations, so this isn't much of a surprise. |
| VERB | v(\*) | |
| X | muu | *muu* "other"|

`DET` and `PART` aren't used by UD Finnish TDT and therefore we exclude them as well. 
(This affects `a:pron:dem`, `a:pron:int` and `pron:dem.`)

We follow UD Finnish TDT in classifying `a:pron:rel` as `ADJ` (rather than `PRON`).

The distinction between auxiliary vs. lexical verbs isn't encoded in LA-murre.
- `AUX` forms/lemmas in UD Finnish TDT: https://universaldependencies.org/treebanks/fi_tdt/fi_tdt-pos-AUX.html. These can however also be full verbs.
- In LA-murre, the annotations `pred2` and `pred3` are used in sentences with multiple verbs, where `pred` (and, if applicable, `pred2`) are auxiliaries and `pred2` (or, if applicable, `pred3`) is an infinitive or participle.
- We assign verbs with the lemma *olla* "to be" or with a lemma matching the AUX list above and a corresponding `predX` annotation to the `AUX` class.

Lemmas tagged `q` in LA-murre and their tags in UD_Finnish-TDT:
```
??? -- [used for unknown lemmas]
ainoa ('PRON', 38) ('ADJ', 2) ('NOUN', 1)
ainut ('PRON', 1)
ensi ('ADJ', 23) ('ADV', 1)
eri ('ADJ', 133)
eräs ('PRON', 79)
harva ('PRON', 9) ('ADJ', 6) [corresponding to harva being tagged 'q' or 'a' in LA-murre]
itsekukin --
joka ('PRON', 1873)
joka ainoa -- [UD_TDT: PRON+ADJ]
joka ikinen -- [UD_TDT: PRON+PRON]
jokaan --
jokainen ('PRON', 84)
jokin ('PRON', 326)
joku ('PRON', 125)
jokunen ('PRON', 1)
jompi --
jompikumpi ('PRON', 3)
kaikki ('PRON', 544) ('ADV', 1)
ken ('PRON', 3)
koko ('ADJ', 155) ('NOUN', 15)
kuka ('PRON', 55)
kukaan ('PRON', 75)
kukin ('PRON', 37)
kumma ('ADJ', 7)
kumpainen --
kumpainenkaan --
kumpainenkin --
kumpi ('PRON', 2)
kumpikin ('PRON', 18)
mikin ('PRON', 1)
mikä ('PRON', 559)
mikään ('PRON', 191)
molemmat ('PRON', 41)
moni ('PRON', 165)
monias --
muu ('PRON', 418) ('ADV', 2)
muuan ('PRON', 4)
muutama ('PRON', 88)
oma ('ADJ', 282) ('NOUN', 1)
omas --
oooo -- [used for unknown lemmas]
sama ('PRON', 210)
samainen ('ADJ', 1)
usea ('ADJ', 127) ('PRON', 6) [both 'a' and 'q' are used]
```

## Dialect groups

For a map, see e.g. [here](https://www.sprakinstitutet.fi/sv/om_sprak/dialekter/finska_dialekter).
Options (`--groupby` flag):
- `group`: 25-way distinction; meta tag `dialect_group` in the original files
- `eight`: 8-way distinction based on the `dialect_group` information (SW Finnish, SW Finnish transition area, Tavastian, South Ostrobothnian, Central/North Ostrobothnian, North Finnish, Savonian, SE Finnish)
- `region`: 6-way distinction; meta tag `dialect_region` in the original files; like the previous grouping but it combines South Ostrobothnian, Central/North Ostrobothnian and North Finnish into a single Ostrobothnian region
- `two`: 2-way distinction: East vs. West
- `no_groups`: all source files grouped together in one target file
