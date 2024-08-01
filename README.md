# Genome Selection for Grape Seedlessness
![Update](https://img.shields.io/badge/Update-01/08/2024-green?logo=github)
![Author](https://img.shields.io/badge/Author-Xu.Wang-orange)
![Email](https://img.shields.io/badge/Email-571720850@qq.com-blue?)

![Genome Selection](https://github.com/Immortal2333/Genome_Selection_for_Grape_Seedlessness/blob/main/WorkFlow.png)

## Data preparation
**Input Data**
* VCF File
* CSwithmtpt.sort.fa. The reference genome for 'Cabernet Sauvignon' has been uploaded to [Zenodo](https://zenodo.org/doi/10.5281/zenodo.8278185).

**Dependency**
The detials of all tools can be available in their **offical website** as followed and most of them can quickly install using [Anaconda](https://anaconda.org/):
* [Beagle](https://anaconda.org/bioconda/beagle), [bedtools](https://anaconda.org/bioconda/bedtools), [sklearn](https://scikit-learn.org/)
* [bgzip](https://anaconda.org/bioconda/pbgzip), [bcftools](https://anaconda.org/bioconda/bcftools)

## VCF filling
For variant calling on your own samples, please refer to the pipeline: [Population Genetic Analysis Pipeline](https://github.com/Immortal2333/Population_Genetic_Analysis_Pipeline).

Please install [Beagle](https://anaconda.org/bioconda/beagle) and download [`beagle.21Apr21.304.jar`](https://github.com/Immortal2333/Genome_Selection_for_Grape_Seedlessness/blob/main/beagle.21Apr21.304.jar) for the subsequent analysis.

If you want to predict the seedless phenotype of your own samples, please merge your VCF file with our VCF file using `bgzip` and `bcftools`.
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
## Extract significant position
You can find all 794 variantions [above](https://github.com/Immortal2333/Genome_Selection_for_Grape_Seedlessness/blob/main/794snp.indel.txt).

```
bcftools index -t merge.beagle.vcf.gz
bcftools view -R 794snp.indel.txt merge.beagle.vcf.gz > merge.beagle.keep_var.vcf
perl change_vcf_format_impute.pl merge.beagle.keep_var.vcf | cut -f 3,10- | grep -v "^#" > merge.beagle.keep_var_impute.vcf       # 0|0 to 0, 0|1 to 1, 1|1 to 2
```
Please check the number of your extracted variations. You can use `wc -l file.txt` to check the line count. Sometimes, bcftools may extract more variations than expected.

## Python for genome prediction
Please prepare the phenotype file with the format `ID` + tab(/t) + `phe`. An example file has been uploaded to this repository.

```
pip install -U scikit-learn

# In order to check your installation you can use
python -m pip show scikit-learn  # to see which version and where scikit-learn is installed
python -m pip freeze  # to see all packages installed in the active virtualenv
python -c "import sklearn; sklearn.show_versions()"

python gs_pred.py 
```
You can check the output result `keep_pos.test_regression_pred.csv` and analyze the statistics of multiple unknown phenotypes.

# Code availability and Citation
All scripts could be found in this repository above. If you have any question, please do not hesitate contact us. Xu Wang: 571720850@qq.com

Wang, Xu, et al. "Integrative genomics reveals the polygenic basis of seedlessness in grapevine" ***Current Biology*** (2024). doi: [https://doi.org/10.1016/j.cub.2024.07.022](https://doi.org/10.1016/j.cub.2024.07.022)





