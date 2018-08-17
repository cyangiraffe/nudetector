# NuDetect

NuDetect uses an object-oriented framework to organize analyses of different detector experiments. For example, the class ```GammaFlood``` contains methods and takes initialization parameters specific to the analysis and plotting of gamma flood data. Other such classes include ```Noise``` (for noise data) and ```Leakage``` (for leakage current data, WIP). 

## Getting Started

### Installing

Currently, the package can be crudely installed by putting the 'nudetect.py' file into the directory in which you want to run data analysis scripts and then imported like any other module.

### Required Packages

* Numpy
* Astropy
* Scipy
* Matplotlib

**Note:** for matplotlib to work on the server lif.srl.caltech.edu (as of 8 August 2018), you must switch the matplotlib backend to 'agg'. To do this permanently in a miniconda distribution, copy the matplotlibrc sample file at miniconda3/lib/python3.6/site-packages/matplotlib/mpl-data/matplotlibrc to the .config/matplotlib directory, open the file (.config/matplotlib/matplotlibrc) and set the value of 'backend' to 'agg'.

### Example Script
```python
import numpy as np
import matplotlib.pyplot as plt
from nudetect import GammaFlood

# Here, we initialize a 'GammaFlood' object. This stores information about the 
# experiment, and will be populated with analyzed data as we call its methods
# for processing the raw gamma flood data.
gamma = GammaFlood('20170315_H100_gamma_Am241_-10C.0V.fits', # raw data
					data_dir='data/{}', # default save directory for data
					plot_dir='plots/{}', # default save directory for plots
					detector='H100', # detector ID
					source='Am241', # Used to fit peaks and get gain data
					voltage=0, # in volts
					temp=-10) # in degrees celsius

# Note: The '{}' in 'save_dir' is automatically formatted to the detector ID.
# In this case, data_dir == 'data/H100'

# At this point, we have some processed data attributes initialized, but not
# contianing any data:
#	gamma.count_map: None
#   gamma.gain:      None
#	gamma.spectrum:  None

#
# Processing data
#

# Populates the attribute 'count_map', a 32 x 32 array with count data for 
# each pixel. The method will store this output in an ascii file in the 
# directory 'outputs/H100'.
gamma.gen_count_map()

# 'gain' is a 32 x 32 array with gain data for each pixel. Here, a spectrum 
# plot is saved for each individual pixel in the directory 
# 'outputs/H100/pixels', and the gain data saved to 'outputs/H100'.
gamma.gen_quick_gain(plot_subdir='pixels')

# 'spectrum' is a 2 x 10000 array representing the whole-detector spectrum.
# This output is saved to 'outputs/H100'.
gamma.gen_spectrum()


# Now, our processed data attributes have been populated with data:
#	gamma.count_map.shape: (32, 32)
#   gamma.gain.shape:      (32, 32)
#	gamma.spectrum.shape:  (2, 10000)

#
# Plotting
#

# The plotting methods called below draw data from the processed 
# data attributes populated above.

# Plots and fits 'spectrum'.
gamma.plot_spectrum()

# Plots a histogram that bins pixels by their event counts.
gamma.plot_pixel_hist('Count')

# Plots heatmaps of counts and gain for each pixel. Here, we specify what type
# of data we want to plot, and the method figures out the rest.
gamma.plot_pixel_map('Count')
gamma.plot_pixel_map('Gain')
```

## Developer Notes
* All classes with data analysis/plotting methods inherit from the ```Experiment``` base class, which is not useful to instantiate on its own, but contains methods to be shared with its children.
* There is one additional class called ```Line``` that can store information about a spectral line and the source that emits it.
* The ```construct_path``` method of the ```Experiment``` class is designed to throw a lot of exceptions and be strict about formatting early on to avoid complications later. Call it early in scripts to avoid losing the results of a long computation to a mistyped directory.

## Authors

* **Hiromasa Miyasaka** - *Wrote original IDL scripts*
* **Sean Pike** - *Wrote scripts in Python* - [snpike](https://github.com/snpike/)
* **Julian Sanders** - *Wrote documentation, helper functions, and classes. Edited existing code for style and efficiency.* - [cyangiraffe](https://github.com/colcaboose)
* **Andrew Sosanya** - *Documentation and logistics* - [DrewSosa](https://github.com/DrewSosa)
