# Genome Selection for Grape Seedlessness
![Update](https://img.shields.io/badge/Update-31/07/2024-green?logo=github)
![Author](https://img.shields.io/badge/Author-Xu.Wang-orange)
![Email](https://img.shields.io/badge/Email-571720850@qq.com-blue?)

![Genome Selection](https://github.com/Immortal2333/Genome_Selection_for_Grape_Seedlessness/blob/main/WorkFlow.png)

## Data preparation
**Input Data**
* VCF File

**Dependency**
The detials of all tools can be available in their **offical website** as followed and most of them can quickly install using [Anaconda](https://anaconda.org/):
* [Beagle](https://anaconda.org/bioconda/beagle), [bedtools](https://anaconda.org/bioconda/bedtools), [sklearn](https://scikit-learn.org/)
* [bgzip](https://anaconda.org/bioconda/pbgzip), [bcftools](https://anaconda.org/bioconda/bcftools)

## VCF impute
Please install [Beagle](https://anaconda.org/bioconda/beagle) and download `beagle.21Apr21.304.jar` for the subsequent analysis.

If you want to predict the seedless phenotype of your own samples, please add your VCF file to our VCF file using `bgzip` and `bcftools`.
```
i="your.vcf"

bgzip -c -f -@ 2 $i > $i.gz
bcftools index -t $i.gz
bcftools index -t all_miss0.8GQ20maf0.0001.id.vcf.gz

bcftools merge $i.gz all_miss0.8GQ20maf0.0001.id.vcf.gz -O z >  merge.vcf.gz

unzip merge.vcf.gz
```
This step is to fill the absent value (./.) in your VCF file by using similar varities. 
```
ln -s /your/path/to/beagle.21Apr21.304.jar
java -Xmx50g -jar beagle.21Apr21.304.jar nthreads=10 gt=merge.vcf out=merge.beagle
```


# Code availability and Citation
All scripts could be found in this repository above. If you have any question, please do not hesitate contact us. Xu Wang: 571720850@qq.com

Wang, Xu, et al. "Integrative genomics reveals the polygenic basis of seedlessness in grapevine" ***Current Biology*** (2024). doi: [https://doi.org/10.1016/j.cub.2024.07.022](https://doi.org/10.1016/j.cub.2024.07.022)
