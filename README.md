# HW1 Светцов 2 группа

ССылка на colab с для подсчета скаффолдов и контигов: https://colab.research.google.com/drive/1jYZ0JFIuaC1HirGNG5c60fDJqJnSeXkI?usp=sharing

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


Чтобы это исправить, вырежем адаптеры из чтений с помощью platanus trim и internal trim (для MP), а затем сделаем по ним отчет:

    mkdir trim_fastqc
    mkdir trim_multiqc
    platanus_trim sample_oil_R1.fastq sample_oil_R2.fastq
    platanus_internal_trim sample_oilMP_S4_L001_R1_001.fastq sample_oilMP_S4_L001_R2_001.fastq
    ls \*trimmed | xargs -tI{} fastqc -o trim_fastqc {}
    multiqc -o trim_multiqc trim_fastqc
    
    scp -P 5222 -i {путь к ключу} fasvettsov@92.242.58.92:/home/fasvettsov/hw1/trim_multiqc/multiqc_report.html .


Анализ обрезанных чтений multiqc:

В сравнении с необрезанными чтениями уменьшились общее число последовательностей и (немного) процент дупликатов: 

![image](https://user-images.githubusercontent.com/86132283/139133458-786df22a-ba13-4446-ba3b-c4cc539dfd82.png)


Качество чтений: после подрезания качества всех тений находятся на допустимом уровне:

![image](https://user-images.githubusercontent.com/86132283/139133804-5594d247-5857-4f4b-9fda-85b8c5c00f5a.png)


Содержание адаптеров: ожидаемо, их больше нет (<1%):

![image](https://user-images.githubusercontent.com/86132283/139134007-3a3d6ce1-9335-43d2-b05e-a537a6eae38f.png)


Соберем геном с помощью platanus assemble: 

        platanus assemble -o fasvetPoil -t 2  -f sample_oil_R1.fastq.trimmed  sample_oil_R2.fastq.trimmed
        
        scp -P 5222 -i {путь к ключу} fasvettsov@92.242.58.92:/home/fasvettsov/hw1/fasvetPoil_contig.fa .


Общее число контигов: 614
Общая длина контигов: 3925614
Длина самого длинного контига: 179307


Соберем скаффолды из контигов:

    platanus scaffold -o fasvetPoil -t 2 -c fasvetPoil_contig.fa -IP1 sample_oil_R1.fastq.trimmed sample_oil_R2.fastq.trimmed -OP2 sample_oilMP_S4_L001_R1_001.fastq.int_trimmed sample_oilMP_S4_L001_R2_001.fastq.int_trimmed

    scp -P 5222 -i {путь к ключу} fasvettsov@92.242.58.92:/home/fasvettsov/hw1/fasvetPoil_scaffold.fa .
    

Общее число скаффолдов: 69
Общая длина скаффолдов: 3875742
Длина самого длинного скаффолда: 3831628
