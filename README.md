# LRGCE

Reference Packages used to analyze the bacterial leaf litter community at Loma Ridge, Irvine, CA, USA

As most common metagenomic pipelines are often biased towards host-associated ecosystems, we developed this curated reference database to quantify soil microbial communities. From almost 80,000 genomes available in the [PATRIC](https://www.patricbrc.org/) database, we created a curated database of representative genomes consisting of 3,019 genomes representing 1,464 genera spanning the entire bacterial domain. When possible, we prioritized genomes isolated from soil habitats. In addition, we included a robust taxonomic representation in the common soil bacterium, Streptomyces (N=54) and Curtobacterium (N=17).

For more information, check out our paper in [mBio](https://doi.org/10.1128/mBio.01809-17). And 
if you really like our approach and wish to use this reference database, please cite us:

>Chase AB, Karaoz U, Brodie EL, Gomez-Lunar Z, Martiny AC, Martiny JBH. 2017. Microdiversity of an abundant terrestrial bacterium encompasses extensive variation in ecologically-relevant traits.mBio 8:e01809-17.


# Reference Phylogeny: 
Multilocus phylogenetic analysis using a concatenated alignment of 29 conserved [single-copy marker genes](http://journals.plos.org/plosone/article?id=10.1371/journal.pone.0077033) 

Visualization of Reference Phylogeny colored by Phyla:

<img src="reference_tree.jpg" width="600" align="middle"/>

Raw Data:
[Tree File](https://github.com/alex-b-chase/LRGCE/blob/master/concat.aligned.filtered.tre) and Multiple-Sequence Alignment ([MSA](https://github.com/alex-b-chase/LRGCE/blob/master/concat.aligned.filtered.fa.zip)) for Reference Tree

# Reference Genomes
List of [Genomes](https://github.com/alex-b-chase/LRGCE/blob/master/concat.aligned.filtered_ids.txt) in reference database - can be found in any of the above reference protein packages (all the same):


# Running the Analysis
You will need to run each marker gene independently. If you have access to a HPC, you can parallelize these easily.

You will first need to filter your metagenomic libraries for each marker gene. You will have needed to translated all your reads to amino acid format. There are plenty of options out there for doing this quickly, including [FragGeneScanPlus](https://github.com/hallamlab/FragGeneScanPlus) and our recommendation, [Prodigal](https://github.com/hyattpd/Prodigal) using the -m option.

We recommend using the [BLASTp](https://github.com/alex-b-chase/LRGCE/blob/master/blastDB) database for an initial filter. This will be the most time consuming step.

```bash
REFDIR=/wherever/you/downloaded/the/refpackages
protein=<reference protein package>
INFILE=<MGreads.faa>

blastp -query $INFILE -db $BLASTDB/totalmarkergene -outfmt 6 -max_target_seqs 2 -evalue .00001 -num_threads 4 > $INFILE.blast.txt

# subset the blast file for each protein
cat $INFILE.blast.txt | grep $protein > $protein.blast.txt
```

The text file we just generated will be a list of sequence IDs that we can parse out from the MG file - wrote a python script [here](https://github.com/alex-b-chase/random_scripts/blob/master/search-fasta.py)

```bash
search-fasta.py -i $INFILE -o $INFILE2 -m $protein.blast.txt
```

Next, you can add a secondary filter using [HMMer](http://hmmer.org/) against the [hmmprofiles](https://github.com/alex-b-chase/LRGCE/blob/master/hmmprofiles)

```bash
hmmsearch --tblout $protein.hmm.txt -E 1e-10 --cpu 4 $HMMPROFILE/$protein.hmm $INFILE2
```

You can use the .txt output files from each to subset the reads accordingly. output=$FINFILE.faa

# PPLACER
To use with [pplacer](http://matsen.fhcrc.org/pplacer/):

1. align filtered reads from metagenome to reference package using [clustal omega](http://www.clustal.org/omega/):

```bash
FINFILE=<filteredMGreads.faa>
OUTPUT=<designate output filename>

clustalo-1.2.0 --profile1 $REFDIR/$protein".refpkg"/$protein".clustalo.aln" -i $FINFILE -o $OUTPUT.fa
```

2. test pplacer to make sure it will run all the way through. Pplacer is installed with [macqiime](http://www.wernerlab.org/software/macqiime) - can just source it directly from there

```bash
/macqiime/bin/pplacer --pretend -c $REFDIR/$protein".refpkg" $OUTPUT.fa
```

You will see something like this:

>Found reference sequences in given alignment file. Using those for reference alignment.

>Pre-masking sequences... sequence length cut from XXX to XXX.

>everything looks OK.


3. Everything is OK, can run!

```bash
/macqiime/bin/pplacer -c $REFDIR/$protein".refpkg" $OUTPUT.fa -p --keep-at-most 20
```

4. use [guppy](https://matsen.github.io/pplacer/generated_rst/guppy.html) to get information from the results

```bash
/macqiime/bin/guppy to_csv --point-mass --pp $OUTPUT.jplace > $OUTPUT.csv
```

5. (optional) get visualization of all placed reads

```bash
/macqiime/bin/guppy fat --node-numbers --point-mass --pp $OUTPUT.jplace
```
