# Day 2 Understanding your AlphaFold output

### Step 0 downloading a file from ALICE to your PC

#### Mac/Linux users

```bash
#for a file
scp username@login.alice.universiteitleiden.nl:/path/to/file /path/to/folder/on/your/pc
#for a folder
scp -r username@login.alice.universiteitleiden.nl:/path/to/folder /path/to/folder/on/your/pc
```

#### Windows users

You can do the same as the Mac users with the MobaXterm home terminal, or you can simply use the interactive file explorer on the left. If this is malfuctioning, restart your session. If still malfunctioning, you may have unintentionally disabled the option and should ask for help.

#### back to all users

### step 1 colour by pLDDT

Now that you know how to transfer a file, please download the comparison pair model cif, starting pair model cif, and the corresponding single protein model cifs. 

If you have already installed PyMOL, you should be able to open these files by clicking on them. What do you see? Can you already tell if your structure makes sense? 

The basic PyMOL visualisation lacks information. Lets colour it by AlphaFolds pLDDT metrics. This data is stored inside the structure file, so PyMOL can find it and use it to colour your protein. The following code can be pasted into the PyMOL terminal.

```bash
# in case you messed around, this resets the cartoon representation 
hide everything, all
show cartoon, all
set cartoon_highlight_color, default
set cartoon_transparency, 0

# These are the official AlphaFold DB pLDDT colors
#   > 90   : #0053D6 (deep blue)
#   > 70   : #65CBF3 (light blue)
#   > 50   : #FFDB13 (yellow)
#   <= 50  : #FF7D45 (orange-red)

set_color af_blue,   [0.00, 0.32549, 0.83922]  # 0x0053D6
set_color af_cyan,   [0.39608, 0.79608, 0.95294]  # 0x65CBF3
set_color af_yellow, [1.0, 0.85882, 0.07451]  # 0xFFDB13
set_color af_orange, [1.0, 0.49020, 0.27059]  # 0xFF7D45

# Because of a technical difficulty selecting the value 0, I first colour everything by the lowest-confidence color
color af_orange, all

# Then override stepwise at higher pLDDT cutoffs
# (only '>' comparisons so PyMOL won't complain)
color af_yellow, (all and b > 50)
color af_cyan,   (all and b > 70)
color af_blue,   (all and b > 90)

# Optional: a tiny bit of smoothing, but still minimal
set cartoon_smooth_loops, on
set cartoon_flat_sheets, 1

# your model should now look prettier and more informative. Call for help if not.
```

Now we save

```bash

# to make a photo, you can use two commands: ray or draw. The ray command uses ray tracing and makes a pretty image but is more computationally heavy. draw is more like a screenshot.
ray
png filename, dpi=300

# or

draw
png filename, dpi=300

# before closing your work, save the session by doing:
save filename.pse

# you can reopen this session in the following steps. close it for now to not overload your PC
```

Perform these steps for both the single protein models of your comparison and starting pair, and for the interaction models of the comparsion and starting pairs. Save your work in a PNG. 


### step 2 analyse the interaction residues

The previous step only looked at pLDDT values. Now lets take a look at interactions. Open the starting pair session you made and run the following code. 

```
select interface_A, chain A within 5 of chain B
select interface_B, chain B within 5 of chain A
show sticks, interface_A or interface_B
zoom interface_A or interface_B
```

Now you should have selections within PyMOL that contain the amino acids which interact. Chain A is one of the pair, and chain B the other. To increase contrast between them, give them separate colours.

```
color wheat, chain A

color aquamarine, chain B
```

Now the sidechains of interacting residues can be better distinguished, but you can further enhance it by coloring them separately or by increasing transparency of your cartoon.

```
# for colouring selections you can do this, but of course feel free to change color as you wish
set stick_color, sand, interface_A

set stick_color, deepteal, interface_B

# and for transparency you can do this
set cartoon_transparency, 0.65
```

To know which residues are the interacting ones, you can set labels with the L button on the top right, or through command line.

```
cmd.label('''byca(interface_A)''', 'oneletter+resi')
cmd.label('''byca(interface_B)''', 'oneletter+resi')
#if this creates too many, you can manually click on the residues you are interested in to create a new selection. Then you can create labels for your curated selection
#to hide labels use
hide labels
```

Finally, you can identify the most important contacts by using the find polar contacts function. This can be done through the command line, but its simpler to simply click the "A" button at the whole object selection, and to click on the find polar contacts function there. Choose between chains to see how the chains interact. Zoom in on the interface, and save it as a png after running ray or draw like before.

To check whether your interface is reliable by pLDDT, you can use the previous coloring command to recolour your structure back to blue/yellow/red pLDDt. Is your interface in a reliable zone?

### Step 3 generating a PAE plot

Now we go back to ALICE. For each protein structure file you have analysed, there will additionally be a confidences.json file in the same output folder. inside it, the PAE matrix is kept in numeric format. We will use that to create our PAE plots. 

First you need to activate a conda environment i prepared for you.

```bash
conda activate /zfsstore/courses/2025-2026/4022BIOIFY/conda/python_pae
```
then you need navigate to your groupfolder e.g. 

```bash
cd /zfsstore/courses/2025-2026/4022BIOIFY/backup/10E_s4280032_s4298500
```
From here you can run the script to plot a PAE plot of your structure file of interest e.g.

```bash
python ../../scripts/plot_PAE.py outputs/track_E_single/P00846/p00846/p00846_confidences.json
```
The output png should be in your group folder if this is where you ran the script from. Windows users can use the MobaXterm file explorer to view the plot, and Mac users will need to use the scp command to download the png to their PC. 

You can make a plot for each structure you are interested in.

I also made a script to plot the contact probability per residue, but this is only indicative and less reliable. It can be useful when you are uncertain of the interaction. To plot it you can use the same python_pae environment to run the following:

```bash
python ../../scripts/plot_contact_probability.py outputs/track_E_comparison_pair/P06576_with_O96008/p06576_with_o96008/p06576_with_o96008_confidences.json
```

### Step 4 calculating binding energy

For this step we will use the tool Prodigy. https://github.com/haddocking/prodigy

I already installed it for you into a conda environment but you can take a look at the github for more information. In general, a binding energy below -8 kcal/mol is considered interesting, and more negative than -11 is suspiciously strong binding. A too strong binding can be an indication of unrealistic interaction. 

To run prodigy you need to activate the conda environment i prepared.

```bash
conda activate /zfsstore/courses/2025-2026/4022BIOIFY/conda/prodigy
```
Then you can run prodigy on the folder that contains your cif structure of interest e.g. 

```bash
prodigy outputs/track_E_comparison_pair/P06576_with_O96008/p06576_with_o96008/
```
To run prodigy on many structure files, you need to put the files of interest into a single folder. We will do this for the screening of bait against the whole track dataset. To collect cifs and put them into a folder, i made a script for you. run it from your group folder. 

```bash
bash ../../scripts/collect_cifs.sh outputs/track_E_P48047_screen/ screening
```
now you will have a folder in your group folder with all cif files under collected_cifs/screening/

Use it to run prodigy on all interactions. Which interactions are good? which are bad? Use the methods practiced previously to analyse the interactions and explain which ones are likely real. 

### Step 5 Investigate on your own
Once you have found your best interactions, use available online resources to further investigate whether the interaction is real. i.e. search databases such as uniprot to figure out what proteins you are looking at. 

Once you are finished, look into the bad interactions. Why are they bad? Which proteins are mitochondrial but do not interact? Are all protein mitochondrial? 



