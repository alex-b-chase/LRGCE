# LRGCE

Reference Packages used to analyze the bacterial leaf litter community at Loma Ridge, Irvine, CA, USA

As most common metagenomic pipelines are often biased towards host-associated ecosystems, we developed this curated reference database to quantify soil microbial communities. From almost 80,000 genomes available in the [PATRIC](https://www.patricbrc.org/) database, we created a curated database of representative genomes consisting of 3,019 genomes representing 1,464 genera spanning the entire bacterial domain. When possible, we prioritized genomes isolated from soil habitats. In addition, we included a robust taxonomic representation in the common soil bacterium, Streptomyces (N=54) and Curtobacterium (N=17).

For more information, check out our paper in [XXX](journalwebsite.com). And 
if you really like our approach and wish to use this reference database, please cite us:

>Chase AB, Karaoz U, Brodie EL, Gomez-Lunar Z, Martiny AC, Martiny JBH. 2017. Microdiversity of an abundant terrestrial bacterium encompasses extensive variation in ecologically-relevant traits.


# Reference Phylogeny: 
Multilocus phylogenetic analysis using a concatenated alignment of 29 conserved [single-copy marker genes](http://journals.plos.org/plosone/article?id=10.1371/journal.pone.0077033) 

[Tree](https://github.com/alex-b-chase/LRGCE/blob/master/concat.aligned.filtered.tre) File and Multiple-Sequence Alignment ([MSA](https://github.com/alex-b-chase/LRGCE/blob/master/concat.aligned.filtered.fa.zip)) for Reference Tree

Visualization of Reference Phylogeny colored by Phyla:

<img src="reference_tree.jpg" width="600" align="middle"/>

# Reference Genomes
List of [Genomes](https://github.com/alex-b-chase/LRGCE/blob/master/concat.aligned.filtered_ids.txt) in reference database - can be found in any of the above reference protein packages (all the same):


# PPLACER
You will need to run each marker gene independently. If you have access to a HPC, you can parallize these easily.

To use with [pplacer](http://matsen.fhcrc.org/pplacer/):

1. align filtered reads from metagenome to reference package using [clustal omega](http://www.clustal.org/omega/):

```bash
REFDIR=/wherever/you/downloaded/the/refpackages
REF=<reference package used>
protein=<filteredMGreads.faa>
OUTPUT=<designate output filename>

clustalo-1.2.0 --profile1 $REFDIR/$REF".refpkg"/$REF".clustalo.aln" -i $protein -o $OUTPUT.fa
```

2. test pplacer to make sure it will run all the way through. Pplacer is installed with [macqiime](http://www.wernerlab.org/software/macqiime) - can just source it directly from there

```bash
/macqiime/bin/pplacer --pretend -c $REFDIR/$REF".refpkg" $OUTPUT.fa
```

>Found reference sequences in given alignment file. Using those for reference alignment.

>Pre-masking sequences... sequence length cut from 464 to 284.

>everything looks OK.


3. if everything is OK, can run!

```bash
/macqiime/bin/pplacer -c $REFDIR/$REF".refpkg" $OUTPUT.fa -p --keep-at-most 20
```

4. use [guppy](https://matsen.github.io/pplacer/generated_rst/guppy.html) to get information from the results

```bash
/macqiime/bin/guppy to_csv --point-mass --pp $OUTPUT.jplace > $OUTPUT.csv
```

5. (optional) get visualization of all placed reads

```bash
/macqiime/bin/guppy fat --node-numbers --point-mass --pp $OUTPUT.jplace
```
