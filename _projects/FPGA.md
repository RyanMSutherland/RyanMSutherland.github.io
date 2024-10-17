---
layout: page
title: FPGA for LHC Applications
description: Working to filter particle collision data in real time
img: assets/img/ATLAS.jpg
importance: 1
category: Capstone
related_publications: true
---

The Large Hadron Collider (LHC) located in Geneva, Switzerland is home to one of the largest physics projects in the world. Here, scientists accelerates particles such as protons near the speed of light then allow them to collide with one another. This is done in order to observe various decay chains and is the reason the Higgs Boson was discovered. 

In order to make these measurements, there are two primary detectors that live on the beamline. For the purposes of this project, we focused on the ATLAS detector as this was the division located at the University of British Columbia. This equipment however comes with one major problem which is data. In order to observe as many interactions as possible, packets of protons containing ~3 million protons will interact with each other every 25 ns. Given current sampling methods, this has led to a collection of 50 TB of data every second which is an extreme amount of data both to store and analyze in any reasonable amount of time. Furthermore, many of the interactions are now considered "common" and no longer require further analysis. This is where our team came into play, to develop a system that could analyze these interactions on the order of microseconds and predict if they were considered "interesting" or not.

To begin, let us first understand how the ATLAS detector functions.

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/ATLAS_cross_section.png" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    The above diagram shows a diagram of the 4 detectors that work together to make up ATLAS.
</div>

In the above diagram, we can begin to understand the four detectors present within the system. Beginning from the innermost section and moving outwards, we have the inner detector which will measure the trajectories of the various charged particles as they move through the detector. Following this, we have the electromagnetic calorimeter (ecal) which absorbs and measures the energies of electrons and photons as they pass through. Next, is the hadronic calorimeter (hcal) which serves a similar purpose to the ecal but will now absorb neutrons and protons as they pass. Finally, is the muon spectrometer which will measure the momenta of muons. For these outer three detectors, the data we receive from the system consists of the absorbed energy, eta and phi where eta and phi represent the location of the relative energy absorption.

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/1.jpg" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/3.jpg" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/5.jpg" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Caption photos easily. On the left, a road goes through a tunnel. Middle, leaves artistically fall in a hipster photoshoot. Right, in another hipster photoshoot, a lumberjack grasps a handful of pine needles.
</div>

You can also put regular text between your rows of images, even citations {% cite einstein1950meaning %}.
Say you wanted to write a bit about your project before you posted the rest of the images.
You describe how you toiled, sweated, _bled_ for your project, and then... you reveal its glory in the next row of images.

<div class="row justify-content-sm-center">
    <div class="col-sm-8 mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/6.jpg" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
    <div class="col-sm-4 mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/11.jpg" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    You can also have artistically styled 2/3 + 1/3 images, like these.
</div>

The code is simple.
Just wrap your images with `<div class="col-sm">` and place them inside `<div class="row">` (read more about the <a href="https://getbootstrap.com/docs/4.4/layout/grid/">Bootstrap Grid</a> system).
To make images responsive, add `img-fluid` class to each; for rounded corners and shadows use `rounded` and `z-depth-1` classes.
Here's the code for the last row of images above:

{% raw %}

```html
<div class="row justify-content-sm-center">
  <div class="col-sm-8 mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/6.jpg" title="example image" class="img-fluid rounded z-depth-1" %}
  </div>
  <div class="col-sm-4 mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/11.jpg" title="example image" class="img-fluid rounded z-depth-1" %}
  </div>
</div>
```

{% endraw %}
