# HyTools: Hyperspectral Processing tools

HyTools is a python library for working with imaging spectroscopy data, with a focus on terrestrial scenes. 
At it's core it consists of a series of functions for reading and writing ENVI-formatted images in addition to 
opening NEON-formatted AOP HDF files. Built on top of these functions are a series of higher
level processing tools for data analysis which include spectral resampling, topographic
correction and BRDF correction. Other features are currently under development and include mask generation and
MNF transformation.

We have also created a series of command line tools which string together the processing functions and
provide a more streamlined workflow for processing images.

## Dependencies
- numpy
- h5py
- gdal

## Basic usage
```python
import hytools as ht

#Read an ENVI file
hyObj = ht.openENVI('envi_file.bin')
#Map image data to numpy memmap object
hyObj.load_data()

#Calculate NDVI, retrieves closest wavelength to input lambda in nm
ir = hyObj.get_wave(900)
red = hyObj.get_wave(660)
ndvi = (ir-red)/(ir+red)

#Other options for retrieving data
band = hyObj.get_band(10)
column = hyObj.get_column(1)
line = hyObj.get_line(234)
chunk = hyObj.get_chunk(x1,x2,y1,y2)

# Create a writer object to write to new file
writer = ht.file_io.writeENVI('output_envi.bin',hyObj.header_dict)

#Create an iterator object to cycle though image
iterator = hyObj.iterate(by = 'line')

# Cycle line by line, read from original data
while not iterator.complete:  
   #Read next line
   line = iterator.read_next() 

   #Do some calculations.......
   radiance = line * gain + offset

   #Write line to file
   writer.write_line(radiance,iterator.current_line)
	
writer.close()  
```

## Examples

### BRDF Correction
The BRDF correction module consists of a series of tools for removing brightness gradients caused
by variation in solar and viewing geometry. The module uses the combination of the Ross and Li
kernels to model the volumetric, geometric and isometric scattering surfaces and applies a multiplicative
correction to remove brightness gradients. 

![Before and after BRDF correction](examples/brdf_before_after.png) 

### Topographic Correction
The topographic correction module uses the Sun-Canopy-Sensor (SCS+C) method developed by [Soenen *et al.* 2005](https://ieeexplore.ieee.org/document/1499030) to remove shadows caused by variation in topography.

![Topographic correction](examples/topo_correct.gif) 


Goal:

Maintain python code for processing hyperspectral imagery that is readable/understandable and can be used by anyone with no guidance from the authors of the code. The core of this code is a set of modular functions that can be easily strung together into a flexible processing workflow (ie. a command line script).

Steps to achieving goal:

Follow widely accepted style guides for writing code (PEP8).

https://www.python.org/dev/peps/pep-0008/

http://docs.python-guide.org/en/latest/writing/style/

Use sphinx for code documentations.

At the beginning of each script/module include a comment block that clearly describes what the code does.

For methods drawn from the literature include full references in beginning comment block and line by line references where appropriate.

Code should be able to run on both local machines and servers seamlessly, ie: consider memory limitations.

Leverage existing python libraries for processing data (GDAL, sklearn…...). Limit use of obscure or abandoned packages.

Rules/Guidelines:

Submodule Structure

Topographic correction

SCSC
Classifiers

Cloud,shadow masks
Landcover
BRDF Correction

Scattering kernel generation
Multiplicative and additive correction
Class specific correction
Spectra processing

Vector normalization
Continuum removal
Wavelet
Spectrum Resampling

Gaussian response approximation
Weights optimization
Atmospheric correction

Atcor parameter processing
Export NEON radiance data to proper format
Ancillary tools

Geotiff exporter
ENVI parsing and reading
NDSI creator
ENVI exporter (for HDF)
Image sampler
MNF
Apply trait models
Point and polygon sampling
Other

Reconcile different file formats: ENVI vs. HDF

Short term: Code for both

Long term: Switch over to HDF?

Provides compression
Everything can be contained in a single file
Very easy to access data, no need for parsing header or
dealing with BSQ, BIP and BIL

Include inplace/copy and edit option

Include mini dataset to test code
