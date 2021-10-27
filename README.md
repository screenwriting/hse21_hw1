# HW1 Светцов 2 группа

Сид: 518
Выберем случайные семплы из исходных последовательностей:

seqtk sample -s518 oilMP_S4_L001_R1_001.fastq 1500000 > sample_oilMP_S4_L001_R1_001.fastq
seqtk sample -s518 oilMP_S4_L001_R2_001.fastq 1500000 > sample_oilMP_S4_L001_R2_001.fastq
seqtk sample -s518 oil_R1.fastq 5000000 > sample_oil_R1.fastq
seqtk sample -s518 oil_R2.fastq 5000000 > sample_oil_R2.fastq

Соберем статистику по каждому файлу через fastqc и xargs, результаты запихнем в папку fastqc, затем объединим их в один отчет с помощью multiqc в папку multiqc:

mkdir fastqc
mkdir multiqc
ls sample* | xargs -tI{} fastqc -o fastqc {}
multiqc -o multiqc fastqc

Я скачивал файлы с помощью команды scp:

scp -P 5222 -i {путь к ключу} fasvettsov@92.242.58.92:/home/fasvettsov/hw1/multiqc/multiqc_report.html .

Анализ исходных чтений multiqc:

![image](https://user-images.githubusercontent.com/86132283/139121122-9fc3c675-15dc-4c9d-92cc-9896a4f4db83.png)
