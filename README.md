# bait-mapper
Uses blastn to map 120mer baits to a reference allowing up to 25% mismatch

## make blastn db
```makeblastdb -in CHR.fa -dbtype nucl -parse_seqids```

## run blastn
```blastn -db CHR.fa -query ALL_PROBES.fa -evalue 1e-5 -max_target_seqs 100000 -outfmt 6 -strand plus > blast.pos.tab```  
```blastn -db CHR.fa -query ALL_PROBES.fa -evalue 1e-5 -max_target_seqs 100000 -outfmt 6 -strand minus > blast.neg.tab```

## filter blast results to those with 25% mismatch  
Column 3 is percent ID and column 4 is alignment length. This command keeps all hits that are between 90 and 120 bp and excludes those that have greater than 30 bp in mismatch. This can be 30 SNPs across 120 bp or 100% ID across 90 bp and everything in between.  
```awk '{if ($4<=120 && $4>=90.00)print }' blast.pos.tab  | awk '{if (($3/100)*$4>=90.00)print }' > blast.pos.filtered.tab```  
```awk '{if ($4<=120 && $4>=90.00)print }' blast.neg.tab  | awk '{if (($3/100)*$4>=90.00)print }' > blast.neg.filtered.tab```

## convert blast output to gff  
This will generate a gff feature for each 120mer aligning to the ref with 25% or less mismatch. This is a huge amount of features that will not plot well.  
```sh blast-to-gff.sh blast.pos.filtered.tab blast.neg.filtered.tab blast.pos.neg.filtered.gff```

## flattern gff regions
This reduces the redundancy in gff features by collapsing overlapping 120mers into larger 1x features, making plotting easier.  
```sh flattern-gff.sh blast.pos.neg.filtered.gff blast.pos.neg.filtered.flattern.gff```
