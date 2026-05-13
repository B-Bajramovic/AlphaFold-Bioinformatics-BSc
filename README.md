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
