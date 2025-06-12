# dbo_marver_mb

12S MarVer1 metabarcoding analysis of eDNA samples from the Northern Bering Sea/Chukchi Sea DBO Station 

## 1. the data 

MarVer1 metabarcoding reads from eDNA samples collected at DBO stations/moorings in the NBS and Chukchi Sea
- SKQ2023 - Sept 2023 

## 2. preprocessing 

### uploaded raw fastq files to eDNA VM 
AKCJM146-KL22:~ kimberly.ledger$ scp Documents/20250423_DBO23_marver/* kimberly.ledger@161.55.97.134:/genetics/edna/rawdata/20250423_DBO23_marver

### trim reads using cutadapt v4.2
* if cutadapt is not already installed: (conda create -n cutadaptenv cutadapt)
* activate enivronment: (conda activate cutadaptenv) 

MarVer sequence 
MarVer1_F: CGTGCCAGCCACCGCG
MarVer1_R: GGGTATCTAATCCYAGTTTG

set DATA= to the directory with the rawdata files:   
DATA=/genetics/edna/rawdata/20250423_DBO23_marver

below, the first set of () creates an array, containing n elements of the desired trimmed and unique names:   
NAMELIST=$(ls ${DATA} | sed 's/e*_L001.*//' | uniq)
echo "${NAMELIST}"

* make folder for trimmed reads: (mkdir trimmed)

iterate over all elements in the above array:   
for i in ${NAMELIST}; do
   cutadapt --discard-untrimmed -g CGTGCCAGCCACCGCG -G GGGTATCTAATCCYAGTTTG -o trimmed/${i}_R1.fastq.gz -p trimmed/${i}_R2.fastq.gz "$DATA/${i}_L001_R1_001.fastq.gz" "$DATA/${i}_L001_R2_001.fastq.gz";
done

### filter and merge reads using DADA2 
- See "1_sequence_filtering.Rmd"
- "output/track.csv" documents input vs output read count per replicate through DADA2 

## 3. taxonomic assignment 
- Ran blastn.sh on SEDNA using NCBI nt database (pulled on Jul 11, 2024) as reference and -perc_identity 96 -qcov_hsp_perc 98
- Output is "output/blastn_taxlineage.txt"
- filtered taxonomic assignments using "2_taxonomic_assignment_blastn.Rmd"

## 4. post asv curation 
ran "3_lulu.Rmd" for post asv curation. this reduces the number of ASVs by identifying ASVs that likely sequencing errors of more abundant ASVs and merges them.  
## 5. joined taxonomic assignments to asv table  
ran "4_taxon_summary.Rmd" to merge taxonomic assignment to asv table and generate some basis summary metrics and figures 
