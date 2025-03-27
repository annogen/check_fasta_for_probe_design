# Test whether supplied sequences in fasta file(s) comply with Annogen probe design requirements

This is an R-script which tests whether sequences and sequence labels are
compliant. It can be run from the commandline or be sourced into an interactive
R-session.

## Output

### Exit status

The script returns 0 if all sequence/ID's are correct, and 1 otherwise. The
return value is returned as exit status when the script is run from the
commandline, or as return value from the function
'check_client_fasta_input(..)' when run interactively.

### Full table of test results per sequence

Each sequence/ID, and each REF/ALT set, is tested on a number of criteria. For
each test a value is returned where '0' always means the sequence/ID passed the
test correctly. A non-zero value always means the sequence/ID failed the test.


Tests:

* sequence ID's are unique
* sequences are unique
* sequences do not contain duplicated reverse complement sequences
* length of sequences are within limits
* sequence consist of standard bases only
* the sequence ID has correct format
* (sequence ID's do not contain spaces)
* sets of REF/ALT contain exactly 1 REF and >=1 ALT
* each ALT is tagged with an integer (ALT_n), which is unique within the REF/ALT set

Running the tests builds a table with rows for every sequence, and a column for
every test. This table is written to file if option -o is given


### Summary of tests

A summary of the tests is by default written to stdout (unless option '-q'),
and optionally to a log file (option -l). The summary specifies the total
number of sequences tested, and for each test the number of sequences failing
that test.

## Usage

The script takes the following arguments:

* names of fasta file(s)
* maximum length of probe sequences
* flag for REF/ALT specifications of sequences

### Script requirements

The script uses the following R-packages:

* data.table
* seqinr
* argparse
* tools

All are available on [CRAN](https://cran.r-project.org/)


### Commandline

The script can be run on linux terminal, eg:

`path/to/script/check_client_fasta_input -i file1.fa file2.fa -r -m 270 -l log.txt -q -o test_results.tsv`

where:

```
-i: input files in fasta format, file extension .fa or .fasta. More than 1 file is allowed
-r: assume REF/ALT format (default: assume std synthetic format)
-l: write summary of test results to logfile 'log.txt'
-m: maximum length of sequences. Sequences are allowed to have lower lengths up to max - 99
-q: do _not_ print summary of results to stdout
-o: print test results per sequence to file 'test_results.tsv'
```

### Interactive session

In an interactive R-session the script can be sourced. The script defines the function `check_client_fasta_input` with arguments:

* inputfile
* logfile (default = NULL)
* quiet (default = FALSE)
* max_length
* refalt (default = FALSE)
* output (default = NULL)


## Requirements of fasta input files:
- no duplicated sequences
- sequence length between supplied max length, and max length-100
- sequences contain only standard bases (A, T, C, G)
- sequence ID's:
  - must be unique
  - format (REF/ALT): {FREETEXT}_{SHORTID}_{REF|ALT_n}
  - format (SYNTHETIC): {FREETEXT}_{SHORTID}
  - {SHORTID} must be unique in synthetic case, 
    or identical among a set of a single REF and all associated ALT's
  - n in ALT_n is 1 for first ALT, 2 for second, etc.
  - (REF/ALT:) each {SHORTID} has an associated REF and one or more ALT's

Each fasta file can contain one or more sequences. 
The set of supplied fasta files make up a single library.

