---
jupytext:
  cell_metadata_filter: -all
  formats: md:myst,ipynb
  text_representation:
    extension: .md
    format_name: myst
    format_version: 0.13
    jupytext_version: 1.19.3
kernelspec:
  name: python3
  display_name: Python 3 (ipykernel)
  language: python
---

# CosmoSim Roulette Resimulation Demo 

This Demo assumes that you are familiar with the principles from
[](./Demo01).  We start with the same imports

```{code-cell} ipython3
import pandas as pd
import matplotlib.pyplot as plt
import numpy as np
from PIL import Image
import toml
import tomllib as tl

from CosmoSim import CosmoSim
from CosmoSim.datagen import SimImage
import CosmoSim.Image as csimg
from CosmoSim import Parameters
```

Additionally, we require the `Resim` class for resimulation.

```{code-cell} ipython3
from CosmoSim.roulettegen import Resim
```

## Basic Configuration

We start with parameters similar to the ones from [](./Demo01),
to make a baseline raytrace simulation using a SIE lens.
We have just rounded some of the parameters to get neater numbers.

```{code-cell} ipython3
with open( "Demo01.toml", 'rb') as f:
            toml = tl.load(f)

param = Parameters( toml )
imsim = SimImage( param, verbose=2 )
im = imsim.getImage()
plt.imshow( im, cmap='gray')
plt.title( "Baseline simulation" )
plt.axis("off")
```

## Roulette parameters

```{code-cell} ipython3
row = imsim.getData()
display( row )
```

```{code-cell} ipython3
rsim = Resim( row )
im = rsim.getImage()
plt.imshow( im, cmap='gray')
plt.title( "Resimulation" )
plt.axis("off")
```

## Roulette amplitudes from sampled lenses

## Resimulation

## Comparing simulation models

## A convenience function

The following function does the four simulations and plots above,
for any given set of parameters `param`.
When we fiddle with the source (or lens) description, we will use
this to run all tests at once.

```{code-cell} ipython3
def quadSim( param ):
    param["simulator"]["model"] = "Roulette"
    param["simulator"]["sampled"] = True
    imsimRouletteSampled = SimImage( param, verbose=0 )
    imRouletteSampled = imsimRouletteSampled.getImage()

    param["simulator"]["sampled"] = False
    imsimRoulette = SimImage( param, verbose=0 )
    imRoulette = imsimRoulette.getImage()

    param["simulator"]["model"] = "Raytrace"
    param["simulator"]["sampled"] = True
    imsimRaytrace = SimImage( param, verbose=0 )
    imRaytrace = imsimRaytrace.getImage()

    param["simulator"]["sampled"] = False
    imsimRaytraceSampled = SimImage( param, verbose=0 )
    imRaytraceSampled = imsimRaytraceSampled.getImage()

    fig = plt.figure(figsize=(10, 10))
    fig.tight_layout(pad=0.0)
    plt.subplots_adjust(hspace=0.1, wspace=0.1) 

    fig.add_subplot(2, 2, 1)
    plt.imshow( imRouletteSampled, cmap='gray')
    plt.title( "Sampled Roulette Simulation" )
    plt.axis("off")

    fig.add_subplot(2, 2, 2)
    plt.imshow( imRaytraceSampled, cmap='gray')
    plt.title( "Sampled Raytrace Simulation" )

    plt.axis("off")

    fig.add_subplot(2, 2, 3)
    plt.imshow( imRoulette, cmap='gray')
    plt.title( "Roulette Simulation" )
    plt.axis("off")
    
    fig.add_subplot(2, 2, 4)
    plt.imshow( imRaytrace, cmap='gray')
    plt.title( "Raytrace Simulation" )
    plt.axis("off")

    return( imRaytrace, imRoulette, imRaytraceSampled, imRouletteSampled )
```

