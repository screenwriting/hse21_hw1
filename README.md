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

В целом картина схожа с той, что была получена на семинаре (из исходного несемплированного файла)

Дупликаты: в чтениях paired-end их процент гораздо больше, чем на mate-pairs, возможно, это связано с тем, что длина чтения с каждого конца короче, чем общая длина читаемого фрагмента. Процент дупликатов немного меньше, чем на семинаре:

![image](https://user-images.githubusercontent.com/86132283/139122067-036342af-db8f-4c21-9906-15e6f0544e05.png)


Качество чтений: С ростом позиции чтения качество чтения падает. Для mate-pairs качество пересекает минимально допустимое значения ближе к концу чтения:

![image](https://user-images.githubusercontent.com/86132283/139124041-6dfa8f4d-862e-40e3-8ec1-7c49223a34f5.png)


Содержание адаптеров: В конце чтений для mate-paires содержания адаптеров превышает допустимые значения и находится в диапазоне от 49.97% до 73.72%:

![image](https://user-images.githubusercontent.com/86132283/139125001-586363ca-828f-463c-a553-143083f7c0bc.png)


