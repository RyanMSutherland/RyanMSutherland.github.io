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
    <div class="col-sm-6 mt-0 mt-md-0 align-self-center">
        {% include figure.liquid loading="eager" path="assets/img/ATLAS_cross_section.png" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    The above diagram shows a diagram of the 4 detectors that work together to make up ATLAS.
</div>

In the above diagram, we can begin to understand the four detectors present within the system. Beginning from the innermost section and moving outwards, we have the inner detector which will measure the trajectories of the various charged particles as they move through the detector. Following this, we have the electromagnetic calorimeter (ecal) which absorbs and measures the energies of electrons and photons as they pass through. Next, is the hadronic calorimeter (hcal) which serves a similar purpose to the ecal but will now absorb neutrons and protons as they pass. Finally, is the muon spectrometer which will measure the momenta of muons. For these outer three detectors, the data we receive from the system consists of the absorbed energy, eta and phi where eta and phi represent the location of the relative energy absorption. In order to filter this data, a global trigger system that operates at 100 kHz uses a trigger table in order to determine whether to store a collision event’s data, this is based on metrics such as the particle event’s resulting energy distribution on the different calorimeters, which indicates what type of collision took place.

As mentioned previously, the global trigger system uses a trigger table in order to decide which event data is worth storing. This sorting task can then be overlapped with the growing focus on using neural networks for pattern detection, naturally leading to the usage and integration of neural networks within the global trigger system, with the main goal being to sift through event data based on its significance, as determined by simulation. This simulation data will serve as the main source for training, validation, and testing data for these neural networks, which can then in turn be implemented within the trigger system, replacing hard coded trigger tables. Our focus then is to explore optimization of the data trigger process using neural networks on FPGA. This process involves understanding the pipeline from a Keras neural network to an FPGA implementation, as well as estimating performance of the resulting implementation.

The entire scope of the problem involves the usage of FPGAs or Field Programmable Gate Arrays, which leads to this brief summary of their use, advantages and the key metrics to explore when evaluating their performance. To begin, FPGAs are essentially an array of
programmable logic blocks and interconnects, which can be used to rapidly prototype customizable circuits, which in our case will be used to implement a neural network. This structure can be seen below, with the customizability allowing users to define custom logic in a hardware description language (VHDL). Some of the key components on an FPGA include memory such as BRAM, digital signal processors (DSPs) for complex operations, look up tables (LUTs) to define custom functions and flip flops (FFs) to store logical states synchronized with the clock cycle.

The main advantages of using FPGA in our context is the flexibility for rapid prototyping, with the aim being to achieve low latency and processing time, as well as minimize resource usage and energy consumption. For the ATLAS project, the key metrics then are naturally low latency, low processing time, minimal resource usage and the ability to achieve efficient implementations and are well suited for functionality on an FPGA.

To simplify the development process, researchers had given us a trained neural network that they wanted to live on the FPGA. However, this model was written in Python and needs to be converted to a language that the FPGA will understand. In order to do this, a tool called High Level Synthesis for Machine Learning (HLS4ML) which was developed by CERN was used in order to effectively translate Python to an optimized version of C++. This was done so that a seperate translator known as Vivado HLS can complete the conversion. HLS4ML is a powerful tool that allows for several optimization tools that help us optimize the limited resources available on the FPGA while increasing the speed in which the model can run. Some of these tools include pruning (pre HLS4ML), quantization and reuse facts. Let us take a closer look at how each of these tools operate

First, we will look at compression of the model through pruning. The primary idea behind this method is that each of the weights in our model has an absolute relative size that impacts their importance in the network. 

<div class="row">
    <div class="col-sm mt-0 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/pruning.jpg" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Operation of Pruning with L1 regularization.
</div>

In the above figure, we can see how this works. First, we will train the network classically with no alterations to the system. Following this, we will look at the weights of our model and retrain using L1 regularization which assigns a penalty to the system for large weights. Next, we will discard the lowest valued/least contributing weights, essentially trimming branches of the network. Finally, we will iteratively retrain and prune until there is no need to reduce the number of weights further. In performing this process, we can effectively decrease the size of our model by reducing the number of computations that need to be completed by the processor while maintaining a similar level of precision.
Once our model has been effectively pruned, we can now convert from Python to C++ using HLS4ML. One of the parameters that we can define during this conversion is known as quantization. In Python, when a model is trained, a 64-bit float is assigned to each number whether this be a weight, input or otherwise. However, by reducing the size of these values, we can reduce the time it takes to perform mathematical operations on the FPGA. By changing the number of bits available per value from 64 to 8 or 16 bits, we can decrease the latency of our system while maintaining accuracy. The above L1 regularization contributes to this as all of the relative weights are similar and reducing the precision of the system therefore should not have a considerable impact on the result. 

<div class="row">
    <div class="col-sm mt-0 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/quantization.jpg" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Impact of quantization on precision and resources.
</div>

As we can see, from the various inputs of the system, there does not appear to be a major loss in data as the number of bits available to the system decreases. By taking advantage of this tool, we can drastically decrease the computational resources required of the system while decreasing latency.

Finally, we can now look at a factor that is unique to conversions with FPGAs. This is known as a reuse factor. On an FPGA, there are only a maximum number of arithmetic computation units known as DSP’s or Digital Signal Processors. Ideally, in any given neural network, every DSP will be used simultaneously so the system may operate as quickly as possible. However, due to the size of neural networks alongside the fact that these DSP’s will be used by multiple operations (other teams using the same FPGA), we need to create a sort of buffer system that will slow the operation of the system but remain within operation requirements. Typically, we have a reuse factor of 1 (situation described above), however by increasing this factor to say 2, we now will use half the number of DSP’s while doubling the latency of the system. 

<div class="row">
    <div class="col-sm mt-0 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/reuse.png" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Impact of reuse factor on DSP utilization
</div>

As we can see, the higher we increase this reuse factor, the less DSP utilization that is required of the system. Something to note is using this factor is essentially an optimization problem as increasing the reuse factor also increases the latency of the system.

After using hls4ml to convert the Keras neural network into a C++ form, as well as optimizing the model for high level synthesis, we are left with the last step of the pipeline, namely using Vivado HLS to convert from C++ with given constraints such as the precision, reuse factor and other parameters as specified in the hls4ml conversion, into RTL code for implementation on FPGA such as VHDL or Verilog.

In our pipeline, Vivado was provided by the sponsor in the designated FPGA container, which we used to build the C++ code using the build command with csim set to False and the rest of the parameters set to default. An interesting option that is available through Vivado HLS is the ability to generate simulation reports on the expected timing and resource usage of the VHDL code on the target FPGA, which is useful for understanding how different models perform.

To verify our pipeline, the HLS conversion was tested in the form of a timing and latency report. This report was generated from the Vivado build and shows timing and latency estimates for the GNN that was simulated. The Vivado HLS report indicates that the target clock frequency (clock period) was set to 5 nanoseconds (ns). After synthesis, the estimated clock period was 4.221 ns, which represents the actual clock period that the design is expected to achieve based on the logic synthesis process. Uncertainty represents the variation or margin of error associated with the estimated clock period. It indicates the range within which the actual clock period might fall. In this case, the uncertainty is reported as 0.62 ns, meaning that the actual clock period might vary by up to 0.62 ns from the estimated value. The minimum and maximum latencies were found to be 61 and 23675 cycles respectively. This can also be represented in time with 0.305 us and 0.118 ms. Note that no specific pipeline type was specific for this simulation but populating this could lead to better latency results. A table containing these results can be seen in Figure 13.

<div class="row">
    <div class="col-sm-4 mt-0 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/performance.png" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
    <div class="col-sm-8 mt-0 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/allocation.png" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Timing, Latency and Utilization report for the HLS conversion of a provided model
</div>

Currently this pipeline is able to take a python keras model, convert it into C++ code using HLS4ML and then compile this code into VHDL using Vivado where timing, latency, and resource usage reports can be generated. This pipeline used a GNN (Graphical Neural Network) as input to represent the data. Future considerations regarding optimization and development of this pipeline could be the following. A better understanding of the C++ to VHDL conversion could be beneficial to help fix potential errors that arise for different models. Understanding the methods of optimizing network layers to minimize latency could be another area of exploration. Additionally, flashing the VHDL code onto the physical FPGA would be a natural next step to confirm the validity of the Vivado simulation report results.