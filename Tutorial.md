# Phylogenomics practical session

By Iker Isarri and M. Eleonora Rossi

## Introduction

In this repository you will find data, scripts and instructions to run the phylogenomics tutorial.

### What can you do with the content of this repository?
You can use the data and instructions provided in this repository to run a phylogenomic analyses from start to finsih. The data provided is from [this paper](https://academic.oup.com/sysbio/article/65/6/1057/2281640?login=true).
This is a toy dataset, and due to time and computational constraints is much smaller than a normal phylogenomic dataset. Nevertheless the workflow provided here is complete, allowing you to have the basics to design your phylogenomic project in the future. 
At the end of the tutorial there is a section provided with all the links for each software we are going to you use and more. 
You won't need to run the  "extra" sections, we provided them to give you an insight on all the steps you should take to infer a phylogeny.

Let's start!

## Data

First we need to copy this directory on our computer
```sh
git clone https://github.com/iirisarri/PEB_Phylogenomics.git
```
or download it? (Zip file?)
Once unzipped we move inside the directory 

```sh 
#cd stands for change directory
# cd [name directory]
cd PEB_Phylogenomics
```
Let's have a look on what's inside by typing the command `ls` (list) or `ll`.
In the folder `vertebrate_proteomes` you have 23 fasta files (extention .faa), these are your 23 species we are going to built the tree for. 
You can check how one of the proteomes looks like by typing. Move into the `vertebrate_proteomes` directory with the cd command and then type: 

```sh
less chicken.faa
```
How does it look like? 
What is the text that comes after the `>` sign?

To return to the previous directory just type `cd ..` (This command moves you one directory back).


## Orthology inference

We will infer the orhology with Orthofinder2. 
You can check the helper for orthofinder by typing `orthofinder -h`

Now let's infer some orthologs. Type the following comand from the `PEB_Phylogenomics`.

```sh 
orthofinder -os -M msa -S blast -f vertebrate_proteomes
```
It will run for a couple of minutes, because we don't have many species or a high number of sequences. 
Keep in mind that a normal phylogenomic dataset comprhend several proteomes with hundreds of sequences, which will highly influence the computational power and time needed to run orthofinder to completion. 

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
Can you count how many sequences you have in this file? 


## EXTRA: Pre-alignment and quality filtering


## Multiple sequence alignment
The next step is to infer multiple sequence alignments. Multiple sequence alignments allow us to know which amino acids/ nucleotides are homologous. A simple yet accurate tool is MAFFT.

We will align gene files separately using a for loop:

```sh
for f in *fa; 
    do mafft $f > $f.mafft.fasta; 
done
```

## EXTRA: Alignment trimming

Normally after the alignment and before the concatenation, we trim each individual alignments. There are several tools for this, the most common ones are TrimAl and BMGE.

If you want to run TrimAl:
```sh
for a in *.mafft.fasta;

	do trimal -in $a -out $a.trim  -fasta -gappyout;

done
```
[Here](https://trimal.readthedocs.io/en/latest/algorithms.html) you can check what the gappyout option does

If you want to run BMGE:
the `-g` flag allows you to remove alignment positions with > 80% gaps, you can decide which sides to exclude by changing the percentage of gaps you want to remove.

```sh
for f in *mafft.fasta; 
    do java -jar /software/BMGE.jar -i $f -t AA -g 0.8 -h 1 -w 1 -of $f.g08.fas;
 done

```

## Concatenate the alignments

By this point you should have performed all the steps necessary to remove paralogs, low quality sequences or species, and be pretty confident that the genes you have selected are trusty orthologs. 
You should now have the genes ready to be concatenated in a super matrix. There are many tools that you can use for this step (e.g [concat_fasta.pl](https://github.com/santiagosnchez/concat_fasta) or [catsequences](https://github.com/ChrisCreevey/catsequences)), here we are going to use [FASTCONCAT](https://github.com/PatrickKueck/FASconCAT-G), it will read any format you have for your genes (e.g. Fasta, Phylip, Nexus)


```sh
perl /software/FASconCAT-G_v1.04.pl -l -s
```
It will concatenated the genes in random order, at the end you will have a matrix composed of 23 taxa and 21 genes. The matrix is `FcC_supermatrix.fas`.
You can visualize the matrix using [SeaView](https://doua.prabi.fr/software/seaview) 

## Tree inference : Maximum likelihood
Now we are ready to infer our first phylogenomic tree!
One of the best and most powerful tools available at the moment to infer trees using maximum likelihood is IQTree2. You can check the manual and all the tutorials they provide [here](http://www.iqtree.org/).

Let's check the helper to see all the commands we are going to use
```sh
iqtree2 --help
```
As you can see there are lots of options. Here we will  provide the number of threads to use (`-nt 1`) input alignment (`-s`), tell IQTREE to select the best-fit evolutionary model with BIC (`-m TEST -merit BIC -msub nuclear`) and ask for branch support measures such as non-parametric bootstrapping and approximate likelihood ratio test (`-bb 1000 -alrt 1000 -bnni`).
Let's generate our first tree!

```sh
iqtree -s FcC_supermatrix.fas -m TEST -msub nuclear -bb 1000 -alrt 1000 -nt 1 -bnni -pre unpartitioned
```
You run your first tree!!

Now IQtree will generate several output file, your tree is in the `.treefile`. Check what are the other files to have an idea of their meaning. 


## EXTRA: Bayesian Inference 
The next step would be to run a Bayesian analysis using Phylobayes, however due to time constraints, we will provide you with the bayesian topology. Compare it with the ML one and check if you find any difference. 

This is the command we used for phylobayes

```sh
```
## Coalescence methods with ASTRAL

To run a coalescent analysis you can use ASTRAL. The input for ASTRAL are the single gene trees for each of the ortholouges we have infered. Astral assume that the gene trees are correct and not prone to errors, this is not always the case, nevertheless coalescent methods are useful tools to account for incomplete lineage sorting (ILS).
Astral will infer a species tree from the individual gene trees. 
Keep in mind: a gene tree follows the evolutionary history of the gene, and only orthologous genes have an evolutionary history similar to the species history since they evolve for speciation.

Thus, before running ASTRAL, we will need to estimate individual gene trees. This can be easily done calling IQTREE in a for loop:

```sh

for f in *.mafft.fasta; 
    do iqtree -s $f -m TEST -msub nuclear -merit AICc -nt 1; 
done
```

Once all the gene trees are generated you need to put them in one single file.

```sh
cat *treefile > All_genetrees.tre
```

Now you can run ASTRAL
```sh
java -jar /srv/evop/resources/astral/ASTRAL/Astral/astral.5.7.3.jar -i my_gene_trees.tre -o species_tree_ASTRAL.tre 2> out.log
```
Excellent!! You just got your ML, Bayesian, and Coalescence trees. Now visualize them. Cna you spot any differences?

## Tree visualization

For tree visualization there are different tools that you can use, such as [FigTree](https://github.com/rambaut/figtree/releases), [ITol](https://itol.embl.de/), [TreeViewer](https://treeviewer.org/) The most common is FigTree, but there are also several onlin

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
