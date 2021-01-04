## COI barcoding analysis

Raw data will typically consist of 658bp sequence from the COI locus acquired via Folmer et al (1994) primers. 

### Blast analysis

Sequences are checked via nucleotide blast analysis to determine whether blast matches are consistent with field/morphological identification. When consistent, a COI Barcode reference sequence of the species is included for downstream species delimitation analysis. Any mismatches that occur are noted, and the sequence of the Blast matched species is included in downstream analsysis. Where no field/morphological identification is available, sequences are tentatively identified as matched species. 

Fasta sequences are entered in the test area under in the "Enter Query Sequence" box in [Nucleotide Blast](https://blast.ncbi.nlm.nih.gov/Blast.cgi?PROGRAM=blastn&PAGE_TYPE=BlastSearch&BLAST_SPEC=&LINK_LOC=blasttab&LAST_PAGE=blastn). The "standard databases" are searched for "Highly similar sequences."

### Exploratory data analysis with trees and networks
The neighbor-joining (nj) method is frequently used to explore the clustering of sequences data. Trees and networks constructed from this mehtod can be used to test species hyptheses, and identify where species splitting or merging may be occurring. In the case of COI Barcoding analysis, they are typically constructed using K2P distance matrices, and the distance between sequences and tree/network clusters has been frequently used to delimit species-level differences i.e. ~2% K2P distance has been used as a crude species threshold ([Hebert et al 2003a](https://royalsocietypublishing.org/doi/10.1098/rspb.2002.2218), [Hebert et al 2003b](https://royalsocietypublishing.org/doi/10.1098/rsbl.2003.0025)). 
Within a sequence dataset of very closely related species, for example when dealing with data from species complexes, relationships may be better explained by a network rather than a tree. The neigbor-joining network method of [Huson & Bryant 2006](https://academic.oup.com/mbe/article/23/2/254/1118872) offers will display a nj tree when a tree-like signal dominates the seuqnce dataset, whereas it will recover a network (with its conflicting signals) when the data-set is not tree-like. 
The standard NJ K2P tree can be contructed in [MEGA](https://academic.oup.com/bib/article/5/2/150/330185), while the NJ Network method can be contructed in [SplitsTree](https://uni-tuebingen.de/fakultaeten/mathematisch-naturwissenschaftliche-fakultaet/fachbereiche/informatik/lehrstuehle/algorithms-in-bioinformatics/software/splitstree/)

### Phylogenetic relationships
ML
MrBayes

### Species delimitation 
ABGD
PTP
RESL

```markdown
Syntax highlighted code block


# Header 1
## Header 2
### Header 3

- Bulleted
- List

1. Numbered
2. List

**Bold** and _Italic_ and `Code` text

[Link](url) and ![Image](src)
```


