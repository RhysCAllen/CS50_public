# Maxwell-Boltzmann Animation
For interactive simulation of the Maxwell-Boltzmann distribution of kinetic energies of gases

##### Video demo:

https://github.com/user-attachments/assets/0efe9a2c-95a2-43b6-8339-1d010c4d7bd8

##### REQUIREMENTS:

Ubuntu 22.04.4 LTS (Jammy Jellyfish)
Python==3.12.2
clang==14.0.0-1ubuntu1.1
libatlas-base-dev==3.10.3-12ubuntu1
matplotlib==3.8.3
scipy==1.13.1
ffmpeg==7:4.4.2-0ubuntu0.22.04.1

##### HOW TO RUN:
With a free GitHub.com account, the repo can be cloned and run within a codespace.

From the codespace terminal, navigate to the /project/backend/ folder and compile the files:

$ clang -o particles particles.c -lcblas

Then, create a shared library file:

$ clang -fPIC -shared -o particles.so particles.c -lcblas

Finally, navigate to the /project folder and run the app:
$ python project.py

To view the resulting simulation, open the animation file:
$ code movie.mp4


##### CONTACT ME:
31254709+RhysCAllen@users.noreply.github.com

#### Project Summary
This repository will be submitted towards fulfllment of the requirements for CS50P: Introduction to Programming with Python (Harvard University via edX), 2024. This is the final project for the class.

My long-term goal is to be able to write programs that showcase scientific analysis, especially related to microbiology. This project allows me to explore the <a href="https://fortran-lang.discourse.group/t/ten-computer-codes-that-transformed-science/595" famous </a> CBLAS library: basic linear algebra subroutines for C. I also wanted to compare my previous experience of programming interactive simulations using JavaScript and HTML, with using Python's powerful interactive animation libraries, matplotlib.animation, plotly, and dash. This project is helping me reach my long-term goal to understand and share scientific discoveries via computing. 

#### Skills previously used during CS50:
    -Writing and binding dynamically linked C libraries to Python

#### Skills new for this project:
    -Model parameters of Maxwell-Boltzmann probability distribution for elastic collisions
    -Using CBLAS, a basic linear algebra library for C, to calculate particle dynamics
    -Write and navigate a K-D tree in C for space-partitioning of particles in each animation frame
    -Using matplotlib.animation in Python

#### Features
    Basic implementation:
        -Non-interactive particles bouncing around a square 2D frame, with display of actual vs theoretical particle energies
        -User input of parameters such as temperature and what type of particles (e.g. oxygen, neon, etc.)

    Stretch goals:
        -An interactive simulation that adjusts to user inputs of activation energy and temperature on reaction rates and Keq
        -color to better illustrate conservation of energy during collisions

#### Description: project background and goals

The original inspiration for this project came from the <a href="https://en.wikipedia.org/wiki/Maxwell%E2%80%93Boltzmann_distribution"> Wikipedia article </a>, for the Maxwell_Boltzmann distribution, which has a beautiful animation of both gas particle kinetics and their corresponding probability distribution. Seeing that animation on the Wikipedia page made me want to recreate it as a tribute to one of my favorite topics in chemistry, and to grow my programming skills for creating interactive simulations.

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

*Part 1* Learn Python's animation libraries (plotly; matplotlib) using random numbers (i.e. not based on physics of collisions).

*Part 2* Write a C library to accurately calculate the particle dynamics (elastic collisions with conservation of energy and momentum), and update their new velocities. 

*Part 3* Combine parts 1 and 2 for a functioning animation of particle collisions in 2D space.

Project progress: Part 1 is complete. Part 3 is partially complete. Part 2 is underway. 

#### Annotated repo contents: files created, decisions made, obstacles encountered, and project outcomes.

 ###### file name: project.py
 C integration:
I used python's cytpes library to be able to standardize arrays, floats, and integers between C and python. 
Arrays are passed by reference to my C library, called in Python. It was necessary to cast my Numpy arrays to 32-bit floats, otherwise the different float sizes created an interesting error where an array with the same pointer address appeared to have different values in C vs Python. In fact it was the different stride length (array element size of 32-bit vs 64-bit) that created this illusion. 

 Animation:
I researched a variety of Python libaries that supported interactive animations. 

 ###### file name: particles.c
Data types:
I initially chose an array of structs as my bitflip.c output, in order to include a DNA string field and its corresponding mutated DNA base char index field, for each mutation. I was unable to parse an array of structs in JavaScript, so I refactored the entire project using an array of JSON object strings instead, by including cJSON.h software from a third party. Because JSON objects, like Python dictionaries, do not have inherent order, I was concerned that the mutation order would be lost when parsed with JavaScript, interfering with the animation. However, I learned that order is preserved with JSON object strings in the newest version of JavaScript (ES7) for my use case. Unfortunately, web browsers deploy JavaScript versions unevenly and incompletely, so my program will not animate mutations in the correct order on older web browsers.



###### file name: string.js
I used layout.html to establish common web page features such as the navigation bar. I extended layout.html for specific web page features such as the DNA string 'mutation' animation.
I learned how to use JavaScript's Fetch API so that the DNA animation would not require that the web page reload after the user submitted their DNA input. I used the setTimeout() function to simulate animation by updating innerHTML after regular time intervals.

##### FAQs about gamma rays, DNA databases, bit flips, etc. from project results
Q: Are some or all bit flips in DNA sequence detectable? If so, can they be corrected? \
A: Yes, most bit flips of DNA sequence stored in a computer (both real and simulated) are detectable. Of the total possible bit flip mutations for a single DNA base (letter), 95% or 243 / 255 are detectable. Only those flips that convert one of the four bases to another, such as A --> T, are undetectable post-hoc, unless it is directly compared to an unmutated reference sequence (version control).

Q: How much potentially descructive cosmic radiation reaches the Earth's surface from space? How much of that can get to a typically stored computer chip and cause a bit flip? What percent of potential flips actually cause a flip, versus being reflected or absorbed harmlessly? \
A: Precise numbers were hard to come by, but some references are provided below. A study that used computers inside caves as a negative control (no cosmic rays in caves) established a baseline of 5,950 failures in time (FIT = failures per billion hours) per DRAM ("standard" RAM) chip at sea level on Earth. Cosmic radiation is naturally occurring throughout the universe (a product of nuclear reactions in stars). The Earth's atmosphere screens out the majority, so bit flips increase with Earth elevation. Crunching the available numbers, a conservative estimated 11.9 flips / billion hours of operation / RAM chip for every meter gained in elevation was used.

Q: Can cosmic rays harm humans? For example, can it cause DNA mutation directly in our cells (not a bit flip)? \
A: Yes, humans and other organisms also absorb cosmic rays, and if it reaches the DNA in our cells, it can ionize atoms. Ionizing radiation can cause physical changes in DNA molecules leading to mutation, just like ionizing radiation from cosmic rays can flip bits. Most people's risk of mutation is much higher from other sources. Most sources agree that over 50% of our exposure to environmental background ionizing radiation comes from radon gas, and less than 25% from cosmic rays. Airline pilots and astronauts have higher levels of gamma ray exposure.

Q. Are bit flips too rare to matter on Earth? How many bit flips happen on my desktop computer per year? Is there a way that backend engineers can protect servers storing sensitive data from bit flips, or detect when bit flips occur? \
A. Interestingly, cosmic ray bit flips are a real concern in modern super computing. The concern mostly relates to the "standard" RAM (DRAM), as this presents the largest and ever-growing target for cosmic rays. Petascale supercomputers have O(10-100 TB) of DRAM, growing to Exascale: O(1-100 PB) of DRAM in the future. Modern hard drives use Error Correction Codes (algorithms that detect and correct bit flips from various sources). In space, techniques such as radiation hardening (computer chips made from specially treated silicon) to protect against bit flips. Other missions may use redundant copies of essential programs for real-time detection and correction using version control.


##### References:

The original animation:
https://en.wikipedia.org/wiki/Maxwell%E2%80%93Boltzmann_distribution#/media/File:Simulation_of_gas_for_relaxation_demonstration.gif

Youtube channel that I used to identify some libraries I could use to get the effects I wanted (interactive animation and simulation).
I did not watch the full video, just the first few minutes to see the libraries he chose.
Mr. P Solver, youtube:
https://www.youtube.com/watch?v=2XckqFzUiYU

History and development of the M-B distribution:
https://en.wikipedia.org/wiki/Maxwell%E2%80%93Boltzmann_distribution
https://en.wikipedia.org/wiki/Maxwell%E2%80%93Boltzmann_statistics
https://chem.libretexts.org/Bookshelves/Physical_and_Theoretical_Chemistry_Textbook_Maps/Physical_Chemistry_(LibreTexts)/27%3A_The_Kinetic_Theory_of_Gases/27.03%3A_The_Distribution_of_Molecular_Speeds_is_Given_by_the_Maxwell-Boltzmann_Distribution

The Chi-squared distribution:
https://en.wikipedia.org/wiki/Chi-squared_distribution

The Arrhenius equation:
https://chem.libretexts.org/Bookshelves/Physical_and_Theoretical_Chemistry_Textbook_Maps/Supplemental_Modules_(Physical_and_Theoretical_Chemistry)/Kinetics/06%3A_Modeling_Reaction_Kinetics/6.02%3A_Temperature_Dependence_of_Reaction_Rates/6.2.03%3A_The_Arrhenius_Law/6.2.3.01%3A_Arrhenius_Equation


General Chemistry background:

Atkins link
