# fastq-from-ITSx
These scripts allow ITSx to be used with fastq files, with minimal overhead.

These two R scripts can be used with ITSx in order to get fastq files as an output. This is useful if you are running a program like DADA2 to cluster your extracted sequence data, since it uses quality scores of sequences. The general workflow is to make a fasta copy of your fastq file (the script just deletes quality data), run ITSx on that fasta file, then copy the quality information from your original fastq file back into the ITSx extracted fasta sequence data. When quality data are added back in, the script uses a 100% sequence data match to make sure it pulls out the right quality data. This works because ITSx only pulls out a subset of the sequence - it doesn't edit it at all. You'd run these scripts as follows:
```
# 1. Create a fasta copy of your input fastq file:
./fastq_to_fasta.r -i my_raw_seqs.fastq -o my_raw_seqs.fasta

# 2. Run ITSx on that fasta copy you just made:
/# note: ITSx just appends ".ITS1.fasta" to your -o here, and that's the file you'll use in step 3
ITSx -i my_raw_seqs.fasta -o my_extracted_seqs -t "Fungi" --cpu 8 --graphical F --preserve T --save_regions ITS1

# 3. Add quality data back into the fasta sequences extracted with ITSx:
./itsx_fastq_extractor.r -f my_extracted_seqs.ITS1.fasta -q my_raw_seqs.fastq --threads 8 -o my_extracted_seqs.ITS1.fastq
```

Requirements/notes:
1. Requires R package "optparse". Install beforehand with 
    install.packages("optparse")
2. This won't work on interleaved files. Not sure if interleaved fastq is a thing, even. I hope not.
3. This DOES work with gzipped files. It doesn't write them, though. 
4. ITSx produces a ton of extra files that you probably don't want. HIGHLY recommend running this in its own dir so that you can keep the final product and easily rm everything else.
