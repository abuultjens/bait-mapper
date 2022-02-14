# bait-mapper
Uses blast to map 120mer baits to a reference allowing 25% mismatch

make blastn db
```makeblastdb -in CHR.fa -dbtype nucl -parse_seqids```

run blastn
```blastn -db CHR.fa -query ALL_PROBES.fa -evalue 1e-5 -max_target_seqs 100000 -outfmt 6 -strand plus > blast.tab```

# filter blast results to those with 25% mismatch
```awk '{if ($4<=120 && $4>=90.00)print }' blast.tab  | awk '{if (($3/100)*$4>=90.00)print }' > blast.filtered.tab```

# convert blast output to gff
```sh blast-to-gff.sh blast.filtered.tab blast.filtered.gff```

# flattern gff regions
```sh flattern-gff.sh filtered.blast.gff filtered.blast.flattern.gff```
