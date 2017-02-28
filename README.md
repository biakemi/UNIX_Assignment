# UNIX_Assignment
UNIX assignment repository

###Cloning the git repository
1. `$git clone https://github.com/biakemi/UNIX_Assignment.git`
2. I added the files to the repository and checked the status by typing `$git status`
3. The files I moved to the repository were marked as "untracked files" - so I staged the files  `$git add .`
4. By typing `$git status` again, I checked that all files were staged


###Data inspection

* **File size**  

`$du -h fang_et_al_genotypes.txt snp_position.txt`

1. fang_et_al_gentypes.txt = 11M
2. snp_position.txt = 84K
	
* **Number of lines**

`$wc -l fang_et_al_genotypes.txt snp_position.txt`  

1. fang_et_al_gentypes.txt = 2782 lines
2. snp_position.txt = 984 lines

* **Number of columns**

1. `$awk -F "\t" '{print NF; exit} fang_et_al_genotypes.txt`   
986 columns

2. `$awk -F "\t" '{print NF; exit} snp_position.txt`  
15 columns

* **
* _Comitting changes in README files_  
`$ git commit -m "changes in README file"`

_Push changes to repository_  
`$ git push origin master`

* **
###Data processing

* **Extract maize data**

Maize = Groups ZMMIL, ZMMLR, ZMMMR  
`$ grep -E "(ZMMIL|ZMMLR|ZMMMR)" fang_et_al_genotypes.txt > maize_genotypes.txt`

* **Extract teosinte data**

Teosinte = Groups ZMPBA, ZMPIL, ZMPJA  
`$ grep -E "(ZMPBA|ZMPIL|ZMPJA)" fang_et_al_genotypes.txt > teosinte_genotypes.txt`

* **Extract header**

`$ grep "Group" fang_et_al_genotypes.txt > header.txt`

* **Concatenate header with extracted files**

`$ cat header.txt maize_genotypes.txt > maize.txt`
`$ cat header.txt teosinte_genotypes.txt > teosinte.txt`

* **Cut unnecessary columns**

`$ cut -f 3-968 maize.txt > cut_maize.txt`
`$ cut -f 3-968 teosinte.txt > cut_teosinte.txt`

* **Transpose the data**

`$ awk -f transpose.awk cut_maize.txt > transposed_maize.txt`  
`$ awk -f transpose.awk cut_teosinte.txt > transposed_teosinte.txt`

* **

* _Stage and commit new files_  

`$ git add .`
`$ git commit -m "new extracted and transposed maize and teosinte files"`
`$ git push origin master`

* **

* **Cut snp_position.txt columns that matter**

`$ grep -v "^#" snp_position.txt | cut -f 1,3,4 > cut_snp_position.txt`

* **Exclude header from files**  
At first I thought that having the headers would help me in joining the files, but I realized that they are making it more complicated. So I am removing them in order to make it easier.

`$ grep -v "Group" transposed_maize.txt > maize_noheader.txt`
`$ grep -v "Group" transposed_teosinte.txt > teosinte_noheader.txt`
`$ grep -v "SNP_ID" cut_snp_position.txt > snp_noheader.txt`

* **Sort files**

`$ sort -k1,1 snp_noheader.txt > sorted_snp.txt`
`$ sort -k1,1 maize_noheader.txt > sorted_maize.txt`
`$ sort -k1,1 teosinte_noheader.txt > sorted_teosinte.txt`

* **Checking if files are sorted**

`$ sort -k1,1 -c sorted_snp.txt | echo $?`
returned 0
`sort -k1,1 -c sorted_maize.txt | echo $?`
returned 0
`sort -k1,1 -c sorted_teosinte.txt | echo $?`
returned 0

* **

* _Stage and commit new files_  

`$ git add .`
`$ git commit -m "new extracted and transposed maize and teosinte files"`
`$ git push origin master`

* **

* **Join the files**

`$ join -t $'\t' -1 1 -2 1 sorted_snp.txt sorted_maize.txt > maize_data.txt`  
`$ join -t $'\t' -1 1 -2 1 sorted_snp.txt sorted_teosinte.txt > teosinte_data.txt` 

* **

* _Stage and commit new files_  

`$ git add .`
`$ git commit -m "joined files"`
`$ git push origin master`

* **


* **Sort files by chromosome number**

`$ sort -k2,2n maize_data.txt > maize_sorted_by_chr.txt`
`$ sort -k2,2n teosinte_data.txt > teosinte_sorted_by_chr.txt`

*  **Insert special characters in missing data**

1.  substituing missing data with ?
`$ sed 's/<TAB>/?/g' maize_sorted_by_chr.txt > maize_substituted.txt`  
`$ sed 's/<TAB>/?/g' teosinte_sorted_by_chr.txt > teosinte_substituted.txt`  

2.  substituing ? with -
`$ sed 's/?/-/g' maize_substituted.txt > maize_substituted_dash.txt`  
`$ sed 's/?/-/g' teosinte_substituted.txt > teosinte_substituted_dash.txt`

*  **Extract data by chromosome number**

`$ awk '$2 == /1/' maize_substituted.txt > maize_chr1.txt`  
`$ awk '$2 ~ /2/' maize_substituted.txt > maize_chr2.txt`
`$ awk '$2 ~ /3/' maize_substituted.txt > maize_chr3.txt`
... repeated for all chromosomes, input files and output files

- Name of input files with respective output files
`maize_substituted_dash.txt > maize_chr#_dash.txt`
`teosinte_substituted.txt > teosinte_chr#.txt`
`teosinte_substituted_dash.txt > teosinte_chr#_dash.txt`  

* **
* _Stage and commit new files_  

`$ git add .`
`$ git commit -m "files with missing data replaced by special characters and data divided by chromosome number"`
`$ git push origin master`

* **

* **Dividing files in folders to keep it organized, because there is too many files**

`$ mkdir raw_data extracted_transposed_data sorted_joined_data substituted_data`
`$ mv fang_et_al_genotypes.txt snp_position.txt raw_data/`
... repeated the `mv` command to others files

* **Sort files with ? according to SNP increased position values**
`$ sort -k3,3n maize_chr#.txt > maize_snp_chr#.txt`
`$ sort -k3,3n teosinte_chr#.txt > teosinte_snp_chr#.txt`

* **Sort files with - according to SNP decreasing position values**  
`$ sort -k3,3nr maize_chr#_dash.txt > maize_snp_chr#_reversed.txt`
`$ sort -k3,3nr teosinte_chr#_dash.txt > teosinte_snp_chr#_reversed.txt`

* **Organizing the unsorted files in folders**

`$ mkdir maize_chr_data teosinte_chr_data`
`$ mv maize_chr*.txt maize_chr_data/`
`$ mv teosinte_chr*.txt teosinte_chr_data/`


**Results:**  

* 20 files (1 for each chromosome), for maize and teosinte with SNPs ordered based on increasing position values and with missing data encoded by ?  
	* `maize_snp_chr#.txt` and `teosinte_snp_chr#.txt`  


* 20 files (1 for each chromosome), for maize and teosinte with SNPs ordered based on decreasing position values and with missing data encoded by -
	* `maize_snp_chr#_reversed.txt` and `teosinte_snp_chr#_reversed.txt`


* **
* _Stage and commit new files_  

`$ git add .`
`$ git commit -m "final files and complete README"`
`$ git push origin master`

* **

#R Assignment
All commands are in R notebook.
Comparing the flow work with UNIX analysis, using R was simpler due to the RStudio interface - I was able to check my results and do not get lost. Given that, I didn't do the steps of putting and removing the header as I did in UNIX, but, on the other hand, I had to include a step to make my row names as columns so I could merge the data frames.
