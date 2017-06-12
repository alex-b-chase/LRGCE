# LRGCE

Reference Packages used to analyze the bacterial leaf litter community.

Reference tree:
![](https://github.com/alex-b-chase/LRGCE/blob/master/chase_suppfigure1.pdf)

To use with pplacer:

1. align filtered reads from metagenome to reference package:

$ clustalo-1.2.0 --profile1 $REFDIR/$REF".refpkg"/$REF".clustalo.aln" -i $protein -o $OUTPUT.fa

2. test pplacer to make sure it will run all the way through. Pplacer is installed with macqiime - can just source it directly from there

$ /macqiime/bin/pplacer --pretend -c $REFDIR/$REF".refpkg" $OUTPUT.fa

>Found reference sequences in given alignment file. Using those for reference alignment.

>Pre-masking sequences... sequence length cut from 464 to 284.

>everything looks OK.


3. if everything is OK, can run!

$ pplacer -c $REFDIR/$REF".refpkg" $OUTPUT.fa -p --keep-at-most 20

4. use guppy to get information from the results

$ guppy to_csv --point-mass --pp $OUTPUT.jplace > $OUTPUT.csv

5. (optional) get visualization of all placed reads

$ guppy fat --node-numbers --point-mass --pp $OUTPUT.jplace
