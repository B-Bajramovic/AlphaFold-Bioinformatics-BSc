# Day 1: AlphaFold prediction of protein complexes

## From sequence to structure in the mitochondrial ATP synthase

Welcome to Day 1 of the protein complex prediction module.

In this module, you will investigate how protein sequences can be used to predict protein structures and protein complexes. The biological case study is the mitochondrial ATP synthase, a large molecular machine that produces ATP by coupling proton movement through a membrane sector to catalysis in a soluble catalytic sector.

Today you will use AlphaFold to predict protein structures and protein-protein interactions. Later in the module, you will analyse coevolutionary signals and inspect predicted structures in more detail. The work you do today will provide material for your final group presentation.

You do not need to submit a formal report today. Instead, use the tables and questions in this page as scaffolding. They are designed to help you collect useful results, screenshots, and interpretations for your presentation.

---

## Learning goals

By the end of Day 1, you should be able to:

1. Explain why protein structure prediction is useful in biology.
2. Describe, at a basic conceptual level, why evolutionary information can help predict protein structures.
3. Explain the difference between predicting a single protein and predicting a protein complex.
4. Prepare or inspect AlphaFold input files for protein complex prediction.
5. Submit or inspect AlphaFold prediction jobs on HPC ALICE.
6. Interpret basic AlphaFold confidence outputs using pLDDT and PAE.
7. Open predicted protein structures in PyMOL.
8. Make an initial, cautious judgement about whether a predicted protein-protein interaction is plausible.

---

## The big idea

Proteins evolve under structural and functional constraints. If two residues physically contact each other, a mutation in one position may be compensated by a mutation in the other. Across many related sequences, this can create a coevolutionary signal.

AlphaFold uses amino acid sequence information, multiple sequence alignments, structural patterns learned from known protein structures, and learned residue-pair representations to predict 3D structures.

For protein complexes, the challenge is harder. AlphaFold must not only predict how each protein chain folds, but also whether the chains interact and how they are positioned relative to each other.

Today, you will not try to prove the full ATP synthase structure. Instead, you will use AlphaFold as a hypothesis-generating tool.

---

## Important caution

A predicted structure is not the same as experimental proof.

For today, your goal is to ask:

> Does AlphaFold produce a plausible interaction model for this protein pair?

You should not conclude:

> These proteins definitely interact in vivo.

or

> These proteins do not interact in vivo because one pairwise prediction looked weak.

The mitochondrial ATP synthase is a large multi-subunit complex. Some real interactions only make sense in the full assembly, in a membrane environment, or with additional partner proteins present. Pairwise prediction is a useful teaching strategy, but it is a simplification.

---

## Day 1 schedule

### Part 1: Lecture, 1 hour

The lecture introduces the principles behind computational protein structure prediction.

Main topics:

1. Why protein structure matters.
2. Why experimental structure determination can be difficult.
3. How sequence evolution contains structural information.
4. What multiple sequence alignments are.
5. How coevolution can reveal residue contacts.
6. How AlphaFold uses sequence, MSA, and pairwise residue information.
7. Why protein complex prediction is harder than single-chain prediction.
8. How to interpret pLDDT and PAE.
9. Why ATP synthase is a useful case study.

### Part 2: Practical, 2 hours

The practical is divided into three stages:

1. **Anchor prediction**  
   You inspect or run one known or likely protein-protein interaction.

2. **Bait screen**  
   You test one ATP synthase protein against a panel of candidate proteins. Some candidates are expected ATP synthase partners, while others are negative controls or distractors.

3. **Initial interpretation**  
   You inspect pLDDT, PAE, and 3D structure in PyMOL. You choose useful examples to carry forward into later days.

---

## Before you start

Make sure you can:

1. Log into ALICE.
2. Move through folders using `cd`.
3. List files using `ls`.
4. View files using `less`, `head`, or `cat`.
5. Submit jobs using `sbatch`.
6. Check the queue using `squeue`.
7. Open or download predicted structures for PyMOL inspection.

Useful basic commands:

```bash
pwd
ls
cd folder_name
cd ..
less filename
head filename
grep ">" sequences.fasta
```

---

## Repository structure

Your course repository may look like this:

```text
day1_alphafold/
  README.md
  data/
    protein_subset.fasta
    protein_metadata.tsv
    group_tracks.tsv
  candidate_panels/
    track_A_F1_head.tsv
    track_B_central_stalk.tsv
    track_C_peripheral_stalk.tsv
    track_D_Fo_membrane.tsv
    track_E_decoy_screen.tsv
  inputs/
    anchor_pairs/
    bait_screens/
  jobs/
    template_job.sbatch
  scripts/
    make_pair_input.py
    make_bait_screen_inputs.py
  results_precomputed/
    anchor_pairs/
    bait_screens/
  student_outputs/
```

Your instructor may adjust the exact folder names and ALICE paths.

---

## Protein complex prediction strategy

Today you will use a prediction ladder.

### Stage 1: Anchor pair

Each group starts with one assigned protein pair. This is your controlled starting point.

The anchor pair should help you learn:

1. What an AlphaFold input file looks like.
2. What output files are produced.
3. How to inspect pLDDT.
4. How to inspect PAE.
5. How to open the predicted model in PyMOL.
6. What a plausible or implausible interface looks like.

### Stage 2: Bait screen

After the anchor pair, your group will work with one bait protein.

The bait protein is tested against a panel of candidate proteins. Some candidates are expected to be related to ATP synthase. Others are mitochondrial proteins that are not expected to be direct ATP synthase interaction partners.

This creates a small protein-protein interaction screen.

The question is:

> Which candidate proteins produce the most plausible interaction predictions with the bait?

### Stage 3: Selection for later days

At the end of Day 1, choose:

1. One strong or plausible predicted interaction.
2. One weak, uncertain, or suspicious predicted interaction.

You will use these examples later when analysing coevolution and protein interface structure.

---

## Group tracks

The class is divided into five tracks. Each track focuses on a different region or interpretation problem of ATP synthase.

Your instructor will assign each group to one track.

| Track | Focus | Main teaching idea |
|---|---|---|
| A | F1 catalytic head | Soluble ATP synthase subunits can form clear predicted interfaces |
| B | Central stalk | Some true biological interactions depend on larger complex context |
| C | Peripheral stalk / stator | Interfaces can connect soluble and membrane-associated parts |
| D | Fo membrane sector | Membrane proteins are important but harder to interpret |
| E | ATP synthase versus decoys | Shared mitochondrial localization does not mean direct interaction |

---

## Track A: F1 catalytic head

### Biological focus

The F1 region contains the soluble catalytic part of ATP synthase. It includes alpha and beta subunits that form the catalytic head.

### Anchor pair

```text
ATP5F1A x ATP5F1B
```

### Suggested bait protein

```text
ATP5F1A
```

### Candidate panel

```text
ATP5F1B
ATP5F1C
ATP5F1D
ATP5F1E
ATP5PO
ATP5PB
ATP5PD
ATP5PF
MT-ATP6
MT-ATP8
ATP5MC1
MDH2
NDUFA9
UQCRC1
COX5A
TOMM20
HSPD1
VDAC1
```

### Questions for this track

1. Does ATP5F1A form a plausible interface with ATP5F1B?
2. Do other F1 subunits produce plausible interactions?
3. Do unrelated mitochondrial proteins produce weaker predictions?
4. Can you distinguish “same complex” from “direct interaction”?

---

## Track B: Central stalk

### Biological focus

The central stalk connects the catalytic head to the rotating membrane sector. Some interactions may depend strongly on the full ATP synthase assembly.

### Anchor pair

```text
ATP5F1D x ATP5F1E
```

### Suggested bait protein

```text
ATP5F1C
```

### Candidate panel

```text
ATP5F1A
ATP5F1B
ATP5F1D
ATP5F1E
ATP5PO
ATP5PB
ATP5PD
ATP5PF
ATP5MC1
MT-ATP6
MT-ATP8
ATP5ME
ATP5MF
ATP5MG
MDH2
NDUFS2
COX4I1
TOMM40
HSPA9
```

### Questions for this track

1. Which candidates produce plausible interactions with ATP5F1C?
2. Are the predicted interactions compact or extended?
3. Do the models suggest a stable pairwise interaction or a context-dependent assembly?
4. Which predictions should be treated cautiously?

---

## Track C: Peripheral stalk / stator

### Biological focus

The peripheral stalk helps hold the catalytic head in place while the central rotor turns. This region connects the F1 head to the membrane sector.

### Anchor pair

```text
ATP5PO x ATP5PB
```

Alternative anchor pair if assigned:

```text
ATP5PB x ATP5PD
```

### Suggested bait protein

```text
ATP5PO
```

### Candidate panel

```text
ATP5PB
ATP5PD
ATP5PF
ATP5F1A
ATP5F1B
ATP5F1C
ATP5F1D
ATP5F1E
ATP5ME
ATP5MF
ATP5MG
MT-ATP6
MT-ATP8
ATP5MC1
NDUFA9
UQCRC2
COX5B
HSPD1
VDAC1
```

### Questions for this track

1. Which proteins produce plausible interactions with ATP5PO?
2. Are any predictions difficult to interpret because of elongated or flexible regions?
3. Does PAE support a confident relative placement of chains?
4. Which candidates might require a larger complex context?

---

## Track D: Fo membrane sector

### Biological focus

The Fo region is membrane-associated and contains the proton channel and rotor components. These proteins can be more difficult to interpret because hydrophobic membrane helices may form plausible-looking contacts.

### Anchor pair

```text
MT-ATP6 x ATP5MC1
```

Alternative anchor pair if assigned:

```text
MT-ATP6 x MT-ATP8
```

### Suggested bait protein

```text
MT-ATP6
```

### Candidate panel

```text
MT-ATP8
ATP5MC1
ATP5ME
ATP5MF
ATP5MG
ATP5MJ
ATP5MK
ATP5PB
ATP5PD
ATP5PF
ATP5PO
ATP5F1C
ATP5F1D
ATP5F1E
COX1
COX2
CYB
UQCRB
NDUFA1
```

### Questions for this track

1. Which candidates produce plausible membrane-sector interactions?
2. Are the predicted interfaces formed by transmembrane helices?
3. Could hydrophobic membrane helices create misleading contacts?
4. Which predictions would need extra evidence before being trusted?

---

## Track E: ATP synthase versus mitochondrial decoys

### Biological focus

Not every mitochondrial protein belongs to ATP synthase. This track tests whether AlphaFold predictions can help separate likely ATP synthase interactions from unrelated mitochondrial proteins.

### Anchor pair

Positive control:

```text
ATP5F1A x ATP5F1B
```

Negative control:

```text
ATP5F1A x MDH2
```

### Suggested bait protein

```text
ATP5F1A
```

Alternative bait proteins may be assigned:

```text
ATP5PO
MT-ATP6
```

### Candidate panel

```text
ATP5F1B
ATP5F1C
ATP5PO
ATP5PB
ATP5PD
MT-ATP6
ATP5MC1
MDH2
IDH3A
HSPD1
HSPA9
TOMM20
TOMM40
VDAC1
NDUFA9
NDUFS2
UQCRC1
COX5A
CYCS
```

### Questions for this track

1. Which candidates look like plausible ATP synthase partners?
2. Which candidates look like likely negatives?
3. Do any decoy proteins produce misleading contacts?
4. Why is mitochondrial localization alone not enough evidence for direct interaction?

---

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

Check that you can see the main folders:

```bash
ls data
ls candidate_panels
ls scripts
ls results_precomputed
```

---

## Step 2: Find your group track

Open the group assignment file:

```bash
column -t -s $'\t' data/group_tracks.tsv | less -S
```

Find your group number.

Example table structure:

```text
group_id    track    anchor_pair         bait
group_01    A        ATP5F1A_ATP5F1B     ATP5F1A
group_02    A        ATP5F1A_ATP5F1B     ATP5F1A
group_06    B        ATP5F1D_ATP5F1E     ATP5F1C
group_11    C        ATP5PO_ATP5PB       ATP5PO
group_16    D        MT-ATP6_ATP5MC1     MT-ATP6
group_21    E        ATP5F1A_ATP5F1B     ATP5F1A
```

Open your candidate panel:

```bash
column -t -s $'\t' candidate_panels/track_A_F1_head.tsv | less -S
```

Replace `track_A_F1_head.tsv` with the panel assigned to your group.

---

## Step 3: Inspect the protein metadata

Open the metadata table:

```bash
column -t -s $'\t' data/protein_metadata.tsv | less -S
```

This file may contain:

```text
protein_id    short_name    description                         category
P25705        ATP5F1A       ATP synthase F1 subunit alpha        ATP_synthase
P06576        ATP5F1B       ATP synthase F1 subunit beta         ATP_synthase
P36542        ATP5F1C       ATP synthase F1 subunit gamma        ATP_synthase
P30049        ATP5F1D       ATP synthase F1 subunit delta        ATP_synthase
P56381        ATP5F1E       ATP synthase F1 subunit epsilon      ATP_synthase
P40925        MDH2          malate dehydrogenase 2               decoy
```

The exact contents may differ depending on the course dataset.

Inspect the FASTA file:

```bash
grep ">" data/protein_subset.fasta
```

Count the number of proteins:

```bash
grep -c ">" data/protein_subset.fasta
```

---

## Step 4: Inspect your anchor pair input

Your anchor pair input may already be prepared.

Example:

```bash
ls inputs/anchor_pairs
less inputs/anchor_pairs/ATP5F1A_ATP5F1B/input.json
```

Try to identify:

1. The job name.
2. The protein chains.
3. The sequence of chain A.
4. The sequence of chain B.
5. Whether this is a single-chain or multi-chain prediction.

Questions:

1. Which proteins are included?
2. Are the proteins identical or different?
3. Is the prediction testing a direct protein-protein interaction?
4. What would count as a convincing result?

---

## Step 5: Run or inspect the anchor prediction

Your instructor will tell you whether to submit the anchor job yourself or inspect a precomputed result.

If you submit the job:

```bash
cd inputs/anchor_pairs/ATP5F1A_ATP5F1B
sbatch job.sbatch
squeue -u $USER
```

If you inspect a precomputed result:

```bash
ls results_precomputed/anchor_pairs/ATP5F1A_ATP5F1B
```

Look for:

```text
structure file
PAE plot or PAE data
confidence file
ranking file
log file
```

File names may differ depending on the AlphaFold version and local setup.

---

## Step 6: Inspect pLDDT

pLDDT is a per-residue confidence score.

General interpretation:

| pLDDT range | Approximate interpretation |
|---|---|
| > 90 | Very high local confidence |
| 70 to 90 | Confident local structure |
| 50 to 70 | Low confidence or flexible region |
| < 50 | Very low confidence, often disorder or unreliable local structure |

Questions:

1. Are most residues high confidence?
2. Are low-confidence residues located in tails, loops, or entire domains?
3. Are interface residues high confidence?
4. Is one chain more confident than the other?

Important:

High pLDDT means AlphaFold is confident about the local structure. It does not automatically mean the protein-protein interaction is correct.

---

## Step 7: Inspect PAE

PAE means predicted aligned error. It helps estimate whether AlphaFold is confident about the relative positions of residues, domains, or chains.

For protein complexes, PAE is especially useful.

Questions:

1. Is PAE low within each chain?
2. Is PAE low between the two chains?
3. Does AlphaFold seem confident about the relative placement of the proteins?
4. Are there blocks of high PAE between chains, suggesting uncertain interaction geometry?

General interpretation:

| PAE pattern | Possible interpretation |
|---|---|
| Low PAE within chains, low PAE between chains | More plausible stable complex |
| Low PAE within chains, high PAE between chains | Chains may fold well individually, but relative placement is uncertain |
| High PAE in flexible regions | Disorder or flexibility may be present |
| Mixed PAE between chains | Some parts may be confidently positioned, others uncertain |

---

## Step 8: Open the model in PyMOL

Open your structure file in PyMOL.

Example:

```bash
pymol results_precomputed/anchor_pairs/ATP5F1A_ATP5F1B/ranked_model.cif
```

The file name may differ. Use `ls` to find the correct structure file.

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
```

Zoom in on the interface:

```pymol
zoom interface_A or interface_B
```

Questions:

1. Do the chains touch?
2. Is the interface compact?
3. Is the interface made of ordered regions?
4. Does the structure look physically plausible?
5. Do the chains form one coherent complex or do they appear loosely arranged?

---

## Step 9: Record your anchor pair interpretation

Use this table in your notes. You do not need to submit it today, but it will help you prepare your presentation.

```text
Anchor pair:
Track:
Group:

Question                                           Answer
Which proteins were predicted?
Do the chains touch?
Is local confidence high at the interface?
Is between-chain PAE low, medium, or high?
Does the model suggest a plausible interaction?
What makes the prediction convincing?
What makes the prediction uncertain?
Would you carry this pair forward to Day 2 or Day 3?
```

Suggested final call categories:

```text
likely direct interaction
possible interaction
uncertain
likely negative
not interpretable from this prediction
```

---

## Step 10: Prepare the bait screen

The bait screen tests one bait protein against several candidate proteins.

Example:

```text
bait: ATP5F1A

candidates:
ATP5F1B
ATP5F1C
ATP5F1D
ATP5F1E
ATP5PO
MDH2
NDUFA9
COX5A
...
```

Your instructor may provide prepared input files for all bait-candidate pairs.

Example:

```bash
ls inputs/bait_screens/track_A_F1_head
```

If input files must be generated:

```bash
python scripts/make_bait_screen_inputs.py \
  --fasta data/protein_subset.fasta \
  --bait ATP5F1A \
  --candidate-table candidate_panels/track_A_F1_head.tsv \
  --outdir student_outputs/track_A_F1_head_ATP5F1A_screen
```

Check the generated folders:

```bash
ls student_outputs/track_A_F1_head_ATP5F1A_screen
```

---

## Step 11: Run or inspect bait screen predictions

Your instructor will tell you whether to run a small number of predictions yourself or inspect precomputed outputs.

If submitting one candidate job:

```bash
cd student_outputs/track_A_F1_head_ATP5F1A_screen/ATP5F1A_ATP5F1B
sbatch job.sbatch
squeue -u $USER
```

If inspecting precomputed results:

```bash
ls results_precomputed/bait_screens/track_A_F1_head
```

Do not spend all your time on one model. The goal is to compare several predictions.

---

## Step 12: Summarize bait screen results

Use this table as a working template.

You do not need perfect answers today. The goal is to organize observations for later analysis.

```text
bait	candidate	chains_touch	interface_pLDDT	between_chain_PAE	3D_interface	final_call	short_reason
ATP5F1A	ATP5F1B	yes	high	low	compact	likely direct interaction	F1 catalytic head proteins
ATP5F1A	ATP5F1C	yes	medium	medium	partial	possible interaction	may require larger complex context
ATP5F1A	MDH2	no	high	high	none	likely negative	mitochondrial enzyme, no clear interface
```

Suggested values:

For `chains_touch`:

```text
yes
no
unclear
```

For `interface_pLDDT`:

```text
high
medium
low
mixed
```

For `between_chain_PAE`:

```text
low
medium
high
mixed
```

For `3D_interface`:

```text
compact
partial
loose
none
suspicious
```

For `final_call`:

```text
likely direct interaction
possible interaction
uncertain
likely negative
not interpretable
```

---

## Step 13: Choose examples for later days

At the end of Day 1, choose two examples from your track.

### Example 1: Strong or plausible interaction

Choose a prediction that seems worth further analysis.

Good signs:

1. Chains form a clear interface.
2. Interface residues have reasonable pLDDT.
3. Between-chain PAE is relatively low.
4. The interaction makes biological sense.
5. The pair belongs to a plausible ATP synthase subcomplex.

### Example 2: Weak, uncertain, or suspicious interaction

Choose a prediction that is difficult to interpret.

Possible reasons:

1. Chains touch, but between-chain PAE is high.
2. The interface is small or strange.
3. One or both chains have low confidence.
4. The proteins are likely unrelated.
5. The model looks plausible visually but lacks confidence support.
6. The prediction may require larger complex context.

These two examples will be useful for Day 2 and Day 3.

---

## What to save for your presentation

You do not need to submit anything today, but you should save useful material.

Recommended items:

1. Screenshot of one plausible predicted interaction.
2. Screenshot of one weak or suspicious predicted interaction.
3. PAE plot or PAE summary for both examples.
4. Short notes on pLDDT at the interface.
5. Your bait screen result table.
6. A short explanation of why you trust one prediction more than another.

Your final presentation will be easier if you collect these items today.

---

## Decision rules for Day 1

Use these rules as a first-pass guide.

### More convincing prediction

A prediction is more convincing if:

1. The chains form a clear interface.
2. The interface is made of structured regions.
3. pLDDT is high or reasonable at the interface.
4. PAE between chains is low or at least not uniformly high.
5. The interaction makes biological sense.
6. A negative control looks clearly worse.

### Less convincing prediction

A prediction is less convincing if:

1. The chains barely touch.
2. The interface is formed only by low-confidence tails.
3. Between-chain PAE is high.
4. The orientation of chains appears uncertain.
5. The proteins are not expected to be in the same complex.
6. Similar-looking contacts appear for many unrelated proteins.

### Not interpretable from Day 1 alone

A prediction may be difficult to interpret if:

1. It involves membrane proteins.
2. The interaction likely requires additional subunits.
3. The proteins are flexible or elongated.
4. The correct stoichiometry is unknown.
5. The model is sensitive to small changes in input.

In these cases, do not force a yes or no answer. Use `uncertain` or `not interpretable`.

---

## Common mistakes to avoid

### Mistake 1: Treating high pLDDT as proof of interaction

High pLDDT means local structure confidence. It does not guarantee that two chains are correctly positioned relative to each other.

### Mistake 2: Ignoring PAE

For complexes, PAE is essential. If two proteins are individually confident but have high between-chain PAE, the interaction may be uncertain.

### Mistake 3: Calling all ATP synthase proteins direct interactors

Two proteins can belong to the same large complex without directly touching each other.

### Mistake 4: Calling all mitochondrial proteins ATP synthase partners

Mitochondria contain many unrelated proteins. Shared localization is not enough evidence for direct interaction.

### Mistake 5: Trusting every membrane protein contact

Hydrophobic helices can form plausible-looking contacts. Membrane-sector predictions need careful interpretation.

---

## Day 1 working questions

Use these questions to guide your group discussion.

### AlphaFold and confidence

1. What does pLDDT tell you?
2. What does pLDDT not tell you?
3. What does PAE tell you?
4. Why is PAE especially useful for protein complexes?
5. Can two chains have high pLDDT but still form an uncertain complex?

### Anchor pair

1. What is your anchor pair?
2. Does the predicted model show a clear interface?
3. Is the interface locally confident?
4. Is the relative chain placement supported by PAE?
5. Would you call the interaction likely, possible, uncertain, or unlikely?

### Bait screen

1. What is your bait protein?
2. Which candidate gave the most convincing prediction?
3. Which candidate gave the weakest prediction?
4. Did any negative or decoy protein produce a misleading result?
5. Which pair do you want to analyse further on Day 2?
6. Which pair do you want to inspect structurally on Day 3?

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

## Optional: useful file checks

Count proteins in the FASTA file:

```bash
grep -c ">" data/protein_subset.fasta
```

List protein names:

```bash
grep ">" data/protein_subset.fasta
```

Find all result folders:

```bash
find results_precomputed -maxdepth 3 -type d
```

Find structure files:

```bash
find results_precomputed -name "*.cif" -o -name "*.pdb"
```

Find possible PAE files:

```bash
find results_precomputed -iname "*pae*"
```

Find log files:

```bash
find results_precomputed -name "*.out" -o -name "*.err" -o -name "*.log"
```

---

## Mini glossary

### AlphaFold

A computational system for predicting protein structures from amino acid sequence and related information.

### Protein complex

A structure formed by two or more protein chains that physically interact.

### MSA

Multiple sequence alignment. A set of related protein sequences aligned so that equivalent positions can be compared.

### Coevolution

Coordinated evolutionary change between residues or proteins. Coevolution can suggest structural or functional relationships.

### pLDDT

Predicted local distance difference test. A per-residue confidence score for local structure.

### PAE

Predicted aligned error. A confidence estimate for the relative placement of residues, domains, or chains.

### Interface

The region where two protein chains physically contact each other.

### Bait protein

The protein used as the fixed query in a screen against many candidate partners.

### Candidate protein

A protein tested as a possible interaction partner for the bait.

### Decoy

A protein included as a likely negative control or distractor.

### Stoichiometry

The number of copies of each protein chain in a complex.

---

## References and further reading

1. Jumper J, Evans R, Pritzel A, et al. Highly accurate protein structure prediction with AlphaFold. Nature. 2021;596:583 to 589. DOI: 10.1038/s41586-021-03819-2.

2. Evans R, O'Neill M, Pritzel A, et al. Protein complex prediction with AlphaFold-Multimer. bioRxiv. 2021. DOI: 10.1101/2021.10.04.463034.

3. Abramson J, Adler J, Dunger J, et al. Accurate structure prediction of biomolecular interactions with AlphaFold 3. Nature. 2024;630:493 to 500. DOI: 10.1038/s41586-024-07487-w.

4. Marks DS, Colwell LJ, Sheridan R, et al. Protein 3D structure computed from evolutionary sequence variation. PLoS One. 2011;6:e28766. DOI: 10.1371/journal.pone.0028766.

5. Senior AW, Evans R, Jumper J, et al. Improved protein structure prediction using potentials from deep learning. Nature. 2020;577:706 to 710. DOI: 10.1038/s41586-019-1923-7.

6. Varadi M, Anyango S, Deshpande M, et al. AlphaFold Protein Structure Database: massively expanding the structural coverage of protein sequence space with high accuracy models. Nucleic Acids Research. 2022;50:D439 to D444. DOI: 10.1093/nar/gkab1061.

---

## End of Day 1

Today you used AlphaFold to generate or inspect protein complex predictions. You learned how to make an initial judgement using pLDDT, PAE, and 3D structure inspection.

On Day 2, you will go one layer deeper and ask whether sequence coevolution supports the predicted interactions.
