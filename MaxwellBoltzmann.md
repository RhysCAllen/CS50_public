# Maxwell-Boltzmann Animation
## Interactive simulation of Maxwell-Boltzmann distribution of kinetic energies of gases

https://github.com/user-attachments/assets/0efe9a2c-95a2-43b6-8339-1d010c4d7bd8

### REQUIREMENTS:

Ubuntu 22.04.4 LTS (Jammy Jellyfish) \
Python==3.12.2 \
clang==14.0.0-1ubuntu1.1 \
libatlas-base-dev==3.10.3-12ubuntu1 \
matplotlib==3.8.3 \
scipy==1.13.1 \
ffmpeg==7:4.4.2-0ubuntu0.22.04.1

### HOW TO RUN:
With a free GitHub.com account, the repo can be cloned and run within a codespace.

From the codespace terminal, navigate to the `/project/backend/` folder and compile the files: 

`$ clang -o particles particles.c -lcblas`

Then, create a shared library file:

`$ clang -fPIC -shared -o particles.so particles.c -lcblas`

Finally, navigate to the /project folder and run the app: \
`$ python project.py`

To view the resulting simulation, open the animation file: \
`$ code movie.mp4`

### CONTACT ME:
31254709+RhysCAllen@users.noreply.github.com

### Project Summary
This repository will be submitted towards fulfllment of the requirements for CS50P: Introduction to Programming with Python (Harvard University via edX), 2024. This is the final project for the class.

August 2024: Please note this is a work in progress. The front end (visualization and data transfer between C and Python) is completed with random data. The backend (particle dynamics and KD tree for space partitioning of animation frames) is under construction.

My long-term goal is to be able to write programs that showcase scientific analysis, especially related to microbiology. This project allows me to explore the <a href="https://fortran-lang.discourse.group/t/ten-computer-codes-that-transformed-science/595"> famous</a> CBLAS library: basic linear algebra subroutines for C. I also wanted to decide whether JavaScript-based animation (previous project) or desktop-based animation in Python (this project) worked better for me as a programmer. This project is helping me reach my long-term goal to understand and share scientific discoveries via computing. 

### Skills previously used during CS50:
    -Writing and binding dynamically linked C libraries to Python
### Skills new for this project:
    -Model parameters of Maxwell-Boltzmann probability distribution for elastic collisions
    -Using CBLAS, a basic linear algebra library for C, to calculate particle dynamics
    -Write and navigate a K-D tree in C for space-partitioning of particles in each animation frame
    -Using matplotlib.animation in Python

### Features
    Basic implementation:
        -Non-interactive particles bouncing around a square 2D frame, with display of actual vs theoretical particle energies
        -User input of parameters such as temperature and what type of particles (e.g. oxygen, neon, etc.)

    Stretch goals:
        -An interactive simulation that adjusts to user inputs of activation energy and temperature on reaction rates and Keq
        -color to better illustrate conservation of energy during collisions

### Description: project background and goals

The original inspiration for this project came from the Wikipedia article about the <a href="https://en.wikipedia.org/wiki/Maxwell%E2%80%93Boltzmann_distribution">Maxwell-Boltzmann distribution</a>. As a chemistry tutor, I grew to appreciate how effectively this distribution helps students understand the relationship between temperature, chemical rates, and chemical equilibria. Seeing the animation on the Wikipedia page made me want to recreate it as a tribute to one of my favorite topics in chemistry, and to grow my programming skills for creating interactive science simulations.

Thermometers are essentially speedometers for molecules. When we place a thermometer in a sample, we are getting an estimate of the average speed at which its molecules are colliding.  However, temperature is a macroscopic property: on a molecular level, particles have a range of speeds, some faster and some slower. The average speeds determine the given temperature. The probability of what speeds are present for a single temperature is predicted by the Maxwell-Boltzmann distribution.

This simple and elegant model, with only one parameter (the degrees of freedom), allows students to see why some chemical reactions, like spoiling milk, happen faster at warmer temperatures, because they have a larger fraction of particles with sufficient energy to react.  With a simple vertical line, we can see the effect of a catalyst on reaction rates, and why some reactions go to completion whereas others remain a dynamic equilibrium of reactants and products.  This vertical line represents the activation energy necessary for reactants to form products, and is a perfect visual representation of the Arrhenius equation:

ln k = ln(constant*f)
ln k = ln A + ln f; where f is the fraction of molecules with sufficient kinetic energy to form products.
Since ln f = -Ea/RT, we have

ln k = lnA - Ea/RT, or

k = A*e^(-Ea/RT)

where k is the value of rate constant of the reaction, and A, the "pre-exponential factor", is the value of the rate constant if all of the particles were converted to products. The -Ea/RT term reduces the total fraction of particles by some amount. This fraction increases with the activation energy required, Ea: thei higher the Ea, the fewer particles can form products. The value of RT is equal to the average kinetic energy. Thus the activation energy shows us the fraction of molecules that form products at a given temperature, because they have the sufficient kinetic energy to form products. 

Furthermore, since a reaction reaches equilibrium when the forward and reverse rates are equal, then the ratio of forward and reverse rate constants gives us the equilibrium constant. So from that single verticle line on the Maxwell-Boltzmann distribution, we can learn not only how fast the reaction reaches equilibrium, but how much product there will be at equilibrium. So much powerful predictive and conceptual insight comes together so elegantly in this single graph, that it is one of my favorite topics in chemistry.

As described in the Wikipedia article, the Maxwell-Boltzmann distribution was identified empirically by Maxwell in 1860. Collision theory and kinetic molecular theory provide the conceptual frameworks for these calculations. Boltzmann then developed the mathematical proof (more rigorously than the outline above) showing that this distribution is predicted by the system (of gas particles) maximizing its total entropy (distribution of thermal energy) in three dimensions.

Another interesting historical aspect of this discovery is that it's the first ever example of the chi-squared distribution, which is one of the moset extensively used statistical distributions today. The chi-squared distribution is simply the distribution of the square of the standard normal distribution. Where Z ~ N(0,1) then Z^2 = Q ~ Chi-squared. 

__PART 1__ Learn Python's animation libraries (plotly; matplotlib) using random numbers (i.e. not based on physics of collisions).

__PART 2__ Write a C library to accurately calculate the particle dynamics (elastic collisions with conservation of energy and momentum), and update their new velocities. 

__PART 3__ Combine parts 1 and 2 for a functioning animation of particle collisions in 2D space.

__PROJECT STATUS:__ Parts 1 and 3 are complete. Part 2 is underway. 

### Annotated repo contents: files created, decisions made, obstacles encountered, and project outcomes.

###### file name: project.py
Animation:  
I researched a variety of Python libaries that supported interactive animations. Candidates included manim ("math animations") (Grant Sanderson, <a href="https://www.youtube.com/c/3blue1brown">3Blue1Brown<a>) and bokeh, for interactive Python plots. GUI options including Vulcan, Metal, vpython (GlowScript),  OpenGL, pyqt5 and tkinter. I decided against GUI options and opted for a base case of creating an animated video using matplot.animate (pyplot). This worked well with my stretch goal of adding interactivity with plotly and dash Python libraries, and pytorch for very computationally intensive future projects.  

I also found a variety of dedicated software communities for certain types of scientific animations. <a href="https://en.wikipedia.org/wiki/CompuCell3D">CompuCell3D<a> for the cellular Potts model (cells growing over a surface). <a href="https://web.archive.org/web/20210920063153/https://journals.plos.org/ploscompbiol/article?rev=2&id=10.1371/journal.pcbi.1006220">Tellurium</a>, a Jupyter notebook that includes built-in support for animation of scientific models and is compliant with the synthetic biology <a href="https://co.mbine.org/">standard registry of parts.<a> These are useful for highly specialized mathematical modeling and simulation of specific processes, and were great to discover, if not relevant for the current project. 

Data types:  
My decisions on what data structures to use were determined first by what could be animated. All animation is essentially a flip-book of freeze-frame images that are shown in a time series. The faster the frame rate, the smoother and more realistic the animation, at the expense of processing power and time. The computer science of video animation (i.e. computer graphics) is developed to a high level of sophistication, including specialized chips (GPU) that render a series of frame images into a continous video stream. A critical determination in computer animation is whether your images will be created and rendered in real time, such as video games, or whether it will be rendered ahead of time and streamed at a later date, like digital special effects in cinema. 

I sought out an <a href="https://www.youtube.com/c/PrimerLearning/videos">experienced animator</a> on Discord for advice. I wanted to determine whether my animation would need to be real-time rendered, for example by using a python generate() function for each frame. It turned out that matplotlib.animate only offers two-step animation (rendering first; streaming later). matplotlib.animate does offer the option of 'object-order' (a single particle is updated) or  'image-order' (the entire plot is updated each frame). Due to the number of particles, I chose an 'image-order' animation strategy: I would calculate the new positions for all the particles, once per frame. That meant representing a frame as a 3D array, where the first two dimensions were particle position and velocity, and the final dimension was the number of particles per frame. 

C integration:  
I imported the Python cytpes library in order to bind my C library functions and import them into Python. 

It was necessary to cast my NumPy arrays to 32-bit floats, otherwise the different float sizes created an interesting effect where an array with the same pointer address appeared to have different values in C vs Python. In fact it was the different stride length (array element size of 32-bit vs 64-bit) that created this illusion. 

Each time the frame is updated in the python animate() function, it is passed by reference to C where the new particle positions are calculated. The relationship between the frame rate and the particle speed is controlled in order to illustrate a smooth animation of continuous particle motion. The histogram looked best at around 2 frames per second. I needed to know the size of the markers to calculate when they collided, so I found some online code to pin the marker size to the units of the x axis of the scatterplot. Then I chose x unit sizes so that the particle animation would look smooth and continuous for the actual particle speeds, approx. 450 meters/sec for room temperatures. 

###### file name: particles.c
CBLAS:  
The complexity of accessing CBLAS from <a href="https://www.netlib.org/blas/">source files</a> ported in Ubuntu was challenging for me. <a href="https://www.netlib.org/atlas/">ATLAS</a> was a welcome discovery as it greatly simplified and optimized working with CBLAS in codespace. Additionally, ATLAS automatically tunes the CBLAS library to the user's specific machine architecture for maximum performace. 

Data types:  
I took inspiration from CBLAS on how use pointer arithmetic to access vectors of position and velocity from my 3D particle frame. Vectors (1D arrays) and matrices (multi-dimensional arrays) are the standard units of calculation for linear algebra. The vector is represented as the start (array index), stride (calculated by multiplying dimension lengths) and length (number of elements). This allowed me to access and update particle kinetics by reference rather than copying data. Representing the particle physics algoritms with vectors also made the logic of the program easier to write and to read. 

Particle Dynamics:  
The physics of particle collisions is very simple if one assumes elastic collisions where kinetic energy and momentum are conserved. However, I would need to understand both the linear algebra of calculating particle dynamics, and also how to computationally manage potentially hundreds of particle interactions per frame. Thankfully I happened to find excellent references for both. 
The linear algebra of elastic particle collisions was described <a href="https://www.vobarian.com/collisions/2dcollisions2.pdf">here</a>. 
The programming and computation strategies for keeping track of hundreds of independently moving objects in one frame was discussed at a high level <a href="https://www.youtube.com/watch?v=eED4bSkYCB8">here</a>.

I chose the space partitioning technique described, which divides the frame (Cartesian plane plot) into sections iteratively in order to sort which particles are colliding, and which are not. The sorting is accomplished by a K-D tree, and the nodes of the tree are particles that are sent to wall_collision() or particles_collision() for updating their speed and trajectory. 

### References

The original animation that inspired this project:
https://en.wikipedia.org/wiki/Maxwell%E2%80%93Boltzmann_distribution#/media/File:Simulation_of_gas_for_relaxation_demonstration.gif

YouTube video that discussed how to programmatically and computationally manage hundreds of independently moving particles.
https://www.youtube.com/watch?v=eED4bSkYCB8

Linear algebra of elastic particle collisions:  
https://www.vobarian.com/collisions/2dcollisions2.pdf

History and development of the M-B distribution:  
https://en.wikipedia.org/wiki/Maxwell%E2%80%93Boltzmann_distribution  
https://en.wikipedia.org/wiki/Maxwell%E2%80%93Boltzmann_statistics  
https://chem.libretexts.org/Bookshelves/Physical_and_Theoretical_Chemistry_Textbook_Maps/Physical_Chemistry_(LibreTexts)/27%3A_The_Kinetic_Theory_of_Gases/27.03%3A_The_Distribution_of_Molecular_Speeds_is_Given_by_the_Maxwell-Boltzmann_Distribution

The chi-squared distribution:  
https://en.wikipedia.org/wiki/Chi-squared_distribution

The Arrhenius equation: 
https://chem.libretexts.org/Bookshelves/Physical_and_Theoretical_Chemistry_Textbook_Maps/Supplemental_Modules_(Physical_and_Theoretical_Chemistry)/Kinetics/06%3A_Modeling_Reaction_Kinetics/6.02%3A_Temperature_Dependence_of_Reaction_Rates/6.2.03%3A_The_Arrhenius_Law/6.2.3.01%3A_Arrhenius_Equation

