# RepeatScout

RepeatScout is a software tool for the de novo identication of repetitive element families in DNA sequences.

## Description

This repository contains the Dfam consortium maintained version of RepeatScout. The original RepeatScout 
software was developed by Price A.L., Jones N.C. and Pevzner P.A. and described in detail in the following paper:

Price, Alkes L., Neil C. Jones, and Pavel A. Pevzner. 
"De novo identification of repeat families in large genomes." 
Bioinformatics 21.suppl_1 (2005): i351-i358.

RepeatScout identifies repeats by first identifying short, exact matches (seeds).  The seed consensus is extended
into the flanking regions by peforming (banded) pairwise alignment between each seed location and each possible
consensus extension {A,C,G,T}.  A "fit-preferred" alignment score is calculated for each extension, capping the
penalties for partial repeat instances in the extending set.  The most abundant seeds are processed first, and
following extension the consensus is used to update the seed counts to reduce the chance that a family is 
rediscovered in subsequent iterations.  In addition, seeds instances are only counted if they are a minimum
distance apart, to reduce the chance of building consensi for satellite repeats.

The output of RepeatScout is a FASTA file containing the consensus sequences of the repeat families identified.

We have made some modifications to the original RepeatScout code to improve its performance and to make it easier
to use.  These include:

* Adding boundary checks to the seed extension process to prevent seeds from extending across sequence boundaries.
* Switched post-processing simple-repeat checking from NSEG to DUSTMASKER.
* Fixed a few minor bugs in the scripts/code.

## Getting Started

### Dependencies

* C-compiler, Make
* Perl 5.5 or better (see http://www.perl.com)
* dustmasker (part of NCBI BLAST+ - ftp://ftp.ncbi.nlm.nih.gov/blast/executables/)
* trf (Tandem Repeats Finder - http://tandem.bu.edu/trf/trf.html)

### Installing

To build the RepeatScout software, follow these steps:
* download the source code tarball RepeatScout-#.#.#.tar.gz 
     from https://github.com/Dfam-consortium/RepeatScout/releases
* gunzip and untar it (e.g., tar -xvfz RepeatScout-#.#.#.tar.gz).  
     A directory named RepeatScout-### will be created.
* build the software by typing the following commands:
```
cd RepeatScout-###
make
```
This will generate two programs: build_lmer_table and RepeatScout.
You may leave these binaries where they are or copy them to any other location; 
no external libraries are needed.

### Executing program

Running RepeatScout proceeds in four phases.  First, build_lmer_table
creates a file that tabulates the frequency of all l-mers in the
sequence to be analyzed.  Second, RepeatScout takes this table and
the sequence and produces a fasta file that contains all the repetitive
elements that it could find.  Third, the "filter-stage-1.prl" script
is run on the output of RepeatScout to remove low-complexity and
tandem elements; RepeatMasker is run on the sequence of interest using
this filtered RepeatScout library.  The program "filter-stage-2.prl"
then filters out any repeat element that does not appear a certain number
of times (by default, 10).  Finally, the locations of the repeats found
by RepeatMasker are used, in conjuction with GFF files that describe
segmental duplications or exons or other such "uninteresting" regions
to remove sequences from the library that are likely to not be mobile
elements; the program "compare-out-to-gff.prl" does exactly this.

The RepeatScout program requires a substantial amount of memory
and a fair amount of time.  On the human X chromosome, it requires
approximately _ hours on a 3 Ghz PC while using _ Gb of memory.  We are
currently working on a way to decrease the memory usage of the program so
that it can run on much larger sequences (whole genomes) in a reasonable
amount of time.

You can see a list of command line parameters for each program by calling
the program with the "--h" flag.

### Parameter Choices

The repeat library, as constructed in the paper, was created with the
parameter "-stopafter" set to 500.  ("-stopafter" is essentially how far
RepeatScout will continue searching for an alignment when the alignment
score is not improving.)  The current default value for this parameter
is 100, which decreases running time significantly.

The default value of l, which is the "length of l-mer to consider", is set
to be ceil(log_4(L)+1) where:
  ceil(x) = smallest integer greater than x
  log_4(x) = log base 4 of x
  L is the length of the input sequence
This value can be adjusted by giving the "-l" parameter, but it is essential
that the same value of -l be given to both build_lmer_table and RepeatScout.
It is not clear that values of l other than the default are sensible, but
the options are there if you need them.

See the help file for the RepeatScout program (--h) for a list of other tunable
parameters.


## Authors

Original Code Authors:
* Alexander L. Price
* Neil C. Jones
* Pavel A. Pevzner

Contributors to the Dfam consortium maintained version of RepeatScout include:
* Robert Hubley
* Arian Smit


## Version History

* 1.0.6  
    * Switched filter-stage-1.prl from deprecated NSEG to the
        DUSTMASKER tool.
* 1.0.5  
    * Bug fix to filter-stage-1.prl reported by Eric Ganko.
* 1.0.4  
    * filter-stage-1.prl skipped the last sequence in the input file.  
      Thanks to Gyorgy Abrusan for reporting this.
* 1.0.3
    * Sequence boundaries are now honored in calculations.  The previous
      version concatenates the sequences together and allowed seeds to 
      extend across sequence boundaries. 
      ( Submitted by Robert Hubley, Institute for Systems Biology
       <rhubley@systemsbiology.org> )
* 1.0.2
    * Bug fix to handle IUPAC codes in build_lmer_table
* 1.0.1
    * Bug fix (parameter settings)

## Original Code

Original code/links include [although some appear to be broken]:
*  http://www-cse.ucsd.edu/groups/bioinformatics/software.html
*  http://repeatscout.bioprojects.org/
*  http://bix.ucsd.edu/repeatscout/

