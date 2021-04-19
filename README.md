# cel2vcf
Adaptation of existing tools to convert SNPArray CEL files to VCF, primarily adapted from [gtc2vcf](https://github.com/freeseek/gtc2vcf)

On the rare occasion that SNPArray files are required in VCF format, such as demultiplexing cell line data that was initially run on SNPArray chips, these steps will convert hg19 CEL files to hg38 VCF files. 

## Installation
### 1: Install Affymetrix Analysis Power tools (apt)
```
wget https://downloads.thermofisher.com/APT/APT_2.11.3/apt_2.11.3_linux_64_bit_x86_binaries.zip
unzip apt_2.11.3_linux_64_bit_x86_binaries.zip
chmod -R 775 apt_2.11.3_linux_64_bit_x86_binaries
cd apt_2.11.3_linux_64_bit_x86_binaries/bin
export PATH=$PWD:$PATH #add these tools to the path
cd ../..
```


### 2: Import Affymetrix dependencies
```
wget http://www.affymetrix.com/Auth/support/downloads/library_files/genomewidesnp6_libraryfile.zip
wget http://www.affymetrix.com/Auth/analysis/downloads/lf/genotyping/GenomeWideSNP_6/SNP6_supplemental_axiom_analysis_files.zip
wget http://www.affymetrix.com/Auth/analysis/downloads/na35/genotyping/GenomeWideSNP_6.na35.annot.csv.zip
unzip -oj genomewidesnp6_libraryfile.zip CD_GenomeWideSNP_6_rev3/Full/GenomeWideSNP_6/LibFiles/GenomeWideSNP_6.{cdf,chrXprobes,chrYprobes,specialSNPs}
unzip -o SNP6_supplemental_axiom_analysis_files.zip GenomeWideSNP_6.{generic_prior.txt,apt-probeset-genotype.AxiomGT1.xml,AxiomGT1.sketch}
unzip -o GenomeWideSNP_6.na35.annot.csv.zip GenomeWideSNP_6.na35.annot.csv
```

### 3: Install local version of bcftools
```
wget https://github.com/samtools/bcftools/releases/download/1.11/bcftools-1.11.tar.bz2
tar -xvjf bcftools-1.11.tar.bz2
rm bcftools-1.11.tar.bz2
cd bcftools-1.11/
./configure --prefix $(pwd)
```

### 4: Install the affy2vcf plugin for bcftools from [gtc2vcf](github.com/freeseek/gtc2vcf) and add the plugin to the current path
```
wget -P bcftools/plugins https://raw.githubusercontent.com/freeseek/gtc2vcf/master/{gtc2vcf.{c,h},affy2vcf.c}
ls bcftools #There should be a new bcftools/plugins directory within the bcftools-1.11 directory
mv bcftools/plugins/* plugins/
rm -rf bcftools
make
```
Test for the plugin installation by looking for gtc2vcf after running this command:
```
bcftools plugin -lv
```

### 5: Set the variables locally
```
export BCFTOOLS_PLUGINS=$PWD/plugins
export PATH=$PWD:$PATH
```
