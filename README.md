# Schmidt_2017_Seth_CVTK
CVTK analysis of 2017 Data

# 1. Generate mpileup with E files
````
#!/bin/bash

BAM_DIR="/mnt/d/SchmidtLabData_2017/bams"
REF="/mnt/d/xQTL_2025_Data/ref/dm6_nochr.fa"
OUT="/mnt/d/SchmidtLabData_2017/CVTK/E_all.mpileup"

mkdir -p "$(dirname "$OUT")"

# Collect all E-files in consistent order
mapfile -t BAMS < <(find "$BAM_DIR" -maxdepth 1 -name "E[0-9]*.dedup.realign.bam" | sort)

echo "Found ${#BAMS[@]} BAM files:"
printf '  %s\n' "${BAMS[@]}"
echo ""

samtools mpileup \
    -f "$REF" \
    -B \
    -Q 20 \
    -q 20 \
    -o "$OUT" \
    "${BAMS[@]}"

echo "Done: $OUT"

````

# 2. Create metadata with files:

````
BAM_DIR="/mnt/d/SchmidtLabData_2017/bams"
OUT="/mnt/d/SchmidtLabData_2017/CVTK/E_samples.tsv"

echo -e "sample\ttreatment\tcage\ttpt\tbam" > "$OUT"

find "$BAM_DIR" -maxdepth 1 -name "E[0-9]*.dedup.realign.bam" | sort | while read -r bam; do
    fname=$(basename "$bam")
    # E11-T2_.dedup.realign.bam
    if [[ "$fname" =~ ^E([0-9]+)-T([0-9]+)_ ]]; then
        cage="${BASH_REMATCH[1]}"
        tpt="${BASH_REMATCH[2]}"
        sample="E${cage}-T${tpt}"
        echo -e "${sample}\tE\t${cage}\t${tpt}\t${bam}"
    else
        echo "WARNING: could not parse $fname" >&2
    fi
done >> "$OUT"

echo "Done: $OUT"
cat "$OUT"

````

# 3. Call grenedalf .sync file from the mpileup:

````
TBD
````


