# Maxwell-Boltzmann Animation
For interactive simulation of the Maxwell-Boltzmann distribution of kinetic energies of gases

##### Video demo: 
[![Watch the video](paste url to thumbnail.png here )](https://github.com/RhysCAllen/CS50_public/blob/main/files/MB_fakedata.mp4)

##### REQUIREMENTS:

matplotlib 3.8.3
python -m pip install scipy: from scipy.stats import chi2
sudo apt-get install liblapacke-dev
wget lapacke.h
sudo apt-get install ffmpeg
JavaScript ES7
Python==3.11.4
Ubuntu clang version 14.0.0-1ubuntu1.1

##### HOW TO RUN:
With a free GitHub.com account, the repo can be cloned and run within a codespace.

From the codespace terminal, navigate to the /project folder and compile the files:
$ clang -o duckie duckie.c cJSON.c -lm

Then, create a shared library file:
$ clang -fPIC -shared -o duckie.so duckie.c cJSON.c

Finally, navigate to the /frontend folder and run the web app:
$ flask run


##### CONTACT ME:
31254709+RhysCAllen@users.noreply.github.com

#### Project Summary
This repository was submitted in partial fulfllment of the requirements for CS50p: Introduction to Programming with Python (Harvard University via EdX), 2024. This is the final project for the class.

My long-term goal is to be able to write programs that showcase scientific analysis. For this project, I chose to animate an interactive simulation, because it would allow me to combine the speed and power of C, with the data science libraries and data visualization capabilities of Python. This project helped me work towards my goals as a scientist and programmer.

#### Skills previously used during cs50:
    -Writing and importing C libraries into Python

#### Skills new for this project:
    -Model parameters of Maxwell-Boltzmann distribution with linear algebra
    -Using matplotlib.animation in Python
    -writing and navigating a K-D tree in C for space-partitioning of each animation frame

#### Features
    Basic implementation:
        -Non-interactive; two particles bouncing around a square 2D frame
        -User input of parameters such as temperature and what type of particles (eg oxygen, neon, etc).

    Stretch goals:
        -An interactive simulation that shows a probability distribution of gas particle kinetics
        -Refactor using Pytorch for large sample size (Google Cloud VM with GPU for using tensors instead of matrices)
        -color


#### Description: project background and goals

The original inspiration for this project came from the Wikipedia article for the Maxwell-Boltzmann distribution, which has a beautiful animation of both gas particle kinetics and their corresponding probability distribution. Seeing the animation on the Wikipedia page made me want to recreate it as a tribute to one of my favorite topics in chemistry, and to grow my programming skills for creating data visualizations.

Thermometers are essentially speedometers for molecules: when we place a thermometer in a sample, we are getting an estimate of the average speed at which its molecules are colliding.  However, temperature is a macroscopic property: on a molecular level, particles have a range of speeds, some faster and some slower. The average speeds determine the given temperature. The probability of what speeds are present for a single temperature is predicted by the Maxwell-Boltzmann distribution.

This simple and elegant model, with only one parameter (the degrees of freedom), allows students to see why some chemical reactions, like spoiling milk, happen faster at warmer temperatures, because they have a larger fraction of particles with sufficient energy to react.  With a simple vertical line, we can see the effect of a catalyst on reaction rates, and why some reactions go to completion whereas others remain a dynamic equilibrium of reactants and products.  This vertical line represents the activation energy necessary for reactants to form products, and is a perfect visual representation of the Arrhenius equation:

ln k = ln(constant*f)
ln k = ln A + ln f; where f is the fraction of molecules with sufficient kinetic energy. Since ln f = -Ea/RT, we have

ln k = lnA - Ea/RT, or

k = A*e^(-Ea/RT)

where k is the value of rate constant of the reaction, and A, the "pre-exponential factor", is the value of the rate constant if all of the particles were converted to products. The -Ea/RT term reduces the total fraction of particles by some amount which increases with the activation energy required, Ea. The value of RT is equal to the average kinetic energy. Thus the activation energy shows us the fraction of molecules that form products at a given temperature, because they have the sufficient kinetic energy to form products. It is logical that the ratio of the activation energy over the average kinetic energy at a given temperature will give you the fraction of molecules with sufficient energy to form products at a certain temperature.

Furthermore, since a reaction reaches equilibrium when the forward and reverse rates are equal, then the ratio of forward and reverse rate constants gives us the equilibrium constant. So from that single verticle line on the Maxwell-Boltzmann distribution, we can learn not only how fast the reaction reaches equilibrium, but how much product there will be at equilibrium. So much powerful predictive and conceptual insight comes together so elegantly in this single graph, that it is one of my favorite topics in chemistry.

As described in the Wikipedia article, this distribution was identified empirically by Maxwell in 1860. Collision theory and kinetic molecular theory provide the conceptual frameworks. Boltzmann then developed the mathematical proof (more rigorously than the outline above) showing that this distribution is predicted by the system (of gas particles) maximizing its total entropy (distribution of thermal energy) in three dimensions.

Another interesting historical aspect of this discovery is it is the first ever example of the Chi-squared distribution, which is one of the moset extensively used statistical distributions today. The chi-squared distribution is simply the distribution of the square of the standard normal distribution. Where Z ~ N(0,1) then Z^2 = Q ~ Chi-squared. It is a special case of a gamma distribution.

*Part 1* Teach myself how to use python's animation libraries (plotly; matplotlib) on a single moving object.

*Part 2* Write a C library to calculate the particle dynamics

*Part 3* Combine parts 1 and 2 for a functioning animation of particle collisions in 2D space.

#### Annotated repo contents: files created, decisions made, obstacles encountered, and project outcomes.

 ###### file name: particles.c
Cosmic ray bit flips are rare events, meaning that they occur singly, while their neighboring bits nearby remain unaffected.
The C programming language does not have bit-addressable memory. The smallest unit of addressable memory is a byte (8 bits).

Bits can be changed indirectly by mathematical operations. For example, adding or subtracting 1, or mutiplying or dividing by 2. However, these operations will most often change multiple bits per byte, and they are not random.

An input char (one DNA base) is assumed to have any ASCII value between 0 and 255. Several possible strategies were considered to flip a random bit per char (byte), including defining a bit field of size 1, or using bit field operators. I used a bit field operator and some if-then statements to identify the value of a randomly selected bit. I then used binary arithmetic to flip the bit.

For the animation, I wanted to simulate a saturation of random mutagenesis (all chars are flipped once, in random order, but no chars are flipped more than once). I used the Fisher-Yates algorithm to randomize the order of a range of integers. This allowed me select each index (base) of a DNA input once, in random order.

Data types:
I initially chose an array of structs as my bitflip.c output, in order to include a DNA string field and its corresponding mutated DNA base char index field, for each mutation. I was unable to parse an array of structs in JavaScript, so I refactored the entire project using an array of JSON object strings instead, by including cJSON.h software from a third party. Because JSON objects, like Python dictionaries, do not have inherent order, I was concerned that the mutation order would be lost when parsed with JavaScript, interfering with the animation. However, I learned that order is preserved with JSON object strings in the newest version of JavaScript (ES7) for my use case. Unfortunately, web browsers deploy JavaScript versions unevenly and incompletely, so my program will not animate mutations in the correct order on older web browsers.

 ###### file name: app.py
 C integration:
 I researched several options for combining C and Python, including writing a Python extension module for my C functions. The simpler solution of using the ctypes module was sufficient for this project. I created a library of my C functions, and imported them to my flask app using the Python ctypes module.

 Animation:
Flask supports animations through streaming templates, by wrapping a route function around a second generate() function.
 However, this results in an image or string that is continuously appended, rather than an image or string that is replaced in a single location on the page.
For replacement, I can do something more complicated with flask, or I can send my animation all at once, and control the animation with JS. The documentation and Discord support for Flask was insufficient for me at this time. Instead, I used
the setTimeout() function combined with HTML elements allowed me to show one DNA string replaced by another, to simulate an animation with JavaScript.

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
