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
less chicken.faa
```
To return to the previous directory just type `cd ..`

## Orthology inference

We will infer the orhology with Orthofinder2. 
You can check the helper for orthofinder by typing `orthofinder -h`

Now let's infer some orthologs. Type the following comand from the `PEB_Phylogenomics`.

```sh 
orthofinder -os -M msa -S blast -f vertebrate_proteomes
```
It will run for a couple of minutes, we don't have many species or sequences, but keep in mind that you data will highly influence the computational power you will need to run orthofinder to completion. 

Once Orthofinder is finished let's check how many orthogroups we have found. Your orthogroups are in the folder `Orthogroups_Sequences`

From the PEB_Phylogenomics directory go to the results directory:

```sh
cd /vertebrate_proteomes/OrthoFinder/Results_Dec17
```
The list of single-copy orthologs will be in a file called `Orthogroups/Orthogroups.tsv`. This file contains lists of sequence names inferred to belong to the same orthogroups. The sequence files of these orthogroups can be found in `Orthogroup_Sequences`. Each file corresponds to one orthogroup ("gene"), containing one sequence per species. Check that!

Go to the `Orthogroup_Sequences` directory and type
```sh
less -S OG0000006.fa
```


## EXTRA: Pre-alignment and quality filtering


## Multiple sequence alignment
The next step is to infer multiple sequence alignments. Multiple sequence alignments allow us to know which amino acids/ nucleotides are homologous. A simple yet accurate tool is MAFFT.

We will align gene files separately using a for loop:

```sh
for f in *fa; do mafft $f > $f.mafft.fasta; done
```

## EXTRA: Alignment trimming

## Concatenate the alignments

By this point you should have performed all the steps necessary to remove paralogs, low quality sequences or species, and be pretty confident that the genes you have selected are trusty orthologs. 
You should now have the genes ready to be concatenated in a super matrix. There are many tools that you can use for this step (e.g [concat_fasta.pl](https://github.com/santiagosnchez/concat_fasta) or [catsequences](https://github.com/ChrisCreevey/catsequences)), here we are going to use [FASTCONCAT](https://github.com/PatrickKueck/FASconCAT-G), it will read any format you have for your genes (e.g. Fasta, Phylip, Nexus)


```sh
perl /software/FASconCAT-G_v1.04.pl -l -s
```
It will concatenated the genes in random order, at the end you will have a matrix composed of 23 taxa and 21 genes. 
You can visualize the matrix using [SeaView](https://doua.prabi.fr/software/seaview) 

## Tree inference : Maximum likelihood
Now we are ready to infer our first phylogenomic tree!
One of the best and most powerful tools available at the moment to infer trees using maximum likelihood is IQTree2. You can check the manual and all the tutorials they provide [here](http://www.iqtree.org/).

Let's check the helper to see all the commands we are going to use
```sh
iqtree2 --help
```
As you can see there are lots of options, but to generate a sensible ML tree we just need to specify the matrix we want to use, the model selection option, and the bootstrap we want to use.

Let's generate our first tree
```sh
iqtree -s FcC_supermatrix.fas -m TEST -msub nuclear -bb 1000 -alrt 1000 -nt 1 -bnni -pre unpartitioned
```

## EXTRA: Bayesian Inferene and Coalescence methods with ASTRAL

The next step would be to run a Bayesian analysis using Phylobayes, however due to time constraints, we will provide you with the bayesian topology. Compare it with the ML one and check if you find any difference. 

This is the command we used for phylobayes

```sh
```

To run a coalescent analysis you can use ASTRAL. The input for ASTRAL are the single gene trees for 



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
