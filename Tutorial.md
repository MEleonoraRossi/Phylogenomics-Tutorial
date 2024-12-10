# Phylogenomics practical session

By Iker Isarri and M. Eleonora Rossi

## Introduction

In this repository you will find the data, the scripts and the instructions to run the tutorial of the phylogenomics session.


## What can you do with the content of this repository?

You can use the data and instructions provided in this repository to run a phylogenomic analyses from start to finsih. The data used from this tutorial are from [this paper](https://academic.oup.com/sysbio/article/65/6/1057/2281640?login=true).
This is a toy dataset, and for time and computational constraints is much smaller than a normal phylogenomic dataset. Nevertheless the workflow provided here is complete to allowing you to design your firt phylogenomic project. 
At the end of the tutorial there is a section provided with all the links for each software we are going to you use and more. 
The extra sections provided are to give you an insight on what all the steps explained in this class comprehend.

## Data

First we need to copy this directory on our computer
```sh
git clone https://github.com/iirisarri/PEB_Phylogenomics.git
```
or download it? (Zip file?)
Once unzipped we move inside the directory 
```sh 
cd [name directoy]
```
Let's have a look on what's inside by typing the command `ls` or `ll`
In the folder `vertebrate_proteomes` you have 23 fasta files (extention .faa), these are your 23 species we are going to built the tree for. 
You can check how one of the proteomes looks like by typing 
```sh
cat chicken.faa
```

## Orthology inference
First we will infer orthology. To do so we are going to use Orthofinder2. 
You can check the helper for orthofinder by typing `orthofinder -h `
```sh 
orthofinder -os -M msa -S blast -f vertebrate_proteomes
```



## EXTRA: Pre-alignment uality filtering

## Multiple sequence alignment

## EXTRA: Alignment trimming

## Concatenate the alignments

By this point you should have performed all the steps necessary to remove paralogs, low quality sequences or species, and be pretty confident that the genes you have selected are trusty orthologs. 
You should now have the genes ready to be concatenated in a super matrix. There are many tools that you can use for this step (e.g [concat_fasta.pl](https://github.com/santiagosnchez/concat_fasta) or [catsequences](https://github.com/ChrisCreevey/catsequences)), here we are going to use [FASTCONCAT](https://github.com/PatrickKueck/FASconCAT-G), it will read any format you have for your genes (e.g. Fasta, Phylip, Nexus)



```sh
perl /software/FASconCAT-G_v1.04.pl -l -s
```

## Tree inference : Maximum likelihood

## EXTRA: Bayesian Inferene and Coalescence methods with ASTRAL

## Tree visualization

## Software links

* Orthofinder (https://github.com/davidemms/OrthoFinder)
* PREQUAL (https://github.com/simonwhelan/prequal)
* MAFFT (https://mafft.cbrc.jp/alignment/software/source.html)
* MUSCLE v5 (https://github.com/rcedgar/muscle)
* TrimAL (https://vicfero.github.io/trimal/)
* FASTCONCAT (https://github.com/PatrickKueck/FASconCAT-G)
* IQTREE2 (http://www.iqtree.org/)
* Phylobayes (https://github.com/bayesiancook/phylobayes/tree/master)
* ASTRAL (https://github.com/smirarab/ASTRAL)
* FIGTree V1.4.4 (https://github.com/rambaut/figtree/releases) 
* iTOL (https://itol.embl.de/)
