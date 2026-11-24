# dbo_marver_mb

12S MarVer1 metabarcoding analysis of eDNA samples from the Northern Bering Sea/Chukchi Sea DBO Stations 

## 1. the data 

MarVer1 metabarcoding reads from eDNA samples collected at DBO stations/moorings in the NBS and Chukchi Sea
- SKQ2023 - Sept 2023 - MiSeq run date: 20250423
- SKQ2024 - 2024 - MiSeq run date: 20260629

MarVer primer sequences 
MarVer1_F: CGTGCCAGCCACCGCG
MarVer1_R: GGGTATCTAATCCYAGTTTG

## 2. preprocessing 

### SKQ2023: processed on eDNA VM
### SKQ2024: processed locally 

Step 1. cutadapt: identify and remove primer regions
Step 2. DADA2: filter and merge reads using DADA2 
  - using "1_sequence_filtering.Rmd"
  - outputs include: 
    *track.csv: documents input vs output read count per replicate through DADA2
    *seqtab.csv: ASV by replicate table 
    *myasvs.fasta: ASV sequences 
    *ASVtable.csv: ASV by replicate table (using short ASV# headers)
    *asv_id_table.csv: ASV id and sequence  
  
Step 3. create hashing for SKQ2023 and SKQ2024 datasets 
  - using "2_hashing.Rmd"
  - "hash_table.csv" includes read counts x hash 

## 3. taxonomic assignment

Step 1. perform taxonomic assignment using MIDORI2 database
conda activate cutadptenv
blastn -query output/combined/conv_file.fasta \
       -db /Users/kimberly.ledger/Documents/metaprobes_BeringSea2024/MIDORI2_UNIQ_NUC_GB267_srRNA_BLAST_db/MIDORI2_UNIQ_NUC_GB267_srRNA_BLAST_db \
       -out output/combined/midori2_blastnresults \
       -perc_identity 96 -qcov_hsp_perc 100 \
       -outfmt "6 qseqid sseqid pident length mismatch gapopen qstart qend sstart send evalue bitscore sscinames staxids" \
       -num_threads 10
       
Step 2. filter/curate taxonomic assignments 
  - using "3_taxonomic_assignment_MIDORI2.Rmd"
