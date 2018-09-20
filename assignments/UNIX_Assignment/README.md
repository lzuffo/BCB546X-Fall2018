# UNIX Assignment


## Step 1 - Data Inspection

### 1.1 File name `fang_et_al_genotypes.txt `

* File Size:  `$ du -h fang_et_al_genotypes.txt` ; **11M**
* Number of Columns: `$ awk -F "\t" '{print NF; exit}' fang_et_al_genotypes.txt` ; **986**
* Number of lines: `$ wc -l fang_et_al_genotypes.txt` ; **2783**
* ASCII: `$ file fang_et_al_genotypes.txt
` ; **fang_et_al_genotypes.txt: ASCII text, with very long lines**

### 1.2 File name ` snp_position.txt`

* File Size: `$ du -h snp_position.txt
` ; **84K**
* Number of Columns: `$ awk -F "\t" '{print NF; exit}' snp_position.txt  `; **15**
* Number of lines: `$ wc -l snp_position.txt`; **984**
* ASCII: `$ file snp_position.txt
`; **snp_position.txt: ASCII text**

## Step 2 - Data Processing

### 2.1 Extract selected genotypes


* Maize:`$ grep -E "(ZMMIL|ZMMLR|ZMMMR)" fang_et_al_genotypes.txt > maize_genotypes.txt
`
* Teosinte: `$ grep -E "(ZMPBA|ZMPIL|ZMPJA)" fang_et_al_genotypes.txt > teosinte_genotypes.txt
`

### 2.2 Extract the header from the genotype file and add to the extracted files

`$ grep "Group" fang_et_al_genotypes.txt > header.txt`

`$ cat header.txt maize_genotypes.txt > maize_header.txt`

`$ cat header.txt teosinte_genotypes.txt > teosinte_header.txt`

### 2.3 Remove Sample ID

`$ cut -f 3-968 maize_header.txt > maize_cut.txt`

`$ cut -f 3-968 teosinte_header.txt > teosinte_cut.txt`

### 2.4 Transpose the data 

`$ awk -f transpose.awk maize_cut.txt > transposed_maize.txt`

`$ awk -f transpose.awk teosinte_cut.txt > transposed_teosinte.txt`

### 2.5 Cut columns (SNP ID, Chromossome number and SNP position in the genome)

`$ grep -v "^#" snp_position.txt | cut -f 1,3,4 > snp_position_cut.txt`

### 2.6 Remove headers from the files

`$ grep -v "Group" transposed_maize.txt > maize_no_header.txt`

`$ grep -v "Group" transposed_teosinte.txt > teosinte_no_header.txt`

`$ grep -v "SNP_ID" snp_position_cut.txt > snp_no_header.txt`

### 2.7 Sort files

`$ sort -k1,1 snp_no_header.txt > snp_sorted.txt`

`$ sort -k1,1 maize_no_header.txt > maize_sorted.txt`

`$ sort -k1,1 teosinte_no_header.txt > teosinte_sorted.txt`

### 2.8 Join SNP and Genotype files

`$ join -t $'\t' -1 1 -2 1 snp_sorted.txt maize_sorted.txt > maize_joined.txt`

`$ join -t $'\t' -1 1 -2 1 snp_sorted.txt teosinte_sorted.txt > teosinte_joined.txt`

### 2.9 Sort files by chromosome number

`$  sort -k2,2n maize_joined.txt > maize_sorted_by_chr.txt`

`$ sort -k2,2n teosinte_joined.txt > teosinte_sorted_by_chr.txt`

### 2.10 Substitute missing data "?" with "-"

`$  sed 's/?/-/g' maize_sorted_by_chr.txt > maize_dash.txt`

`$ sed 's/?/-/g' teosinte_sorted_by_chr.txt > teosinte_dash.txt`

### 2.11 Create separate files for each chromosome

`$ for i in {1..10}; do awk '$2=='$i'' maize_sorted_by_chr.txt > maize_chr"$i"_questionmark.txt; done`

`$ for i in {1..10}; do awk '$2=='$i'' teosinte_sorted_by_chr.txt > teosinte_chr"$i"_questionmark.txt; done`

`$ for i in {1..10}; do awk '$2=='$i'' maize_dash.txt > maize_chr"$i"_dash.txt; done`

`$ for i in {1..10}; do awk '$2=='$i'' teosinte_dash.txt > teosinte_chr"$i"_dash.txt; done`

### 2.12 Sort files SNPs ordered based on increasing position values and with missing data encoded by "?"

`$ for i in {1..10}; do sort -k3,3n maize_chr"$i"_questionmark.txt > maize_chr"$i"_questmark_ordered.txt; done`

`$ for i in {1..10}; do sort -k3,3n teosinte_chr"$i"_questionmark.txt> teosinte_chr"$i"_questmark_ordered.txt; done`

### 2.13 Sort files SNPs ordered based on decreasing position values and with missing data encoded by "-"

`$ for i in {1..10}; do sort -k3,3nr maize_chr"$i"_dash.txt > maize_chr"$i"_dash_ordered.txt; done`

`$  for i in {1..10}; do sort -k3,3nr teosinte_chr"$i"_dash.txt > teosinte_chr"$i"_dash_ordered.txt; done`

### 2.14 Create a file with all SNPs with unknow position in the genome

`$ grep "unknown" maize_sorted_by_chr.txt > maize_unknown.txt`

`$ grep "unknown" teosinte_sorted_by_chr.txt > teosinte_unknown.txt`

### 2.15 Create a file with all SNPs with multiple position in the genome

`$ grep "multiple" maize_sorted_by_chr.txt > maize_multiple.txt`

`$ grep "multiple" teosinte_sorted_by_chr.txt > teosinte_multiple.txt`

### 2.16 Adding files to folders

`$ mkdir Results Results/Maize_Questionmark Results/Teosinte_Questionmark Results/Maize_Dash Results/Teosinte_Dash Results/MaizeandTeosinte_UnknownPositions Results/MaizeandTeosinte_MultiplePositions/ Intermediate_files`

`$ mv maize_chr*_questmark_ordered.txt Results/Maize_Questionmark`

`$ mv teosinte_chr*_questmark_ordered.txt Results/Teosinte_Questionmark`

`$ mv maize_chr*_dash_ordered.txt Results/Maize_Dash`

`$ mv teosinte_chr*_dash_ordered.txt Results/Teosinte_Dash`

`$ mv maize_unknown.txt teosinte_unknown.txt Results/MaizeandTeosinte_UnknownPositions/`

`$ mv maize_multiple.txt teosinte_multiple.txt Results/MaizeandTeosinte_MultiplePositions/`

`$ mv m*.txt t*.txt snp_position_cut.txt snp_no_header.txt snp_sorted.txt h*.txt Intermediate_files`

### 2.17 Stage and Commit changes

` $ git add .`

`$ git commit -m "Completed assignment"`

`$ git push origin master`
