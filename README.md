# LRGCE

Reference Packages used to analyze the bacterial leaf litter community at Loma Ridge, Irvine, CA, USA

# Reference Phylogeny: 
[Tree](https://github.com/alex-b-chase/LRGCE/blob/master/concat.aligned.filtered.tre) File and Multiple-Sequence Alignment ([MSA](https://github.com/alex-b-chase/LRGCE/blob/master/concat.aligned.filtered.fa)) for Reference Tree

Visualization of Reference Phylogeny colored by Phyla:

<img src="reference_tree.jpg" width="600" align="middle"/>

# Reference Genomes
List of [Genomes](https://github.com/alex-b-chase/LRGCE/blob/master/concat.aligned.filtered_ids.txt) in reference database - can be found in any of the above reference protein packages (all the same):


# PPLACER
To use with [pplacer](http://matsen.fhcrc.org/pplacer/):

1. align filtered reads from metagenome to reference package using [clustal omega](http://www.clustal.org/omega/):

```bash
REF=<reference package used>
protein=<filtered reads>
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
