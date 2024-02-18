---
title: 'Geology Time Scale in Matplotlib (Extended)'
date: 2024-02-17
categories:
  - Blog
tags:
  - geology
  - python
toc: true
---
In my previoud post, I shared my "go-to" python script to add a geologic time scale (gts) on my matplotlib plot. In this post I am going to give a more detailed explanation of the scripts, so that people who read this and interested in adding gts on their plot, they can do it more easily.

# Import and setup
## Import
The first few lines of the code, import the required python packages/libraries to add gts.

{% highlight python %}
import numpy as np
import matplotlib.pyplot as plt
import pandas as pd
{% endhighlight %}

The three main libraries you need to import are: `numpy, matplotlib, and pandas`. Numpy's main job is to do array and matrix operatios, whereas pandas' job is to store the information such as ages, color, etc. Matplotlib is our main workhorse (i.e., creating visualization and plots).

If you are nerdier than me, and know more about python, you may question my choice in using pandas. I do know that I can wrap the data that I need to make the plot using python dictionary, but if it ain't broken, i don't need to fix it.

In addition to the pyplot module of matplotlib, we also need to import the `Rectangle` patches module. As you may have guessed, its main job is drawing rectangle in matplotlib.

The next line shown in the snippet below sets the thickness of your lines in the plot.

{% highlight python %}
from matplotlib.patches import Rectangle
plt.rcParams['axes.linewidth'] = 1.5
{% endhighlight %}

## Create dummy data
These lines create dummy data to plot. I define a time variable that store an array of 200 numbers from 500 to 10, and introduce some randomness to make the data look more realistic using the `np.random.normal`. The next line is a simple `sin` operation using `numpy` to create sinusoid trends, and then add the error to the `sin`.

{% highlight python %}
time = np.linspace(500, 10, 200)
error = np.random.normal(0, 0.75, 200)
sin = np.sin(np.linspace(-3*np.pi, 3*np.pi, 200))*2
data = sin + error
{% endhighlight %}

## Define units, boundary, and colors
This is the meat of the script. I define the chronostratigraphic unit of the geologic time scale. For this exercise I plot two units, *Era* and *Period*. I store both units as `epoch` in the pandas dataframe that will be created to store these variable. The rest of the info that we need to enter are the lower and upper boundary of the units, and the color that we want to use. As a stratigrapher I am big believer of using the official color code for each chronostratigraphic unit.

{% highlight python %}
gts = pd.DataFrame({
    'epoch' : ['Paleozoic', 'Mesozoic', 'Cenozoic'],
    'lower_boundary' : [510, 251.9, 66],
    'upper_boundary' : [251.9, 66, 0],
    'color' : ['#99C08D', '#67C5CA', '#F2F91D']
})

period = pd.DataFrame({
    'epoch' : ['Cam', 'Ord', 'Sil', 'Dev', 'Car', 'Per',
               'Tri', 'Jur', 'Cre', 'Pal', 'Neo', ''],
    'lower_boundary' : [510, 486.9, 443.1, 419, 359.3, 298.9, 251.9,
                        201.4, 143.1, 66, 23.04, 2.58],
    'upper_boundary' : [486.9, 443.1, 419, 359.3, 298.9, 251.9, 201.4,
                        143.1, 66, 23.04, 2.58, 0],
    'color' : ['#7FA056', '#009270', '#B3E1B6', '#CB8C37', '#67A599',
              '#F04028', '#812B92', '#34B2C9', '#7FC64E', '#FD9A52',
              '#FFE619', '#F9F97F']
})
{% endhighlight %}

# Matplotlib actions
If you have used matplotlib for a while, you probably know that there are two ways we create plots in matplotlib. The easy way (i.e., procedural programming), and OOP (object oriented). For this task, I use the OOP method just because it allows me to customize more things. If you are not familiar with the difference between OOP and procedural, you can tell by the code if you plot your data using `plt.plot(x,y)` that means you are using the non-OOP way.

## Create figure, grid spec, and axes
Since we are using the OOP way, we first need to initiate the canvas or the `figure` of our plot using the following line.
{% highlight python %}
fig = plt.figure(figsize=(8.5,5))
{% endhighlight %}

The next step is defining the ratios of width and height of the axes that we will define later. We need at least two axes everytime we do this. One axis for the GTS, the other one for the data. In this example we store the ratios as lists of number. We have two numbers in the `h` variable because we want to draw this plot as as a 1 column, 2 row setup (i.e., landscape mode). If you want to do portrait mode, you need to have two numbers in the widht list.
{% highlight python %}
w = [1]
h = [1, 0.1]
{% endhighlight %}

The next step is creating gridspec. Gridspec is a suite of classes and methods to customize the size of our axes in matplotlib. Commonly we would want to have our Chronostratigraphic units in a narrower axis compared to the data. There are many ways to use gridspec, but this one is my favorite because you do not need to import a separate `Gridspec` module.
{% highlight python %}
gs = fig.add_gridspec(ncols=1, nrows=2, width_ratios=w, height_ratios=h)
{% endhighlight %}

Now, let's define our axes. We need at least two one for the GTS, one for the data. In this example `ax0` is the axis for the GTS. One important step before we move on to the next one is defining the limit of the axis. Since we will create a GTS in a landscape form, we need to define the  `xlim` of the axis. Notice that I use `sharex` to initialize the data axis. The main purpose of `sharex` is to make sure that all x axis in both axes are the same size.
{% highlight python %}
ax0 = fig.add_subplot(gs[1,0])
ax0.set_xlim(0, 510)
ax = fig.add_subplot(gs[0,0], sharex=ax0)
{% endhighlight %}

# Plotting
This is the main portion of the script and does the heavy lifting.
## Transform axis and height
We first need to define the axis transform, of `ax0` so that we can define the placement more accurate. You can read more about axis transform in matplotlib [here](https://matplotlib.org/stable/users/explain/artists/transforms_tutorial.html). The next thing we need to do is calculate the height of the unit by substracting the base for the top of each unit. This is the part where using `pandas` to store the variables makes our life so much easier. When you plot the time scale in a portrait format you will also need to define the rotation, of your text here, but you can also do it in the main loop as well. Since we are making a landscape plot we do not need to define the rotation of the text at all.
{% highlight python %}
trans = ax0.get_xaxis_transform()
gts['h'] = gts['lower_boundary'] - gts['upper_boundary']
period['h'] = period['lower_boundary'] - period['upper_boundary']
{% endhighlight %}

## The main loop
This is the main loop that plot a bunch of rectangle to create our geologic time scale. The code is relatively more straightforward and somewhat self-explanatory.
{% highlight python %}
for ind in gts.index:
    ax0.add_patch(Rectangle(xy=[gts['upper_boundary'][ind], 0],
                            height=0.5,
                            width=gts['h'][ind],
                            transform=trans, ec='black',
                            fc=gts['color'][ind]))
    
    ax0.text(gts['lower_boundary'][ind]-0.5*gts['h'][ind], 0.25, 
                str(gts['epoch'][ind]),
                horizontalalignment='center', verticalalignment='center',
                transform = trans, fontsize=12)

for ind in period.index:
    ax0.add_patch(Rectangle(xy=[period['upper_boundary'][ind], 0.5],
                            height=0.5,
                            width=period['h'][ind],
                            transform=trans, ec='black',
                            fc=period['color'][ind]))
    
    ax0.text(period['lower_boundary'][ind]-0.5*period['h'][ind], 0.75, 
                str(period['epoch'][ind]),
                horizontalalignment='center', verticalalignment='center',
                transform = trans, fontsize=10)
{% endhighlight %}

## Plot the data and show
The last portion of the code is pretty standard. You just need to plot the data and labels your axes.
{% highlight python %}
x.scatter(time, data, ec='k', fc='orange', s=100,
           label='Dummy data')
		   
		   
ax.set_ylabel('$\delta^{614}XOXO_{carb}$', fontsize=12)
ax.legend()
ax.tick_params(axis='x', bottom=False, labelbottom=False)
ax0.tick_params(axis='y', labelleft=False, left=False)
ax0.set_xlabel('Age (Ma)', fontsize=12)

plt.tight_layout()
plt.show()
{% endhighlight %}

# The result
Here is the plot that you should see if you get everything correct. You can get the full code [here](https://www.adtma.pw/blog/geotimescale-python/).

{% gist b34c6841ea05ab19538041619de04166%}

Let me know if you have any questions. Cheers!