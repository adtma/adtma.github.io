---
title: 'PHREEQC, Python, and Pyrite Oxydation'
date: 2026-02-04
categories:
  - blog
tags:
  - phreeqc
  - python
  - tutorial
---

A conversation I had a couple of weeks ago led me to explore a geochemical modeling tool called [PHREEQC](www.usgs.gov/software/phreeqc-version-3). I remember in one of the classes I took in grad school, we had a session on this (it was intro to geochemistry, probably). I also remember that I did a little bit of digging and exploring the possibility of incorporating PHREEQC into my carbon cycle model for one of my side projects during my PhD. My idea was to use PHREEQC to do carbonate chemistry calculations to quantify carbonate saturation states in each modeling time step. I don't remember the exact reasons why I abandoned the idea, but likely they were a combination of (1) the software has too many knobs, which means for a deep-time geologist like me, more parameters that are difficult to constrain, and (2) I found a simpler alternative (I translated Richard Zeebe’s cysis C code into python script).

Anyway, recently I learned that there is a Python package that can be used to interact with PHREEQC. This discovery got me excited a little bit, partly because I was not very keen on installing the full GUI software on my laptop due to a steep learning curve. Because of my familiarity with Python, I feel like I can overcome the issue relatively easily. 

## Installation
The package to interface Python with PHREEQC is called [PhreeqPy](https://www.phreeqpy.com/). The installation is pretty simple. The recommended method is installing the package using the pip package manager by typing this in your terminal

```bash
pip install phreeqpy
```

Since I am a conda/mamba user, I was pretty hesitant with this approach. I checked to see if there was a conda version available in the conda-forge channel. Unfortunately, I found that the version available on the conda-forge channel is not the most up-to-date version (which is kind of strange, because conda-forge packages are usually up-to-date). Because of this versioning issue, I went ahead and used pip to install phreeqpy on my laptop. I also made a virtual environment using venv to isolate the package from the rest of my “working” Python environment.

The installation is pretty straightforward. I ran some example scripts that the developer provides on the Phreeqpy website, and all seemed to be working fine. However, when I tried to change the database from the default phreeqc.dat to pitzer.dat, it threw an error message that said “cannot load database”. 

It took me a while to troubleshoot the issue. I even used an LLM to help me, but it was not able to resolve the issue. I tried reinstalling and installed different version, but it did not work at all.

The solution that ended up working was linking the Python package to an Iphreeqc library (you can think of it as a lightweight version of phreeqpy). Here are the steps to install the Iphreqc library on Mac (or Linux, Windows is probably a little different).

```bash
# make a folder to store the library on my home directory
mkdir ~/iphreeqc

# make a folder to store source codes
mkdir ~/src
cd src

# download the source code from the USGS website
wget https://water.usgs.gov/water-resources/software/PHREEQC/iphreeqc-3.8.6-17100.tar.g

# extract the zip file
tar xvf iphreeqc-3.8.6-17100.tar.gz
cd iphreeqc-3.8.6-17100

# configure to store binary files into the iphreeqc folder
./configure --prefix=$HOME/iphreeqc

# Check and compile
make
make check
make install
```

Once I installed the Iphreeqc library, I was able to make the script work. The next step was to do one of the exercises that was provided in the PHREEQC manual.

## Pyrite Oxidation
The exercise I choose to do is reproducing example 5 from the PHREEQC Manual. This example focuses on simulating pyrite oxidation (which is kinda of related to my current research project). Think of it like a simple chemistry experiment where we have a beaker filled with water and a mix of minerals like pyrite, calcite, and goethite. The main goal of this exercise is to see what happens when we gradually add oxygen and a bit of salt to the mix. As the oxygen hits the pyrite, a whole chain reaction starts where minerals dissolve and others form based on the shifting chemical balance. 

First, I need to import the libraries I need to run this exercise

```python
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt

import phreeqpy.iphreeqc.phreeqc_dll as phreeqc_mod
```

I then need define the path to the recently installed Iphreeqc library, the database that we want to use and initialize the phreeqc engine.

```python
# library
lib = "/Users/datu/iphreeqc/lib/libiphreeqc.dylib"
# Database
dbase = "/Users/datu/iphreeqc/share/doc/iphreeqc/database/phreeqc.dat"
phreeqc = phreeqc_mod.IPhreeqc(lib)
phreeqc.load_database(dbase)
```

The next step is setting up the input file. This is taken directly from the PHREEQC manual. The input file for this pyrite oxidation test is basically a set of instructions that tells the program how to set up and run a chemical experiment in a digital beaker.

It starts with a TITLE keyword to keep things organized and then uses the SOLUTION block to define our starting water, which in this case is just pure water. The EQUILIBRIUM_PHASES section lists the minerals like pyrite and calcite that we want the water to react with, while the REACTION block specifies the exact amounts of oxygen and salt we are adding to the mix in steps. To make sense of the results, the SELECTED_OUTPUT block tells the program which specific data points to save to a file.


```python
input_file = """
TITLE Example 5.--Add oxygen, equilibrate with pyrite, calcite, and goethite.
SOLUTION 1 PURE WATER
pH 7.0
temp 25.0

EQUILIBRIUM_PHASES 1
Pyrite 0.0
Goethite 0.0
Calcite 0.0
CO2(g) -3.5
Gypsum 0.0 0.0

REACTION 1
O2 1.0
NaCl 0.5
0.0 0.001 0.005 0.01 0.03 0.05

SELECTED_OUTPUT
-total Cl
-si Gypsum
-equilibrium_phases pyrite goethite calcite CO2(g) gypsum
"""
```

The next step is running the phreeqc engine, extract the results, store them as pandas dataframe and trim. I prefer to store the output as pandas dataframe because it makes plotting and visualization easier.

```python
# Run phreeqc
phreeqc.run_string(input_file)

# Load as pandas dataframe
df = pd.DataFrame(phreeqc.get_selected_output_array())

# Set column heading to the first row
df.columns = df.iloc[0]

# Drop the first row 
df = df.drop(index=0, axis=0)
```

The next step is visualization. Since, I want to reproduce Figure 6 from the PHREEQC manual I need do a little bit of pre-processing.

```python
# Filter the dataframe to only include reaction steps
# Ignore the 'i_soln' state because the graph starts at the first reaction equilibrium
df_react = df[df['state'] == 'react'].copy()

# Define the X-axis: O2 added in millimoles
# The reaction steps were: 0.0, 0.001, 0.005, 0.01, 0.03, 0.05 moles
o2_moles = np.array([0.0, 0.001, 0.005, 0.01, 0.03, 0.05])
df_react['o2_added_mmol'] = o2_moles * 1000

# Calculate millimoles dissolved/precipitated
# For Goethite and Gypsum, the value will be negative because they precipitate
df_react['pyrite_mmol'] = df_react['d_pyrite'] * -1000
df_react['goethite_mmol'] = df_react['d_goethite'] * -1000
df_react['calcite_mmol'] = df_react['d_calcite'] * -1000
df_react['co2_mmol'] = df_react['d_CO2(g)'] * -1000
df_react['gypsum_mmol'] = df_react['d_gypsum'] * -1000 
```

And, finally this is the script to visualize the data.

```python
# Plotting
fig, ax1 = plt.subplots(figsize=(7, 3.5))

# Primary Y-Axis: Millimoles dissolved
ax1.plot(df_react['o2_added_mmol'], df_react['calcite_mmol'], 'b--', label='Calcite')
ax1.plot(df_react['o2_added_mmol'], df_react['pyrite_mmol'], 'k--', label='Pyrite')
ax1.plot(df_react['o2_added_mmol'], df_react['gypsum_mmol'], 'r--', label='Gypsum')
ax1.plot(df_react['o2_added_mmol'], df_react['goethite_mmol'], 'g--', label='Goethite')

ax1.plot(df_react['o2_added_mmol'], df_react['co2_mmol'], 'y--', label='CO2(g)')


ax1.set_xlabel('O2 added (millimoles)', fontsize=14)
ax1.set_ylabel('Millimoles dissolved', fontsize=14)
# ax1.set_title('Pyrite Oxidation (PHREEQC Example 5 / Figure 6)')
ax1.grid(True, linestyle=':')

# Secondary Y-Axis: Gypsum Saturation Index
ax2 = ax1.twinx()
ax2.plot(df_react['o2_added_mmol'], df_react['si_Gypsum'], 'r-o', 
         label='Saturation Index\nGypsum', linewidth=2, markersize=8, markerfacecolor='none')
ax2.set_ylabel('Saturation index', fontsize=14)
ax2.set_ylim(-7, 1) # Scaling to match manual [1]
ax2.axhline(0, color='gray', lw=0.5)

fig.legend(loc='upper left', bbox_to_anchor=(1.001, 1))

fig.tight_layout()
plt.savefig('phreeqc_pyrite.png', dpi=300)
```

Here is what it looks like.
![image-center](/assets/images/blog/2026-02-05-phreeqc/phreeqc_pyrite.png){: .align-center}

The resulting plot depicts the reaction path of pyrite, calcite, and goethite within a beaker as oxygen is added in discrete steps. The primary y-axis measures the millimoles of each mineral transferred, revealing that pyrite and calcite dissolve while goethite precipitates as a result of the oxidation process. A secondary y-axis tracks the saturation index of gypsum, demonstrating that gypsum only begins to form scale once the added oxygen exceeds 30 millimoles and the mineral reaches equilibrium. Additionally, the graph captures the loss of carbon dioxide gas from the system as it acidifies, showing the solution's attempt to maintain equilibrium with atmospheric partial pressure while sulfuric acid is generated.


That is it for now. There are other scenarios and simulations that I would like to try, and at some points I may share them as a blog post. I hope this is interesting and useful and let me know if you have any questions.

