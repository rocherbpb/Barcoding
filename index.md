## Basic COI barcoding analysis
### COI Barcode data
Raw data will typically consist of 658bp sequence from the COI locus acquired via [Folmer et al (1994)](https://pubmed.ncbi.nlm.nih.gov/7881515) primers. 

### Blast analysis

Sequences are checked via nucleotide blast analysis to determine whether blast matches are consistent with field/morphological identification. When consistent, a COI Barcode reference sequence of the species is included for downstream species delimitation analysis. Any mismatches that occur are noted, and the sequence of the Blast matched species is included in downstream analsysis. Where no field/morphological identification is available, sequences are tentatively identified as matched species. 

Fasta sequences are entered in the test area under in the "Enter Query Sequence" box in [Nucleotide Blast](https://blast.ncbi.nlm.nih.gov/Blast.cgi?PROGRAM=blastn&PAGE_TYPE=BlastSearch&BLAST_SPEC=&LINK_LOC=blasttab&LAST_PAGE=blastn). The "standard databases" are searched for "Highly similar sequences."

### Exploratory data analysis with trees and networks
The neighbor-joining (nj) method is frequently used to explore the clustering of sequences data. Trees and networks constructed from this mehtod can be used to test species hyptheses, and identify where species splitting or merging may be occurring. In the case of COI Barcoding analysis, they are typically constructed using K2P distance matrices, and the distance between sequences and tree/network clusters has been frequently used to delimit species-level differences i.e. ~2% K2P distance has been used as a crude species threshold ([Hebert et al 2003a](https://royalsocietypublishing.org/doi/10.1098/rspb.2002.2218), [Hebert et al 2003b](https://royalsocietypublishing.org/doi/10.1098/rsbl.2003.0025)). 

Within a sequence dataset of very closely related species, for example when dealing with data from species complexes, relationships may be better explained by a network rather than a tree. The neigbor-joining network method of [Huson & Bryant 2006](https://academic.oup.com/mbe/article/23/2/254/1118872) offers will display a nj tree when a tree-like signal dominates the seuqnce dataset, whereas it will recover a network (with its conflicting signals) when the data-set is not tree-like. 
The standard NJ K2P tree can be contructed in [MEGA](https://academic.oup.com/bib/article/5/2/150/330185), while the NJ Network method can be contructed in [SplitsTree](https://uni-tuebingen.de/fakultaeten/mathematisch-naturwissenschaftliche-fakultaet/fachbereiche/informatik/lehrstuehle/algorithms-in-bioinformatics/software/splitstree/)

### Phylogenetic relationships
Two methods of phylogenetic reconstruction are typically used: Maximum Likelihood (ML) and Bayesian methods.
#### Maximum likelihood analysis
The ML method can be implemented in [IQ-Tree](http://www.iqtree.org/). Optimum models and partitions for the sequence alignment can also be found using IQ-Tree.    

Potential ways to partition the COI barcode sequence alignment are leave it unpartitioned or partition by codon position. Finding an optimal model for the unpartitioned alignment can be implemented with the following commands:
 

```markdown

## Model selection (unpartitioned)
iqtree -s input.fasta -m MF

```
The range of models tested can be limited to only those implemented in MrBayes using the following commands:
```markdown

## Model selection (unpartitioned) using only models available in MrBayes
iqtree -s input.fasta -m MF -mset mrbayes

```
The optimal models are output in the ".best_scheme.nex" file.

Below is an example of a nexus format codon partition file (the COI barcode sequence will typically begin on Codon position 3):

```markdown
#nexus
begin sets;
  charset cd1 = 2-.\3;
  charset cd2 = 3-.\3;
  charset cd3 = 1-.\3;
end;

```
This partition file is then used in the following commands to determine optimal models for the partitioned alignment:
```markdown

## Model selection (partitioned)
iqtree -s input.fasta -p partition.file -m MF

```
The more likely partition can be determined by finding the one with the lower BIC scores (difference should be greater than 10). 

The optimal partition and models can then be specified in the reconstruction of an ML tree using the following commands:

```markdown
## ML tree recontruction (with ultrafast boostrap appromimation; 1000 reps) with optimal models (from optimal partition) specified in "optimal_partition.best_scheme.nex".
iqtree -s input.fasta -p optimal_partition.best_scheme.nex -m MFP -B 1000

```

#### Bayesian analysis
Bayesian phylogenetic analysis can be implemented in [MrBayes](http://nbisweden.github.io/MrBayes). Here we can implement the optimum partition models obtained from IQ-Tree (using -mset mrbayes). A typical starting point for this analysis consists of two simultaneous runs, each of which were run with four Metropolis-coupled Markov chain Monte Carlo for 10 million steps, a relative burn-in fraction of 25% and a chain temperature of 0.1. Convergence is considered reached when the average standard deviation of split frequencies between the two simultaneous runs reached 0.01, and the potential scale reduction factor values were all approximately 1.0 in the post-burn-in samples. 

Below is an example block that can be appended to a nexus format alignment to create a MrBayes input file:
```markdown
begin mrbayes;
set autoclose=yes nowarn=yes;
  charset cd1 = 2-.\3;
  charset cd2 = 3-.\3;
  charset cd3 = 1-.\3;

  partition p1 = 3:cd1, cd2, cd3;

  set partition = p1;
  unlink revmat=(all) statefreq=(all) shape=(all) pinvar=(all)  tratio=(all);
  prset applyto=(1) statefreqpr=dirichlet(1,1,1,1);
  prset applyto=(2) statefreqpr=dirichlet(1,1,1,1);
  prset applyto=(3) statefreqpr=dirichlet(1,1,1,1);

  lset applyto=(1) nst=6 rates=invgamma; 
  lset applyto=(2) nst=2 rates=propinv; 
  lset applyto=(3) nst=6; 

log start filename=test.log replace;
mcmc ngen=10000000 samplefreq=10000 printfreq=10000 nchains=4 filename=test temp=0.10;
sump relburnin=yes burninfrac=0.25 filename=test;
sumt relburnin=yes burninfrac=0.25 contype=allcompat conformat=simple filename=test;
end;
```
The models specified for codon positions 1, 2 and 3 are GTR+I+G, HKY+I and GTR, respectively.

See also [here](https://gist.github.com/brantfaircloth/895282) for additional model specifications.

### Species delimitation 
Rather than attempt to discrimtate species by eyeballing branches from a phylogenetic tree, several methods have been developed to implement a more objective and automated approach to species delimiation.
#### ABGD
The method of [Puillandre et al. 20012](https://onlinelibrary.wiley.com/doi/abs/10.1111/j.1365-294X.2011.05239.x) estimates an intra-specific divergence limit in order to detect a "barcode gap" (first significant gap beyond this divergence estimate) and partition the data into groups. Limit and gap inferences are then applied to each group until there is no further partitioning. This method is implemented in Automatic Barcode Gap Discovery (ABGD), found ([here](https://bioinfo.mnhn.fr/abi/public/abgd/abgdweb.html)).

#### mPTP
multi-rate Poisson Tree Processes ([mPTP](https://academic.oup.com/bioinformatics/article/33/11/1630/2929345)) is a tree-based or “phylogeny-aware” method that uses differences in mutation rate in a phylogenetic tree to resolve interspecific and intraspecific diversity. The phylogeneitc trees reconstructed above can be used as the input tree for this analysis. The analysis requires a strictly bifurcating tree, so a Maximum-likelihood tree from IQ-Tree or a Bayesian tree set with Contype=Allcompat from MrBayes is suitable. Minimum branch length was estimated (--minbr_auto) to control for enforced non-zero branch lengths. This tool can be downloaded [here](https://mptp.h-its.org) or used via the [webserver](https://mptp.h-its.org).

#### RESL
Refined Single Linkage (RESL) clusters sequences into operational taxonomic units (OTUs) using a graph analytical approach ([Ratnasingham & Hebert, 2013](https://journals.plos.org/plosone/article?id=10.1371/journal.pone.0066213)). Analysis is based on a p-distance matrix using the BOLD aligner, implemented in the [Barcode of Life Data (BOLD) Data System v4](http://www.boldsystems.org).

For each sequence, a BOLD record is first created within a project and the corresponding sequence data is then uploaded to this record. Sequences are then selected (by project or record) and RESL analysis performed by selecting "cluster sequences" from the "sequence analysis" tools. 
