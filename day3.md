# Day 3 Finish the complex

Today I want you to look into the output of other tracks. Can you use this to reconstitute the entire complex, or at least parts of it? Which proteins have indirect interactions? Use previously learned methods like prodigy and UniProt to do this. You can also look for different methods/tools to use for this online. 

You are free to explore today, but you can also just work on refining the data you already acquired to make your presentations. 

Inside the ```/zfsstore/courses/2025-2026/4022BIOIFY/backup/COMPLETED_AF3``` folder you will find three folders, ```comparison_pair starting_pair screen``` these contain all generated models of all tracks and an index CSV for summary.

You can copy this folder with ```cp -r``` into your own group folder for further analysis. 

To run prodigy and save output into a file, use
  
```bash
conda activate /zfsstore/courses/2025-2026/4022BIOIFY/conda/prodigy

prodigy -q /path/to/COMPLETED_AF3/screen/models/ > prodigy_screen.tsv 
```
To make life easier, I made you a python script that can merge the TSV with the CSV index. Run as:

```bash
python /zfsstore/courses/2025-2026/4022BIOIFY/scripts/add_prodigy_to_index.py \
  --index /path/to/screen_index.csv \
  --prodigy prodigy_screen.tsv \
  --output screen_index_with_prodigy.csv
```

Which proteins have indirect interactions?
