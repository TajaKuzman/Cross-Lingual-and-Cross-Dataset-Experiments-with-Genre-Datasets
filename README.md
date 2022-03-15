# Cross-dataset and cross-lingual experiments

## Overview of the task

I will perform cross-dataset and cross-lingual text classification (automatic genre identification) experiments to explore comparability of two genre-annotated corpora, English CORE corpus and Slovene GINCO corpus. To this end, I will 1) train and test on the same dataset to obtain baseline results, 2) train on Slovene corpus and test on English corpus (cross-lingual classification) to see how appropriate whether we can achieve cross-lingual transfer by using these two datasets, 3) train on Slovene corpus, machine-translated to English, and test on English corpus (cross-dataset classification) to explore comparability of corpora while minimising the impact of the language.

### Datasets
We will perform experiments on 3 datasets, all using the GINCORE labels:
* Slovene GINCO (SL_GC), 
* English CORE (EN_C)
* machine-translated GINCO (MT_GC: SL_GC, machine-translated to English)

### Experiments:
I'll do one run of each experiment.

1.	in-dataset experiments:
    * train and test on SL_GC
    * train and test on EN_GC
    * train and test on MT_GC

2. cross-lingual experiment:
    * train on SL_GC, test on EN_GC

3. cross-dataset experiment (on English train and test split, to minimise the impact of cross-lingual learning):
    * train on MT_GC, test on EN_GC

In practice, I will train each model once and then test it parallely on multiple test datasets. Out of interest, I will also analyse cross-lingual and cross-dataset results in the other directions (that won't be included in the paper):
1. train on SL_GC, test on:
    * SL_GC (in-dataset)
    * EN_GC (cross-lingual)

2. train on MT_GC, test on:
    * MT_GC (in-dataset)
    * EN_GC (cross-dataset)

3. train on EN_GC, test on:
    * EN_GC (in-dataset)
    * SL_GC (out of interest - cross-lingual experiment in the other direction)
    * MT_GC (out of interest - cross-dataset experiment in the other direction)

## Preparation of the corpora
* I used only instances, labeled with GINCORE labels that are present in all datasets: 12 GINCORE labels.
* I performed a stratified split 60:20:20 according to the label distribution on all three datasets.

### CORE
* discarded 6.918 texts with multiple labels,
* discarded 6.844 texts with a fuzzy label
* mapped (15) GINCORE labels to the original CORE labels, used only labels that will be used in GINCO as well

Final number of texts:  texts, train-dev-test split: 

### Slovene GINCO
* joined paragraphs into one block of text per document, used only paragraphs that are marked to be useful with the attribute "keep" (as I realised that using deduplicated paragraphs was incorrect, as in the annotation procedure, annotators never considered just the deduplicated paragraphs - the labels on the deduplicated text might not be correct. If we use deduplicated text, some instances have no text or less than 10 words.)
* discarded labels with less than 5 instances, used only labels that appear in CORE as well

Final number of texts:  texts, train-dev-test split: 

### Machine-translated GINCO
* used the same text (paragraphs with attribute "keep") as in the Slovene GINCO, performed machine translation into English on it. I used the DeepL machine translation system. As target language, British variety of English was used, as the English CORE corpus is derived from the General section of the GloWbE corpus (Corpus of Global Web-based English) where British variety is more present than American considering number of web pages (i.e. web documents), although they are almost equally distributed, considering number of words (see https://www.english-corpora.org/glowbe/). The prevalence of the British variety was confirmed with the American-British-variety Classifier:

Variety distribution in CORE:
|     |   variant |
|:----|----------:|
| B   |     13884 |
| UNK |      9693 |
| A   |      8714 |
| MIX |      2557 |

In percentages:
|     |   variant |
|:----|----------:|
| B   | 0.398416  |
| UNK | 0.278151  |
| A   | 0.250057  |
| MIX | 0.0733758 |

 * discarded labels with less than 5 instances, used only labels that appear in CORE as well

Final number of texts: texts, train-dev-test split: 

### Hyperparameters

* separate set (and hyperparameter search) for when training on CORE -> dev set should be used for training
* separate set (and hyperparameter search) for when training on SL_GC and MT_GC -> dev set should be used for training

## TO DO:
* prepare the datasets once again, discarding the labels that are not present in both GINCO and CORE - so that we have comparable number of labels

* set up the code for hyperparameter search


## Further research

* merging SL_GC and EN_GC for training, eval still separately (for comparison with above)
* using different MT systems: repeating experiments with MT_GC on different versions of machine-translated corpora (produced with different MT systems)