# Day 1: From sequence to structure with AlphaFold

## Module context

Welcome to the first day of the protein complex prediction module. In this course block, you will investigate how evolutionary information in protein sequences can be used to predict protein structures and protein complexes.

The biological case study is the mitochondrial ATP synthase. This is a large molecular machine that produces ATP by coupling proton flow through a membrane embedded motor to catalysis in a soluble catalytic head. You will receive a curated subset of mitochondrial proteins. Some of these proteins belong to ATP synthase, while others are distractors. Over the coming days, your job is to use AlphaFold predictions, coevolution analysis, and structural inspection to infer which proteins likely interact.

Today focuses on AlphaFold and practical prediction workflows on HPC ALICE.

## Learning goals

By the end of Day 1, you should be able to:

1. Explain why protein structure prediction is useful in biology.
2. Describe how multiple sequence alignments can contain evolutionary information about protein structure.
3. Explain, at a conceptual level, how AlphaFold uses sequence, MSA, and pairwise residue information.
4. Describe the difference between predicting a single protein and predicting a protein complex.
5. Prepare or inspect AlphaFold input files for protein complex prediction.
6. Submit a small AlphaFold prediction job on HPC ALICE.
7. Interpret basic AlphaFold output files using confidence metrics such as pLDDT and PAE.
8. Make an initial judgement about whether a predicted protein interaction is credible.

## The big idea

Proteins do not evolve residue by residue in isolation. If two residues physically contact each other in a folded protein, a mutation in one position may be compensated by a mutation in the other. Across many related sequences, these paired changes can leave a statistical signal. This is called coevolution.

AlphaFold does not simply memorize protein shapes. It uses information from amino acid sequence, homologous sequences, templates when available, and learned residue pair representations to predict a structure that is compatible with the input sequence and evolutionary context.

For protein complexes, the challenge becomes harder. We need to know not only how each chain folds, but also whether chains interact, where they interact, and whether the predicted interface is biologically meaningful.

## Before the practical

Make sure you can:

1. Log into ALICE.
2. Navigate directories using the command line.
3. Use `ls`, `cd`, `pwd`, `less`, `head`, and `cat`.
4. Submit a job with `sbatch`.
5. Check the queue with `squeue`.
6. Copy output files or open them in a viewer when instructed.

If one of these steps is unfamiliar, ask before starting the prediction work. The goal today is not to become a Linux wizard. The goal is to use the command line as a laboratory instrument.

## Day 1 schedule

### Part 1: Lecture, 1 hour

#### 0 to 10 min: Why predict protein structures?

Questions:

1. Why do we care about protein structure?
2. Why are experimental structures difficult to obtain?
3. What can a predicted structure tell us?
4. What can a predicted structure not tell us?

Key concepts:

* Amino acid sequence
* Protein fold
* Protein domain
* Protein complex
* Protein interface
* Structural model
* Experimental validation

#### 10 to 25 min: Evolutionary information in sequences

Questions:

1. What is a multiple sequence alignment?
2. Why are conserved residues often important?
3. What does it mean when two positions coevolve?
4. Why does correlation not always mean direct contact?

Key concepts:

* Homologs
* Orthologs
* Paralogs
* Conservation
* Covariation
* Coevolution
* Direct and indirect correlations

Example:

Imagine two residues that form a salt bridge. If one residue changes from positive to neutral, the partner residue may also change because the original interaction is no longer useful. Across many species, such compensating changes can indicate that the two positions are structurally or functionally linked.

#### 25 to 40 min: How AlphaFold uses sequence information

Conceptual AlphaFold workflow:

1. Start with an amino acid sequence.
2. Search databases for related sequences.
3. Build a multiple sequence alignment.
4. Extract conservation and covariation patterns.
5. Build internal representations of residue level and residue pair information.
6. Iteratively refine the predicted geometry.
7. Produce a 3D model and confidence estimates.

Important message:

AlphaFold output is not just a coordinate file. It is a prediction plus confidence information. You should always inspect both.

#### 40 to 50 min: Predicting protein complexes

Single protein prediction asks:

> How does this chain fold?

Protein complex prediction asks:

> Do these chains bind, and if so, how?

Additional challenges:

* Correct stoichiometry may be unknown.
* The true interface may need a membrane, cofactor, ligand, or assembly partner.
* Related proteins may confuse the evolutionary signal.
* A visually compact model can still be biologically wrong.
* Flexible regions may have low confidence even if the folded domains are correct.

#### 50 to 60 min: Confidence metrics

Today you will mainly use:

1. **pLDDT**  
   A per residue confidence score. High pLDDT means AlphaFold is locally confident about the position of that residue. Low pLDDT may indicate disorder, flexibility, missing context, or failed prediction.

2. **PAE**  
   Predicted aligned error. This helps you judge whether AlphaFold is confident about the relative placement of domains or chains. For complexes, low PAE between chains is more encouraging than high PAE between chains.

3. **Visual interface inspection**  
   A predicted interaction is more convincing when the chains form a clear interface, the interface has reasonable confidence, and the prediction agrees with biological expectations.

## Practical overview

In the practical, you will work with a small set of mitochondrial proteins. Some are ATP synthase subunits. Some are distractors.

You will:

1. Inspect protein sequence files.
2. Inspect or generate AlphaFold input files.
3. Submit one small prediction job on ALICE.
4. Inspect an example output from a completed prediction.
5. Record your first interpretation of predicted interfaces.

## Repository structure

The course repository may look like this:

```text
day1_alphafold/
  README.md
  data/
    protein_subset.fasta
    protein_metadata.tsv
    example_pairs.tsv
  inputs/
    single_chain_examples/
    complex_examples/
  jobs/
    template_job.sbatch
  scripts/
    make_af3_input.py
    check_prediction_outputs.py
  results_precomputed/
    ATP5F1A_ATP5F1B/
    ATP5F1D_ATP5F1E/
    ATP5F1A_decoy/
  student_outputs/
```

Your instructor may adjust the exact paths depending on the ALICE course environment.

## Step 1: Log into ALICE

Open a terminal and connect to ALICE using the instructions provided by your instructor.

Example:

```bash
ssh your_username@alice.leidenuniv.nl
```

Move into the course folder:

```bash
cd /path/to/day1_alphafold
pwd
ls
```

Check that you can see the repository folders:

```bash
ls data
ls scripts
ls results_precomputed
```

## Step 2: Inspect the protein subset

Open the protein metadata table:

```bash
column -t -s $'\t' data/protein_metadata.tsv | less -S
```

The table may contain:

```text
protein_id    short_name    description                         expected_role
P25705        ATP5F1A       ATP synthase F1 subunit alpha        hidden
P06576        ATP5F1B       ATP synthase F1 subunit beta         hidden
P36542        ATP5F1C       ATP synthase F1 subunit gamma        hidden
P30049        ATP5F1D       ATP synthase F1 subunit delta        hidden
P56381        ATP5F1E       ATP synthase F1 subunit epsilon      hidden
Q00000        DECOY1        mitochondrial decoy protein          hidden
```

Do not worry if the exact proteins differ. Your task is to use evidence, not to memorize the answer.

Inspect the FASTA file:

```bash
grep ">" data/protein_subset.fasta
```

Count how many protein sequences are present:

```bash
grep -c ">" data/protein_subset.fasta
```

View one sequence:

```bash
grep -A 2 "ATP5F1A" data/protein_subset.fasta
```

## Step 3: Think before predicting

Before running AlphaFold, answer these questions in your notes:

1. Which proteins would you expect to be soluble?
2. Which proteins might be membrane associated?
3. Which proteins might form stable subcomplexes?
4. Which combinations would be bad guesses?
5. What evidence would convince you that two proteins interact?

Prediction is expensive. A good computational biologist does not launch jobs randomly. They design tests.

## Step 4: Inspect example protein pairs

Open the example pair table:

```bash
column -t -s $'\t' data/example_pairs.tsv
```

Example:

```text
pair_id              chain_a     chain_b     reason
ATP5F1A_ATP5F1B      ATP5F1A     ATP5F1B     likely F1 head interaction
ATP5F1D_ATP5F1E      ATP5F1D     ATP5F1E     likely stalk interaction
ATP5F1A_DECOY1       ATP5F1A     DECOY1      negative control
```

Choose one pair for today. Your instructor may assign pairs to avoid everyone running the same job.

## Step 5: Inspect AlphaFold input format

AlphaFold3 style inputs are usually JSON files. For this teaching practical, the input files are prepared for you or generated by a script.

Inspect an example input:

```bash
less inputs/complex_examples/ATP5F1A_ATP5F1B/input.json
```

You should be able to identify:

1. The job name.
2. The protein sequences.
3. The chain identifiers.
4. The number of model seeds, if included.
5. Any additional settings used by the local ALICE installation.

Questions:

1. How many protein chains are present?
2. Are the chains identical or different?
3. Is this a single protein prediction or a complex prediction?
4. Does the input define stoichiometry?

## Step 6: Generate an input file

If your instructor asks you to generate an input file, use the provided script.

Example:

```bash
python scripts/make_af3_input.py \
  --fasta data/protein_subset.fasta \
  --pair ATP5F1A ATP5F1B \
  --outdir student_outputs/ATP5F1A_ATP5F1B \
  --job-name ATP5F1A_ATP5F1B \
  --seeds 3
```

Check the output:

```bash
ls student_outputs/ATP5F1A_ATP5F1B
less student_outputs/ATP5F1A_ATP5F1B/input.json
```

If the script fails, read the error message carefully. Common problems include:

* Typing a protein name that does not exist in the FASTA file.
* Running the command from the wrong folder.
* Forgetting to create or specify an output directory.
* Using spaces or special characters in a job name.

## Step 7: Prepare the SLURM job

Inspect the job script:

```bash
less jobs/template_job.sbatch
```

A simplified SLURM script may look like this:

```bash
#!/bin/bash
#SBATCH --job-name=af3_example
#SBATCH --partition=gpu-mig-40g,gpu-a100-80g
#SBATCH --gres=gpu:1
#SBATCH --cpus-per-task=8
#SBATCH --mem=64G
#SBATCH --time=04:00:00
#SBATCH --output=logs/%x_%j.out
#SBATCH --error=logs/%x_%j.err

module purge
module load alphafold/cc8_3-20250304

alphafold \
  --json_path=input.json \
  --output_dir=output
```

Your local ALICE command may differ. Always follow the command given by your instructor.

Copy or generate a job script for your chosen prediction:

```bash
cp jobs/template_job.sbatch student_outputs/ATP5F1A_ATP5F1B/job.sbatch
cd student_outputs/ATP5F1A_ATP5F1B
mkdir -p logs
```

Before submitting, check:

```bash
pwd
ls
less input.json
less job.sbatch
```

## Step 8: Submit the job

Submit the job:

```bash
sbatch job.sbatch
```

Check the queue:

```bash
squeue -u $USER
```

Watch the log files:

```bash
ls logs
tail -f logs/*.out
```

To stop watching the file, press:

```text
Ctrl + C
```

## Step 9: Inspect precomputed results

Because AlphaFold jobs can take time, you will inspect precomputed outputs while your own job is running.

Move back to the course folder:

```bash
cd /path/to/day1_alphafold
```

List precomputed results:

```bash
find results_precomputed -maxdepth 2 -type f | head
```

For each completed prediction folder, look for:

* Predicted structure files, such as `.cif` or `.pdb`
* Confidence files
* PAE plots or JSON files
* Ranking files
* Log files

Example:

```bash
ls results_precomputed/ATP5F1A_ATP5F1B
```

## Step 10: Interpret AlphaFold outputs

For one precomputed prediction, record the following:

### Basic information

```text
Prediction name:
Chains included:
Number of residues in chain A:
Number of residues in chain B:
Prediction model inspected:
```

### Confidence

```text
Average pLDDT, if available:
Regions with high confidence:
Regions with low confidence:
Inter chain PAE low, medium, or high:
```

### Interface

```text
Do the chains touch each other?
Does the interface look compact?
Are interface residues confident?
Is the relative chain placement confident according to PAE?
Would you call this interaction likely, uncertain, or unlikely?
```

### Biological interpretation

```text
Does the predicted interaction make sense for ATP synthase?
Could this be part of the F1 head, central stalk, peripheral stalk, Fo sector, or a decoy interaction?
What additional evidence would you want?
```

## Step 11: Minimal PyMOL inspection

Open a predicted structure in PyMOL or another structure viewer.

Example:

```bash
pymol results_precomputed/ATP5F1A_ATP5F1B/ranked_model.cif
```

Useful PyMOL commands:

```pymol
hide everything
show cartoon
color marine, chain A
color orange, chain B
orient
```

Color by confidence if pLDDT values are stored in the B factor column:

```pymol
spectrum b, blue_white_red
```

Select possible interface residues within 5 Å:

```pymol
select interface_A, chain A within 5 of chain B
select interface_B, chain B within 5 of chain A
show sticks, interface_A or interface_B
```

Questions:

1. Is the interface small or large?
2. Is it formed by ordered regions or floppy tails?
3. Does one chain wrap around the other?
4. Are there obvious clashes or strange geometry?
5. Would this prediction be enough to claim interaction?

## Step 12: Day 1 checkpoint questions

Submit short answers before leaving.

### Conceptual questions

1. What is a multiple sequence alignment?
2. Why can coevolution help predict protein contacts?
3. Why is protein complex prediction harder than single chain prediction?
4. What does high pLDDT tell you?
5. What does high pLDDT not tell you?
6. Why is PAE useful for judging protein complexes?

### Practical questions

1. Which protein pair did you inspect?
2. Did the prediction suggest an interaction?
3. Which confidence evidence supported your judgement?
4. Which evidence made you cautious?
5. What would you test next?

## Expected Day 1 deliverable

Upload or submit a short note with:

1. Your chosen protein pair or subcomplex.
2. One screenshot of the predicted structure.
3. One screenshot or summary of the confidence information.
4. A short interpretation of the predicted interaction.
5. One limitation of the prediction.
6. One follow up prediction or analysis you would run.

Recommended length: 300 to 500 words.

## Example interpretation

The prediction of ATP5F1A with ATP5F1B produced a compact interface between two well folded chains. Most residues in the structured domains showed high pLDDT. The PAE plot suggested that the relative placement of the two chains was more reliable than in the negative control prediction. This supports the idea that ATP5F1A and ATP5F1B interact as part of the F1 catalytic head. However, this prediction alone does not prove the full ATP synthase architecture, because the real complex contains multiple copies and additional subunits. Further predictions with ATP5F1C and other F1 components would be needed.

## Instructor notes

### Recommended teaching strategy

This practical works best if students run one small job but inspect several precomputed jobs. This avoids turning the session into queue watching.

Suggested split:

1. Every group submits one assigned pair.
2. All groups inspect the same positive control.
3. All groups inspect the same negative control.
4. Each group interprets one unique pair.

### Suggested positive controls

* ATP5F1A with ATP5F1B
* ATP5F1D with ATP5F1E
* ATP5F1C with ATP5F1D or ATP5F1E

### Suggested negative controls

* ATP5F1A with an unrelated mitochondrial matrix enzyme
* ATP5F1B with a respiratory chain protein from another complex
* A soluble ATP synthase subunit with a clearly unrelated decoy

### Suggested discussion prompts

Ask students:

1. Did the positive control look obviously better than the negative control?
2. Did any negative control still produce a contact?
3. How would you distinguish a real interface from a forced interface?
4. Why might membrane proteins be harder to judge?
5. What is the difference between a confident fold and a confident interaction?

### Common student misconceptions

1. **High pLDDT means the complex is correct.**  
   Not necessarily. pLDDT is mainly local confidence. A model can contain confident monomers arranged incorrectly.

2. **Any contact between chains means interaction.**  
   Not necessarily. Forced predictions can place unrelated proteins together.

3. **Low confidence means the protein has no structure.**  
   Not necessarily. The region may be flexible, disordered, missing a partner, or poorly represented in the training or sequence data.

4. **AlphaFold replaces experiments.**  
   No. AlphaFold generates hypotheses. Experimental validation remains essential.

5. **One prediction is enough.**  
   Usually no. Good analysis compares alternative stoichiometries, negative controls, confidence metrics, biological knowledge, and sequence based evidence.

## Suggested extension for fast groups

Fast groups can test an additional pair:

```bash
python scripts/make_af3_input.py \
  --fasta data/protein_subset.fasta \
  --pair ATP5F1D ATP5F1E \
  --outdir student_outputs/ATP5F1D_ATP5F1E \
  --job-name ATP5F1D_ATP5F1E \
  --seeds 3
```

They should compare:

1. Which pair gives a more convincing interface?
2. Which pair has better inter chain confidence?
3. Which pair is more biologically plausible?
4. Which prediction would they prioritize for Day 2 coevolution analysis?

## Mini glossary

**AlphaFold**  
A protein structure prediction system developed by DeepMind. AlphaFold2 predicts protein structures from amino acid sequence using deep learning, evolutionary information, and structural patterns learned from known structures.

**AlphaFold Multimer**  
A version of AlphaFold designed for protein complex prediction.

**AlphaFold3**  
A newer AlphaFold model designed to predict biomolecular complexes involving proteins, nucleic acids, small molecules, ions, and modified residues.

**MSA**  
Multiple sequence alignment. A table of related sequences aligned so that homologous positions are compared.

**Coevolution**  
Coordinated evolutionary change between residues, often because those residues are structurally or functionally linked.

**pLDDT**  
Predicted local distance difference test. A per residue confidence score.

**PAE**  
Predicted aligned error. A confidence measure for the relative placement of residues, domains, or chains.

**Interface**  
The physical contact region between two interacting protein chains.

**Stoichiometry**  
The number of copies of each chain in a complex.

**Decoy**  
A protein included as a negative control or distractor.

## References and further reading

1. Jumper J, Evans R, Pritzel A, et al. Highly accurate protein structure prediction with AlphaFold. Nature. 2021;596:583 to 589. DOI: 10.1038/s41586-021-03819-2.

2. Evans R, O'Neill M, Pritzel A, et al. Protein complex prediction with AlphaFold-Multimer. bioRxiv. 2021. DOI: 10.1101/2021.10.04.463034.

3. Abramson J, Adler J, Dunger J, et al. Accurate structure prediction of biomolecular interactions with AlphaFold 3. Nature. 2024;630:493 to 500. DOI: 10.1038/s41586-024-07487-w.

4. Marks DS, Colwell LJ, Sheridan R, et al. Protein 3D structure computed from evolutionary sequence variation. PLoS One. 2011;6:e28766. DOI: 10.1371/journal.pone.0028766.

5. Senior AW, Evans R, Jumper J, et al. Improved protein structure prediction using potentials from deep learning. Nature. 2020;577:706 to 710. DOI: 10.1038/s41586-019-1923-7.

6. Varadi M, Anyango S, Deshpande M, et al. AlphaFold Protein Structure Database: massively expanding the structural coverage of protein sequence space with high accuracy models. Nucleic Acids Research. 2022;50:D439 to D444. DOI: 10.1093/nar/gkab1061.

## End of Day 1

Today you used AlphaFold as a prediction tool and began learning how to judge its output. Tomorrow, you will go one layer deeper and ask where part of AlphaFold's structural signal comes from: coevolution in protein sequences.
