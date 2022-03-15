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
* discarded 6.918 texts with multiple labels
* discarded 6.844 texts with a fuzzy label
* mapped (15) GINCORE labels to the original CORE labels, used only labels that will be used in GINCO as well --> used only instances belonging to the 12 GINCORE labels

Final number of texts: 33,918 texts, train-dev-test split: 20,350:6,784:6,784

### Slovene GINCO
* joined paragraphs into one block of text per document, used only paragraphs that are marked to be useful with the attribute "keep" (as I realised that using deduplicated paragraphs was incorrect, as in the annotation procedure, annotators never considered just the deduplicated paragraphs - the labels on the deduplicated text might not be correct. If we use deduplicated text, some instances have no text or less than 10 words.)
* discarded labels with less than 5 instances, used only labels that appear in CORE as well

Final number of texts: 810 texts, train-dev-test split: 486-162-162

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

Final number of texts: see Slovene GINCO (above)

### Labels Distribution

GINCO labels in CORE:

|                         |   labels |   final %  |
|:------------------------|----------|-----------:|
| News                    |    12658 | 0.373194   |
| Opinion/Argumentation   |     8980 | 0.264756   |
| Information/Explanation |     3406 | 0.100419   |
| Forum                   |     3108 | 0.0916328  |
| Review                  |     1687 | 0.0497376  |
| Instruction             |     1200 | 0.0353794  |
| Promotion               |     1026 | 0.0302494  |
| Research Article        |      804 | 0.0237042  |
| Lyrical*                |      636 | NA         |
| Interview               |      420 | 0.0123828  |
| Prose                   |      276 | 0.00813727 |
| FAQ*                    |      272 | NA         |
| Legal/Regulation        |      186 | 0.00548381 |
| Recipe                  |      167 | 0.00492364 |
| Script/Drama*           |       22 | NA         |


GINCO labels in (Slovene and MT) GINCO:

|                            |   GINCORE |   final %  |
|:---------------------------|-----------|-----------:|
| Promotion                  |       209 | 0.258025   |
| News                       |       204 | 0.251852   |
| Information/Explanation    |       130 | 0.160494   |
| Opinion/Argumentation      |       114 | 0.140741   |
| List of Summaries/Excerpts*|       106 | NA         |
| Forum                      |        52 | 0.0641975  |
| Instruction                |        38 | 0.0469136  |
| Other*                     |        34 | NA         |
| Review                     |        17 | 0.0209877  |
| Legal/Regulation           |        17 | 0.0209877  |
| Announcement*              |        17 | NA         |
| Correspondence*            |        16 | NA         |
| Call*                      |        11 | NA         |
| Research Article           |         9 | 0.0111111  |
| Interview                  |         8 | 0.00987654 |
| Recipe                     |         6 | 0.00740741 |
| Prose                      |         6 | 0.00740741 |
| Lyrical*                   |         4 | NA         |
| FAQ*                       |         3 | NA         |
| Script/Drama*              |         1 | NA         |


Labels, marked with an * were not used in the experiments. Some of them were dicarded because they contained less than 5 instances in GINCO (Script/Drama, FAQ, Lyrical), others were discarded because they are not present in CORE.

The final list of labels, used in the experiments:
```
LABELS = ['News', 'Forum', 'Opinion/Argumentation', 'Review', 'Research Article', 'Information/Explanation', 'Promotion', 'Instruction', 'Prose', 'Interview', 'Legal/Regulation', 'Recipe']
```

### Hyperparameters

* separate set (and hyperparameter search) for when training on CORE -> dev set should be used for training
* separate set (and hyperparameter search) for when training on SL_GC and MT_GC -> dev set should be used for training

## TO DO:
* set up the code for hyperparameter search
* learn XML-RoBERTA on CORE, test
* learn XML-RoBERTA on SI-GINCO, test
* learn XML-RoBERTA on MT-GINCO, test

## Further research

* merging SL_GC and EN_GC for training, eval still separately (for comparison with above)
* using different MT systems: repeating experiments with MT_GC on different versions of machine-translated corpora (produced with different MT systems)