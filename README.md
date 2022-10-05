## Обязательная часть задания
##### *Выполнила Васильева Дарья, БХМ201*
#### 1. В своей папке создала символическую ссылку на каждый из файлов
```
ln -s /usr/share/data-minor-bioinf/assembly/oil_R1.fastq
ln -s /usr/share/data-minor-bioinf/assembly/oil_R2.fastq
ln -s /usr/share/data-minor-bioinf/assembly/oilMP_S4_L001_R1_001.fastq
ln -s /usr/share/data-minor-bioinf/assembly/oilMP_S4_L001_R2_001.fastq
```
#### 2. С помощью команды seqtk выбрала случайно 5 миллионов чтений типа paired-end и 1.5 миллиона чтений типа mate-pairs; для параметра -s (random seed) задала значение 109
```
seqtk sample -s109 oil_R1.fastq 5000000 > sub1.fastq
seqtk sample -s109 oil_R2.fastq 5000000 > sub2.fastq
seqtk sample -s109 oilMP_S4_L001_R1_001.fastq 1500000 > matepairs1.fastq
seqtk sample -s109 oilMP_S4_L001_R2_001.fastq 1500000 > matepairs2.fastq
```
#### 3. С помощью программ fastQC и multiQC оценила качество исходных чтений и получила по ним общую статистику
```
mkdir fastqc
ls sub* matep* | xargs -tI{} fastqc -o fastqc {}
mkdir multiqc
multiqc -o multiqc fastqc
```
![](https://github.com/DomnaVasil/hse22_hw1/blob/a792ec64f549e0f382af7aa475024498be1c84ec/images/multiqc1.jpg)
![](https://github.com/DomnaVasil/hse22_hw1/blob/a792ec64f549e0f382af7aa475024498be1c84ec/images/multiqc.png)
#### 4. С помощью программ platanus_trim и platanus_internal_trim подрезала чтения по качеству и удалила адаптеры
```
platanus_trim sub*
platanus_internal_trim matepairs*
rm sub1.fastq
rm sub2.fastq
rm matepairs1.fastq
rm matepairs2.fastq
```
#### 5. С помощью программ fastQC и multiQC оценила качество подрезанных чтений и получила по ним общую статистику
```
mkdir fastqc_trimmed
ls sub* matep*| xargs -tI{} fastqc -o fastqc_trimmed {}
mkdir multiqc_trimmed
multiqc -o multiqc_trimmed fastqc_trimmed
```
![](https://github.com/DomnaVasil/hse22_hw1/blob/a792ec64f549e0f382af7aa475024498be1c84ec/images/multiqc%20trimmed1.jpg)
![](https://github.com/DomnaVasil/hse22_hw1/blob/a792ec64f549e0f382af7aa475024498be1c84ec/images/multiqc%20trimmed.png)
#### 6. С помощью программы “platanus assemble” собрала контиги из подрезанных чтений
```
time platanus assemble -o Poil -f sub1.fastq.trimmed sub2.fastq.trimmed 2> assemble.log
```
#### 7. С помощью программ “ platanus scaffold” собрала скаффолды из контигов, а также из подрезанных чтений
```
time platanus scaffold -o Poil -c Poil_contig.fa -IP1 sub1.fastq.trimmed sub2.fastq.trimmed -OP2 matepairs1.fastq.int_trimmed matepairs2.fastq.int_trimmed 2> scaffold.log
```
#### 8. С помощью программы “platanus gap_close” уменьшила кол-во гэпов с помощью подрезанных чтений
```
time platanus gap_close -o Poil -c Poil_scaffold.fa -IP1 sub1.fastq.trimmed sub2.fastq.trimmed -OP2 matepairs1.fastq.int_trimmed  matepairs2.fastq.int_trimmed 2> gapclose.log
```
#### 9. Удалила подрезанные чтения
```
rm sub*.trimmed matepairs*.int_trimmed
```
