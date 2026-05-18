# Day 1: AlphaFold prediction of protein complexes

## From sequence to structure in the human mitochondrial ATP synthase

Welcome to Day 1 of the protein complex prediction module.

In this practical, you will investigate how protein sequences can be used to predict protein structures and protein complexes. The biological case study is the human mitochondrial ATP synthase, also known as Complex V. ATP synthase is a large molecular machine that produces ATP by coupling proton movement through a membrane sector to catalysis in a soluble catalytic sector.

Today you will use AlphaFold on ALICE to prepare and run protein complex predictions. Later in the module, you will analyse coevolutionary signals and inspect predicted structures in more detail. The work you do today will provide material for your final group presentation.

No formal report is submitted today. Use the tables, screenshots, and questions in this document to collect results and interpretations for the later parts of the module.

---

## Learning goals

By the end of Day 1, you should be able to:

1. Explain why protein structure prediction is useful in biology.
2. Explain why evolutionary information can help predict protein structures.
3. Explain the difference between predicting one protein chain and predicting a protein complex.
4. Inspect FASTA files and AlphaFold input files.
5. Generate AlphaFold3 input folders from FASTA files.
6. Submit AlphaFold3 jobs on ALICE.
7. Inspect AlphaFold confidence outputs using pLDDT and PAE.
8. Open predicted structures in PyMOL.
9. Make a cautious first judgement about whether a predicted protein protein interaction is plausible.

---

## The biological system

Human mitochondrial ATP synthase contains multiple protein subunits. Some are part of the soluble F1 catalytic head, some form the central stalk, some form the peripheral stalk, and some are embedded in the mitochondrial inner membrane.

The full biological complex is larger than the pairwise predictions you will run today. Pairwise prediction is useful for teaching and exploration, but it is a simplified version of the real biological assembly.

Today the main question is:

```text
Can AlphaFold3 produce a plausible structural model for a given pair of proteins?
```

A plausible prediction is not the same as experimental proof.

---

## Important interpretation rule

A predicted protein complex is a hypothesis.

Do not conclude:

```text
These proteins definitely interact in vivo.
```

Do not conclude:

```text
These proteins do not interact in vivo because one prediction looked weak.
```

For this practical, the correct interpretation is more cautious:

```text
This pair gives a more plausible or less plausible AlphaFold3 complex prediction under the conditions tested today.
```

Some real ATP synthase interactions depend on the full assembly, membrane context, cofactors, or additional subunits. A weak pairwise prediction can therefore be caused by missing biological context. A strong looking pairwise prediction can also be misleading if the confidence metrics do not support the relative placement of the chains.

---

## AlphaFold confidence outputs

### pLDDT

pLDDT is a per residue confidence score. It estimates how confident AlphaFold is about the local structure around each residue.

General interpretation:

| pLDDT range | Approximate interpretation |
|---|---|
| Greater than 90 | Very high local confidence |
| 70 to 90 | Confident local structure |
| 50 to 70 | Low confidence or flexible region |
| Less than 50 | Very low confidence, often disorder or unreliable local structure |

Important point:

```text
High pLDDT supports local structure confidence.
High pLDDT does not prove that two chains interact correctly.
```

### PAE

PAE means predicted aligned error. For protein complexes, PAE is especially useful because it helps assess whether AlphaFold is confident about the relative placement of chains.

General interpretation:

| PAE pattern | Possible interpretation |
|---|---|
| Low PAE within each chain and low PAE between chains | More plausible complex prediction |
| Low PAE within each chain and high PAE between chains | Chains may fold well individually, but their relative placement is uncertain |
| High PAE in flexible regions | Disorder or flexibility may be present |
| Mixed PAE between chains | Some parts may be positioned confidently, while others remain uncertain |

Important point:

```text
For protein complex prediction, between chain PAE is often more informative than pLDDT alone.
```

---

## Day 1 overview

The practical has four stages.

### Stage 1: Set up your working folder

You will log into ALICE, create a group folder, copy the course files, and create a Python environment.

### Stage 2: Inspect your assigned FASTA file

Each group receives one track FASTA file. The five files are:

```text
track_A.fasta
track_B.fasta
track_C.fasta
track_D.fasta
track_E.fasta
```

Each FASTA file contains ATP synthase proteins and additional candidate proteins for comparison.

### Stage 3: Generate and submit AlphaFold3 jobs

You will use the provided script:

```text
scripts/AF3_prepare.py
```

The script converts FASTA files into AlphaFold3 input folders and SLURM batch scripts.

### Stage 4: Interpret predictions

You will inspect structures, pLDDT, PAE, and interfaces. You will choose one convincing prediction and one uncertain or weak prediction for later analysis.

---

## Files used today

The course directory contains these important folders:

```text
datasets/
  track_A.fasta
  track_B.fasta
  track_C.fasta
  track_D.fasta
  track_E.fasta

scripts/
  AF3_prepare.py
```

The FASTA headers have been simplified to UniProt accession IDs only.

Example FASTA format:

```text
>P25705
MLSVRVAAAVVRALPRRAGLVSRNALGSSFIAARNFHASNTHLQKTGTA...
>P06576
MLGFVGRVAAAPASGALRRLTPSASLPPAQLLLRAAPTAVHPVRDYA...
```

This means that protein identity is tracked by UniProt accession.

---

## Group tracks

Each group works with one FASTA file.

| Track | FASTA file | Biological focus |
|---|---|---|
| A | `datasets/track_A.fasta` | F1 catalytic head |
| B | `datasets/track_B.fasta` | Central stalk and rotor associated proteins |
| C | `datasets/track_C.fasta` | Peripheral stalk and stator |
| D | `datasets/track_D.fasta` | Fo membrane sector |
| E | `datasets/track_E.fasta` | Mixed ATP synthase discovery panel |

Each track contains a small panel of proteins. Some pairs are expected to be easier to interpret than others. Some proteins are included to make the screen more realistic.

---

## Track A: F1 catalytic head

### FASTA file

```text
datasets/track_A.fasta
```

### Biological focus

Track A focuses on the soluble F1 catalytic head of ATP synthase. The F1 head contains alpha and beta subunits that form the catalytic core.

### Starting pair

```text
P25705 x P06576
```

### Comparison pair

```text
P25705 x P40926
```

### Main questions

1. Does the starting pair form a clear predicted interface?
2. Is the interface supported by high or reasonable pLDDT?
3. Is the relative placement of the chains supported by low between chain PAE?
4. Which other proteins in `track_A.fasta` produce plausible predictions with `P25705`?
5. Which predictions look weak, uncertain, or biologically suspicious?

---

## Track B: Central stalk and rotor associated proteins

### FASTA file

```text
datasets/track_B.fasta
```

### Biological focus

Track B focuses on proteins associated with the central stalk and rotor region. This region connects the soluble catalytic head to the rotating membrane sector.

### Starting pair

```text
P36542 x P56381
```

### Comparison pair

```text
P36542 x P38646
```

### Main questions

1. Which proteins in `track_B.fasta` produce plausible interactions with `P36542`?
2. Are the predicted interactions compact or extended?
3. Does PAE support a confident relative placement of the two chains?
4. Do any predictions look like they may require a larger ATP synthase assembly to interpret properly?
5. Which prediction should be carried forward for more detailed structural inspection?

---

## Track C: Peripheral stalk and stator

### FASTA file

```text
datasets/track_C.fasta
```

### Biological focus

Track C focuses on the peripheral stalk and stator. The peripheral stalk helps hold the catalytic head in place while the central rotor turns.

### Starting pair

```text
P48047 x Q5QNZ2
```

### Comparison pair

```text
P48047 x O96008
```

### Main questions

1. Which proteins in `track_C.fasta` produce plausible interactions with `P48047`?
2. Are the predicted interfaces compact or elongated?
3. Does PAE support a confident relative placement of the chains?
4. Are any predicted contacts difficult to interpret because of flexible or extended regions?
5. Which prediction looks most useful for later analysis?

---

## Track D: Fo membrane sector

### FASTA file

```text
datasets/track_D.fasta
```

### Biological focus

Track D focuses on the Fo membrane sector. This region contains membrane associated proteins involved in proton translocation and rotor function.

Membrane protein predictions require extra caution. Hydrophobic transmembrane helices can form plausible looking contacts, but visual contact alone is not enough evidence for a confident interaction.

### Starting pair

```text
P00846 x P03928
```

### Comparison pair

```text
P00846 x P21796
```

### Main questions

1. Which proteins in `track_D.fasta` produce plausible membrane sector interactions with `P00846`?
2. Are the predicted interfaces formed by transmembrane helices?
3. Does the PAE support the relative placement of the chains?
4. Could hydrophobic helices create misleading contacts?
5. Which predictions require additional evidence before being trusted?

---

## Track E: Mixed ATP synthase discovery panel

### FASTA file

```text
datasets/track_E.fasta
```

### Biological focus

Track E combines proteins from multiple ATP synthase regions. This track is designed as a small discovery screen rather than a single subcomplex focused panel.

### Starting pair

```text
P48047 x Q5QNZ2
```

### Comparison pair

```text
P06576 x O96008
```

### Main questions

1. Which proteins in `track_E.fasta` produce plausible ATP synthase related interactions?
2. Which predictions look uncertain or weak?
3. Do any proteins outside the obvious ATP synthase core produce misleading contacts?
4. Can pLDDT and PAE help separate confident complex predictions from weak ones?
5. Which two predictions should be selected for comparison in later days?

---

## Step 1: Log into ALICE

### macOS or Linux

Open a terminal and connect to the ALICE gateway:

```bash
ssh studentnumber@ssh-gw.alice.universiteitleiden.nl
```

From the gateway, connect to the ALICE login node:

```bash
ssh studentnumber@login.alice.universiteitleiden.nl
```

### Windows

Use MobaXterm to connect to ALICE.

The ALICE login instructions for Windows are available here:

```text
https://pubappslu.atlassian.net/wiki/spaces/HPCWIKI/pages/37748811/Login+to+ALICE+or+SHARK+from+Windows#MobaXTerm
```

---

## Step 2: Create a group folder

Move to the course users folder:

```bash
cd /zfsstore/courses/2025-2026/4022BIOIFY/users
```

Create a folder for your group. Replace the example name with your real group number and student numbers.

```bash
mkdir groupnumber_studentnumber_studentnumber
cd groupnumber_studentnumber_studentnumber
```

Check that you are in the correct folder:

```bash
pwd
```

---

## Step 3: Copy the course files

Copy the datasets and scripts from the course folder into your group folder.

```bash
cp -r /zfsstore/courses/2025-2026/4022BIOIFY/datasets .
cp -r /zfsstore/courses/2025-2026/4022BIOIFY/scripts .
```

Check that the files are present:

```bash
ls
ls datasets
ls scripts
```

You should see:

```text
datasets
scripts
```

Inside `datasets`, you should see:

```text
track_A.fasta
track_B.fasta
track_C.fasta
track_D.fasta
track_E.fasta
```

Inside `scripts`, you should see:

```text
AF3_prepare.py
```

---

## Step 4: Create and activate a Python environment

Load Miniconda:

```bash
module load Miniconda3/24.7.1-0
```

Initialize conda:

```bash
conda init
source ~/.bashrc
```

If the shell does not update correctly, restart the shell:

```bash
exec bash
```

Create a Python environment inside your group folder:

```bash
conda create -p ./af3_day1_env python=3.11 biopython -c conda-forge -y
```

Activate the environment:

```bash
conda activate ./af3_day1_env
```

Check that Python is coming from your group folder:

```bash
which python
```

The path should contain your group folder and `af3_day1_env`.

Also check that Biopython is installed:

```bash
python -c "import Bio; print(Bio.__version__)"
```

---

## Step 5: Inspect your assigned FASTA file

Use the FASTA file assigned to your group.

Example for Track A:

```bash
less datasets/track_A.fasta
```

List all sequence headers:

```bash
grep "^>" datasets/track_A.fasta
```

Count the number of proteins:

```bash
grep -c "^>" datasets/track_A.fasta
```

Check sequence lengths:

```bash
python - <<'PY'
from Bio import SeqIO
from pathlib import Path

fasta = Path("datasets/track_A.fasta")

for record in SeqIO.parse(fasta, "fasta"):
    print(record.id, len(record.seq))
PY
```

For another track, replace `track_A.fasta` with the correct file.

---

## Step 6: Choose the correct FASTA for your group

Use this table:

| Track | FASTA file |
|---|---|
| A | `datasets/track_A.fasta` |
| B | `datasets/track_B.fasta` |
| C | `datasets/track_C.fasta` |
| D | `datasets/track_D.fasta` |
| E | `datasets/track_E.fasta` |

Set a shell variable for your track. This makes the later commands easier.

For Track A:

```bash
TRACK=track_A
FASTA=datasets/${TRACK}.fasta
```

For Track B:

```bash
TRACK=track_B
FASTA=datasets/${TRACK}.fasta
```

For Track C:

```bash
TRACK=track_C
FASTA=datasets/${TRACK}.fasta
```

For Track D:

```bash
TRACK=track_D
FASTA=datasets/${TRACK}.fasta
```

For Track E:

```bash
TRACK=track_E
FASTA=datasets/${TRACK}.fasta
```

Check:

```bash
echo $TRACK
echo $FASTA
grep "^>" $FASTA
```

---

## Step 7: Generate single protein prediction inputs

First generate AlphaFold3 inputs for all single proteins in your assigned FASTA.

```bash
python scripts/AF3_prepare.py $FASTA \
    --mode single \
    --outdir outputs/${TRACK}_single \
    --submit-script
```

Inspect the output folder:

```bash
ls outputs/${TRACK}_single
```

Each protein should have its own folder containing:

```text
input.json
job.sbatch
```

Inspect one input file:

```bash
less outputs/${TRACK}_single/P25705/input.json
```

The exact folder name depends on the protein IDs in your track.

---

## Step 8: Generate pair prediction inputs for the starting pair

Use the starting pair for your track.

### Track A

```bash
BAIT=P25705
PREY=P06576
```

### Track B

```bash
BAIT=P36542
PREY=P56381
```

### Track C

```bash
BAIT=P48047
PREY=Q5QNZ2
```

### Track D

```bash
BAIT=P00846
PREY=P03928
```

### Track E

```bash
BAIT=P48047
PREY=Q5QNZ2
```

Extract the bait and prey sequences from your track FASTA:

```bash
python - <<PY
from Bio import SeqIO
from pathlib import Path

fasta = Path("$FASTA")
bait = "$BAIT"
prey = "$PREY"

records = {record.id: record for record in SeqIO.parse(fasta, "fasta")}

missing = [x for x in [bait, prey] if x not in records]
if missing:
    raise SystemExit(f"Missing from {fasta}: {missing}")

SeqIO.write(records[bait], "bait.fasta", "fasta")
SeqIO.write(records[prey], "prey.fasta", "fasta")

print(f"Wrote bait.fasta: {bait}")
print(f"Wrote prey.fasta: {prey}")
PY
```

Generate the pair prediction input:

```bash
python scripts/AF3_prepare.py prey.fasta \
    --bait bait.fasta \
    --mode ppi \
    --outdir outputs/${TRACK}_starting_pair \
    --submit-script
```

Inspect the generated job:

```bash
find outputs/${TRACK}_starting_pair -maxdepth 3 -type f
```

Inspect the AlphaFold3 JSON:

```bash
less outputs/${TRACK}_starting_pair/${BAIT}_with_${PREY}/input.json
```

---

## Step 9: Generate pair prediction inputs for the comparison pair

Use the comparison pair for your track.

### Track A

```bash
BAIT=P25705
PREY=P40926
```

### Track B

```bash
BAIT=P36542
PREY=P38646
```

### Track C

```bash
BAIT=P48047
PREY=O96008
```

### Track D

```bash
BAIT=P00846
PREY=P21796
```

### Track E

```bash
BAIT=P06576
PREY=O96008
```

Extract the bait and prey sequences:

```bash
python - <<PY
from Bio import SeqIO
from pathlib import Path

fasta = Path("$FASTA")
bait = "$BAIT"
prey = "$PREY"

records = {record.id: record for record in SeqIO.parse(fasta, "fasta")}

missing = [x for x in [bait, prey] if x not in records]
if missing:
    raise SystemExit(f"Missing from {fasta}: {missing}")

SeqIO.write(records[bait], "bait.fasta", "fasta")
SeqIO.write(records[prey], "prey.fasta", "fasta")

print(f"Wrote bait.fasta: {bait}")
print(f"Wrote prey.fasta: {prey}")
PY
```

Generate the comparison pair prediction input:

```bash
python scripts/AF3_prepare.py prey.fasta \
    --bait bait.fasta \
    --mode ppi \
    --outdir outputs/${TRACK}_comparison_pair \
    --submit-script
```

Inspect the generated files:

```bash
find outputs/${TRACK}_comparison_pair -maxdepth 3 -type f
```

---

## Step 10: Generate a bait screen for your full track

A bait screen tests one selected bait protein against every protein in the track FASTA.

Use the bait protein for your track.

| Track | Bait protein |
|---|---|
| A | `P25705` |
| B | `P36542` |
| C | `P48047` |
| D | `P00846` |
| E | `P48047` |

Set the bait:

### Track A

```bash
BAIT=P25705
```

### Track B

```bash
BAIT=P36542
```

### Track C

```bash
BAIT=P48047
```

### Track D

```bash
BAIT=P00846
```

### Track E

```bash
BAIT=P48047
```

Extract the bait sequence:

```bash
python - <<PY
from Bio import SeqIO
from pathlib import Path

fasta = Path("$FASTA")
bait = "$BAIT"

records = {record.id: record for record in SeqIO.parse(fasta, "fasta")}

if bait not in records:
    raise SystemExit(f"Missing bait from {fasta}: {bait}")

SeqIO.write(records[bait], "bait.fasta", "fasta")
print(f"Wrote bait.fasta: {bait}")
PY
```

Generate the bait screen inputs:

```bash
python scripts/AF3_prepare.py $FASTA \
    --bait bait.fasta \
    --mode ppi \
    --skip-self \
    --outdir outputs/${TRACK}_${BAIT}_screen \
    --submit-script
```

Inspect the generated jobs:

```bash
find outputs/${TRACK}_${BAIT}_screen -name input.json | head
find outputs/${TRACK}_${BAIT}_screen -name job.sbatch | head
```

Count how many pairwise jobs were generated:

```bash
find outputs/${TRACK}_${BAIT}_screen -name job.sbatch | wc -l
```

---

## Step 11: Inspect the SLURM job script

Open one generated `job.sbatch` file:

```bash
less outputs/${TRACK}_${BAIT}_screen/*/job.sbatch
```

Check that it contains:

```text
#SBATCH --partition=gpu_ibl
#SBATCH --reservation=4022BIOIFY_2526_S2
#SBATCH --gres=gpu:rtx5000:1
module load alphafold/cc8_3-20250304
```

These settings are used for the 4022BIOIFY teaching reservation on the IBL GPU node.

---

## Step 12: Submit AlphaFold3 jobs

Submit the starting pair first.

```bash
cd outputs/${TRACK}_starting_pair
./submit_all.sh
cd ../..
```

Check the queue:

```bash
squeue -u $USER
```

Submit the comparison pair:

```bash
cd outputs/${TRACK}_comparison_pair
./submit_all.sh
cd ../..
```

Submit the bait screen after the starting pair and comparison pair have been submitted correctly:

```bash
cd outputs/${TRACK}_${BAIT}_screen
./submit_all.sh
cd ../..
```

Check the queue again:

```bash
squeue -u $USER
```

---

## Step 13: Check job output

Look for output and error logs:

```bash
find outputs -path "*logs*" -type f | head
```

Open a log file:

```bash
less outputs/${TRACK}_starting_pair/logs/*.out
```

Open an error file if a job failed:

```bash
less outputs/${TRACK}_starting_pair/logs/*.err
```

Useful checks:

```bash
grep -R "Finished" outputs/${TRACK}_starting_pair
grep -R "error" outputs/${TRACK}_starting_pair/logs
grep -R "Traceback" outputs/${TRACK}_starting_pair/logs
```

---

## Step 14: Find AlphaFold3 result files

AlphaFold3 creates output files inside the job folder. Use `find` to locate structure and confidence files.

```bash
find outputs/${TRACK}_starting_pair -type f | less
```

Look for files with names ending in:

```text
.cif
.json
```

Useful search commands:

```bash
find outputs/${TRACK}_starting_pair -name "*.cif"
find outputs/${TRACK}_starting_pair -name "*.json"
```

Repeat this for the comparison pair and bait screen.

---

## Step 15: Inspect predicted structures in PyMOL

Load a predicted structure file into PyMOL.

Example:

```bash
pymol model.cif
```

Use the actual `.cif` file produced by your job.

Basic PyMOL commands:

```pymol
hide everything
show cartoon
orient
```

Color chains separately:

```pymol
color marine, chain A
color orange, chain B
```

Show possible interface residues within 5 Angstrom:

```pymol
select interface_A, chain A within 5 of chain B
select interface_B, chain B within 5 of chain A
show sticks, interface_A or interface_B
zoom interface_A or interface_B
```

Save a PyMOL session:

```pymol
save prediction_session.pse
```

Save an image:

```pymol
png prediction_image.png, dpi=300
```

---

## Step 16: Interpret the starting pair

Record the following information in your notes.

```text
Track:
Starting pair:
Group:

Question                                           Answer
Which proteins were predicted?
Do the chains touch?
Is the interface compact?
Is the interface formed by structured regions?
Is local confidence high at the interface?
Is between chain PAE low, medium, high, or mixed?
Does the model suggest a plausible interaction?
What makes the prediction convincing?
What makes the prediction uncertain?
Should this pair be carried forward to Day 2 or Day 3?
```

Use one of these final call categories:

```text
likely direct interaction
possible interaction
uncertain
likely not direct
not interpretable from this prediction
```

---

## Step 17: Interpret the comparison pair

Record the same information for the comparison pair.

```text
Track:
Comparison pair:
Group:

Question                                           Answer
Which proteins were predicted?
Do the chains touch?
Is the interface compact?
Is the interface formed by structured regions?
Is local confidence high at the interface?
Is between chain PAE low, medium, high, or mixed?
Does the model suggest a plausible interaction?
What makes the prediction convincing?
What makes the prediction uncertain?
Should this pair be carried forward to Day 2 or Day 3?
```

Do not rely on visual contact alone. A model can show two chains touching while PAE still indicates that the relative placement is uncertain.

---

## Step 18: Summarize bait screen results

Use this table format in your notes.

```text
bait	candidate	chains_touch	interface_pLDDT	between_chain_PAE	3D_interface	final_call	short_reason
P25705	P06576	yes	high	low	compact	likely direct interaction	clear interface and supported confidence
P25705	P40926	no	high	high	none	likely not direct	no supported interface
```

Suggested values for `chains_touch`:

```text
yes
no
unclear
```

Suggested values for `interface_pLDDT`:

```text
high
medium
low
mixed
```

Suggested values for `between_chain_PAE`:

```text
low
medium
high
mixed
```

Suggested values for `3D_interface`:

```text
compact
partial
loose
none
suspicious
```

Suggested values for `final_call`:

```text
likely direct interaction
possible interaction
uncertain
likely not direct
not interpretable
```

---

## Step 19: Choose two examples for later days

At the end of Day 1, select two predictions from your track.

### Example 1: More convincing prediction

Choose a prediction that seems useful for further analysis.

Good signs:

1. The chains form a clear interface.
2. The interface is made of structured regions.
3. Interface residues have reasonable pLDDT.
4. Between chain PAE is relatively low.
5. The interaction makes biological sense.
6. Other weaker predictions in the same screen look clearly less convincing.

### Example 2: Weak, uncertain, or suspicious prediction

Choose a prediction that is difficult to interpret.

Possible reasons:

1. The chains barely touch.
2. The chains touch, but between chain PAE is high.
3. The interface is small, strange, or formed by low confidence tails.
4. One or both chains have low confidence.
5. The proteins are not expected to belong to the same stable subcomplex.
6. The model looks visually plausible but lacks confidence support.
7. The prediction may require larger complex context.

These two examples will be used later when analysing coevolution and structure.

---

## What to save

Save the following material for your final presentation:

1. A screenshot of one more convincing predicted interaction.
2. A screenshot of one weak or suspicious predicted interaction.
3. PAE plots or PAE summaries for both examples.
4. Notes on pLDDT at the interface.
5. Your bait screen result table.
6. A short explanation of why one prediction is more convincing than another.
7. The UniProt accession IDs of the proteins in your selected examples.
8. The AlphaFold3 output folder names for your selected examples.

---

## Decision rules for Day 1

### More convincing prediction

A prediction is more convincing if:

1. The chains form a clear interface.
2. The interface is made of structured regions.
3. pLDDT is high or reasonable at the interface.
4. PAE between chains is low or at least not uniformly high.
5. The interaction makes biological sense.
6. Other candidates in the same screen look clearly weaker.

### Less convincing prediction

A prediction is less convincing if:

1. The chains barely touch.
2. The interface is formed only by low confidence tails.
3. Between chain PAE is high.
4. The orientation of chains appears uncertain.
5. The proteins are not expected to be direct partners.
6. Similar looking contacts appear for many unrelated proteins.

### Not interpretable from Day 1 alone

A prediction may be difficult to interpret if:

1. It involves membrane proteins.
2. The interaction likely requires additional subunits.
3. The proteins are flexible or elongated.
4. The correct stoichiometry is not represented by the pairwise input.
5. The model is sensitive to small changes in input.
6. The predicted contact is mostly hydrophobic membrane helix packing without strong confidence support.

Use `uncertain` or `not interpretable` when the evidence does not support a clear conclusion.

---

## Common mistakes to avoid

### Mistake 1: Treating high pLDDT as proof of interaction

High pLDDT means local structure confidence. It does not guarantee that two chains are correctly positioned relative to each other.

### Mistake 2: Ignoring PAE

For complexes, PAE is essential. If two proteins are individually confident but have high between chain PAE, the predicted complex may be uncertain.

### Mistake 3: Calling all ATP synthase proteins direct interactors

Two proteins can belong to the same large complex without directly touching each other.

### Mistake 4: Calling all mitochondrial proteins ATP synthase partners

Mitochondria contain many unrelated proteins. Shared localization is not enough evidence for direct physical interaction.

### Mistake 5: Trusting every membrane protein contact

Hydrophobic helices can form plausible looking contacts. Membrane sector predictions require careful interpretation.

### Mistake 6: Overinterpreting one AlphaFold3 run

One prediction is not a complete biological experiment. Treat AlphaFold3 as a hypothesis generator and combine structure, confidence, biological context, and later coevolution analysis.

---

## Working questions

### AlphaFold and confidence

1. What does pLDDT tell you?
2. What does pLDDT not tell you?
3. What does PAE tell you?
4. Why is PAE especially useful for protein complexes?
5. Can two chains have high pLDDT but still form an uncertain complex?

### Starting pair

1. What is your starting pair?
2. Does the predicted model show a clear interface?
3. Is the interface locally confident?
4. Is the relative chain placement supported by PAE?
5. Would you call the interaction likely, possible, uncertain, or unlikely?

### Comparison pair

1. What is your comparison pair?
2. Does the predicted model show a clear interface?
3. How does the comparison pair differ from the starting pair?
4. Does the comparison pair help you interpret the bait screen?
5. What would make this prediction misleading?

### Bait screen

1. What is your bait protein?
2. Which candidate gave the most convincing prediction?
3. Which candidate gave the weakest prediction?
4. Did any unexpected candidate produce a plausible looking contact?
5. Which pair should be analysed further on Day 2?
6. Which pair should be inspected structurally on Day 3?

---

## Minimal PyMOL command list

Open a structure:

```bash
pymol model.cif
```

Basic view:

```pymol
hide everything
show cartoon
orient
```

Color chains:

```pymol
color marine, chain A
color orange, chain B
```

Show interface residues:

```pymol
select interface_A, chain A within 5 of chain B
select interface_B, chain B within 5 of chain A
show sticks, interface_A or interface_B
zoom interface_A or interface_B
```

Save a session:

```pymol
save my_prediction_session.pse
```

Save an image:

```pymol
png my_prediction_image.png, dpi=300
```

---

## Mini glossary

### AlphaFold

A computational system for predicting protein structures from amino acid sequence and related information.

### AlphaFold3

A computational system for predicting structures of biomolecular systems, including protein complexes.

### Protein complex

A structure formed by two or more protein chains that physically interact.

### ATP synthase

A mitochondrial protein complex that produces ATP by coupling proton movement to nucleotide catalysis.

### UniProt accession

A stable identifier for a protein sequence entry in UniProt. In this practical, FASTA headers use UniProt accession IDs.

### MSA

Multiple sequence alignment. A set of related protein sequences aligned so that equivalent positions can be compared.

### Coevolution

Coordinated evolutionary change between residues or proteins. Coevolution can suggest structural or functional relationships.

### pLDDT

Predicted local distance difference test. A per residue confidence score for local structure.

### PAE

Predicted aligned error. A confidence estimate for the relative placement of residues, domains, or chains.

### Interface

The region where two protein chains physically contact each other.

### Bait protein

The protein used as the fixed query in a screen against candidate partners.

### Candidate protein

A protein tested as a possible interaction partner for the bait.

### Stoichiometry

The number of copies of each protein chain in a complex.

---

## References and further reading

1. Jumper J, Evans R, Pritzel A, Green T, Figurnov M, Ronneberger O, et al. Highly accurate protein structure prediction with AlphaFold. Nature. 2021;596:583 to 589. DOI: 10.1038/s41586-021-03819-2.

2. Evans R, O'Neill M, Pritzel A, Antropova N, Senior A, Green T, et al. Protein complex prediction with AlphaFold-Multimer. bioRxiv. 2021. DOI: 10.1101/2021.10.04.463034.

3. Abramson J, Adler J, Dunger J, Evans R, Green T, Pritzel A, et al. Accurate structure prediction of biomolecular interactions with AlphaFold 3. Nature. 2024;630:493 to 500. DOI: 10.1038/s41586-024-07487-w.

4. Marks DS, Colwell LJ, Sheridan R, Hopf TA, Pagnani A, Zecchina R, et al. Protein 3D structure computed from evolutionary sequence variation. PLoS One. 2011;6:e28766. DOI: 10.1371/journal.pone.0028766.

5. Senior AW, Evans R, Jumper J, Kirkpatrick J, Sifre L, Green T, et al. Improved protein structure prediction using potentials from deep learning. Nature. 2020;577:706 to 710. DOI: 10.1038/s41586-019-1923-7.

6. Varadi M, Anyango S, Deshpande M, Nair S, Natassia C, Yordanova G, et al. AlphaFold Protein Structure Database: massively expanding the structural coverage of protein sequence space with high accuracy models. Nucleic Acids Research. 2022;50:D439 to D444. DOI: 10.1093/nar/gkab1061.

---

## End of Day 1

Today you used AlphaFold3 to prepare and run protein complex predictions for human mitochondrial ATP synthase protein panels. You generated pairwise predictions, inspected confidence metrics, and made cautious first interpretations using pLDDT, PAE, and 3D structure.

On Day 2, you will go one layer deeper and ask whether sequence coevolution supports the predicted interactions.
