# Day 2 

## downloading a file from ALICE to your PC

### Mac/Linux users

scp username@login.alice.universiteitleiden.nl:/path/to/file /path/to/folder/on/your/pc

### Windows users

You can do the same as the Mac users with the MobaXterm home terminal, or you can simply use the interactive file explorer on the left. If this is malfuctioning, restart your session. If still malfunctioning, you may have unintentionally disabled the option and should ask for help.

### back to all users

Now that you know how to transfer a file, please download the comparison pair model cif, starting pair model cif, and the corresponding single protein model cifs. 

If you have already installed PyMOL, you should be able to open these files by clicking on them. What do you see? Can you already tell if your structure makes sense? 

The basic PyMOL visualisation lacks information. Lets colour it by AlphaFolds pLDDT metrics. This data is stored inside the structure file, so PyMOL can find it and use it to colour your protein. The following code can be pasted directly into the PyMOL terminal.

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

# to make a photo, you can use two commands: ray or draw. The ray command uses ray tracing and makes a pretty image but is more computationally heavy. draw is more like a screenshot.

ray
png filename, dpi=300

#or

draw
png filename, dpi=300

```


