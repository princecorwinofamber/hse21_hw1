# hse21_hw1
ДЗ-1 по курсу Анализ Данных Секвенирования  

seqtk sample -s21 oil_R1.fastq 5000000 > sub_paired_end_R1.fq  
seqtk sample -s21 oil_R2.fastq 5000000 > sub_paired_end_R2.fq  
seqtk sample -s21 oilMP_S4_L001_R1_001.fastq 1500000 > sub_mate_pairs_R1.fq  
seqtk sample -s21 oilMP_S4_L001_R2_001.fastq 1500000 > sub_mate_pairs_R2.fq  

mkdir subsequences  
mv sub* subsequences/  
cd subsequences  

ls -1 | xargs -P 1 -tI{} fastqc {}  
multiqc .

Качество чтений для всех четырех файлов относительно хорошее, хотя и портится к концу чтения:  
![](https://github.com/princecorwinofamber/hse21_hw1/blob/main/img/1.png)
![](https://github.com/princecorwinofamber/hse21_hw1/blob/main/img/2.png)
![](https://github.com/princecorwinofamber/hse21_hw1/blob/main/img/3.png)
![](https://github.com/princecorwinofamber/hse21_hw1/blob/main/img/4.png)
![](https://github.com/princecorwinofamber/hse21_hw1/blob/main/img/5.png)
![](https://github.com/princecorwinofamber/hse21_hw1/blob/main/img/6.png)
![](https://github.com/princecorwinofamber/hse21_hw1/blob/main/img/7.png)

cd ..
mkdir originals  
mv *.fastq originals  
cd subsequences  

mv sub_paired_end_R1.fq oil_R1.fastq
mv sub_paired_end_R2.fq oil_R2.fastq
mv sub_mate_pairs_R1.fq oilMP_S4_L001_R1_001.fastq
mv sub_mate_pairs_R2.fq oilMP_S4_L001_R2_001.fastq
mv * ..
cd ..

mkdir full_qc
mv *qc* full_qc
mv full_qc full_q_c

platanus_trim oil_R1.fastq oil_R2.fastq  
platanus_internal_trim oilMP_S4_L001_R1_001.fastq oilMP_S4_L001_R2_001.fastq  
mkdir trimmed_fastq
mv -v *trimmed trimmed_fastq

mkdir trimmed_fastqc  
ls trimmed_fastq/* | xargs -P 1 -tI{} fastqc -o trimmed_fastqc {}  
cd trimmed_fastqc  
multiqc .  

Результат работы программы multiqc (статистика и качество чтений):  
![](https://github.com/princecorwinofamber/hse21_hw1/blob/main/img/8.png)
![](https://github.com/princecorwinofamber/hse21_hw1/blob/main/img/9.png)
![](https://github.com/princecorwinofamber/hse21_hw1/blob/main/img/10.png)
![](https://github.com/princecorwinofamber/hse21_hw1/blob/main/img/11.png)
![](https://github.com/princecorwinofamber/hse21_hw1/blob/main/img/12.png)
![](https://github.com/princecorwinofamber/hse21_hw1/blob/main/img/13.png)
![](https://github.com/princecorwinofamber/hse21_hw1/blob/main/img/14.png)

cd ..
platanus assemble -o Poil -t 2 -m 28 -f trimmed_fastq/oil_R1.fastq.trimmed trimmed_fastq/oil_R2.fastq.trimmed 2> assemble_errors.log


platanus scaffold -o Poil -t 2 -c Poil_contig.fa -IP1 trimmed_fastq/oil_R1.fastq.trimmed trimmed_fastq/oil_R2.fastq.trimmed -OP2 trimmed_fastq/oilMP_S4_L001_R1_001.fastq.int_trimmed trimmed_fastq/oilMP_S4_L001_R2_001.fastq.int_trimmed 2> scaffold.log  


platanus gap_close -o Poil -t 1 -c Poil_scaffold.fa -IP1 trimmed_fastq/oil_R1.fastq.trimmed trimmed_fastq/oil_R2.fastq.trimmed -OP2 trimmed_fastq/oilMP_S4_L001_R1_001.fastq.int_trimmed trimmed_fastq/oilMP_S4_L001_R2_001.fastq.int_trimmed 2> gapclose.log


Переименуем Poil_contig.fa в contigs.fasta, а Poil_gapClosed.fa в scaffolds.fasta  
Файл longest.fasta получим из результата программы gap_close при помощи BioPython

https://colab.research.google.com/drive/1wd_KWk1zw3ilLzrf9ZaS8iBGFT2RD_OF?usp=sharing
