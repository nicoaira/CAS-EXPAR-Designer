# CAS-EXPAR-Designer

___


![alt text](https://github.com/nicoaira/CAS-EXPAR-Designer/blob/main/img/logo.png?raw=true "Logo")
___

EXPAR (EXPonential Amplification Reaction) is an isothermal amplification reaction first described by [Jeffrey Van Ness et al in 2003](https://www.pnas.org/doi/10.1073/pnas.0730811100).

The reaction start when a sequence termed "trigger-X" is present in the sample, which hybridizes to the complementary sequence called Template-X'-X'. This creates and exponential amplification loop in which this trigger-X sequence get amplified in a short amount of time.

Different ways of generating this trigger-X sequences to prime the reaction have been described. For instance, one implementation for detecting SARS-CoV-2 RNA relies on the generation of a DNA-RNA hybrid that is recognized and cut by a nickase that release the trigger-X sequence
([Jake G. Carter et al, 2021](https://www.pnas.org/doi/10.1073/pnas.2100347118)).

Also, some implementations make use of the CRISPR-CAS technology to recognize the target and trigger the reaction ([Mengqi Huang et al, 2017](https://pubs.acs.org/doi/pdf/10.1021/acs.analchem.7b04542)).

The mechanism for this implementation is summarized in the following figure:

![alt text](https://github.com/nicoaira/CAS-EXPAR-Designer/blob/main/img/mechanism.png?raw=true "Mechanism")

___

The main drawback of EXPAR is its high background amplification, produced by the nonspecific trigger of the reaction. A well performing design is the one in which the positive sample amplifies in a relative short time, and the negative sample (no template control) yields a signal after a long time. A study have found that a good design of the Template X'-X' is key for obtaining a well performing reaction ([Jifeng Qian et al, 2012](https://pubmed.ncbi.nlm.nih.gov/22416064/)). In this paper, the authors measured the performance of 384 templaete-X'-X' to create a dataset that was later used for training two different classifiers. One based is based on a SVM using a position weight matrix and the other uses a RELIEF attribute evaluation with Naïve Bayes classification.

___

Herein, we generated a pipeline for designing optimal Template-X'-X' sequences.

If the user has an specific target region, the pipeline will start in step 3.

1. The user inputs the sequences which will be considered as the target of the reaction. The sequences are aligned and gaps are removed.

2. Once conserved regions are found within the alignment, variable regions are filtered out (as they would not be good as targets).

3. PAM sequences (needed for CAS binding) are found and flanking regions are stored in a dataset.

4. The sequences obtained in step 3 are analyzed for the possible formation of hairpins with a bot that uses [UNAFold](http://www.unafold.org/Dinamelt/applications/quickfold.php). The thermodynamic parameters of each sequence (number of structures, melting temperature and ΔG) are stored in the dataset.

5. The sequences are then analyzed for the formation of self-dimers with a bot that uses the [OligoAnalyzer](https://www.idtdna.com/pages/tools/oligoanalyzer) (IDT). Again, the thermodynamic parameters of the sequences are stored in the dataset.

6. The best sequences are then analyzed by three different classifiers:

* A SVM (reproduction of Jifeng Qian et al, 2012)
* A Naïve Bayes classifier (reproduction of Jifeng Qian et al, 2012)
* A deep-learning algorithm developed for this project (Deep-EXPAR).

Then, a short-list of the sequences classified as well perfoming by these models can be manually selected for experimental evaluation.


___

Accuracy of classifiers used in this pipeline:

|             | SMV   | Naïve Bayes | Deep-EXPAR* |
|-------------|:-----:|:----------:|:----------:|
| Sensitivity | 67.7% | 70.6%       | 87.3%      |
| Specificity | 80.4% | 77.5%       | 90.2%      |

*Deep-EXPAR is still being improved, so the accuracy of this model could improve in the future.
