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

2. `$awk -F "\t" '{print NF; exit} `  
15 columns

**Comitting changes in README files**  
`$git commit -m "changes in README file"`

**Push changes to repository**  
`$ git push origin master`

###Data processing
