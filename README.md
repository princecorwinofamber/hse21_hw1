# hse21_hw1
ДЗ-1 по курсу Анализ Данных Секвенирования

seqtk sample -s21 oil_R1.fastq 5000000 > sub_paired_end_R1.fq
seqtk sample -s21 oil_R2.fastq 5000000 > sub_paired_end_R2.fq
seqtk sample -s21 oilMP_S4_L001_R1_001.fastq 1500000 > sub_mate_pairs_R1.fq
seqtk sample -s21 oilMP_S4_L001_R2_001.fastq 1500000 > sub_mate_pairs_R2.fq

mkdir subsequnces
mv sub* subsequnces/
cd subsequnces

ls -1 | xargs -P 1 -tI{} fastqc {}
