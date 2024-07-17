
# Cosmic Ray Bit Flip Simulator
For simulation and quantification of cosmic ray bit flips in DNA sequence data



[![Watch the video](https://github.com/RhysCAllen/CS50_public/blob/main/CRBS_thumbnail.png)](https://www.vimeo.com/985784183)

##### Video demo: 

##### REQUIREMENTS:
Flask==2.3.2 \
JavaScript ES7 \
Python==3.11.4 \
Ubuntu clang version 14.0.0-1ubuntu1.1

##### HOW TO RUN:
With a free GitHub.com account, the repo can be cloned and run within a codespace.

From the codespace terminal, navigate to the `/project` folder and compile the files: \
`$ clang -o duckie duckie.c cJSON.c -lm`

Then, create a shared library file: \
`$ clang -fPIC -shared -o duckie.so duckie.c cJSON.c`

Finally, navigate to the `/frontend` folder and run the web app: \
`$ flask run`

##### CONTACT ME:
31254709+RhysCAllen@users.noreply.github.com

#### Project Summary
This repository was submitted in partial fulfllment of the requirements for CS50x: Introduction to Computer Science (Harvard University via edX), 2023. This is the final project for the class.

My long-term programming goal is to learn scientific computation: using the power of computers to create, analyze, and visualize scientific discoveries, especially related to microbiology. I chose to focus on writing web applications for this project in order to combine the speed and power of C, with the data science libraries and web-based visualizations of Python and JavaScript. This project helped me work towards my goals as a scientist and programmer.

#### Skills previously used during cs50x:
    -C, Python, flask, jinja, HTML, CSS, JavaScript
    -Writing a dynamic web app

#### Skills new for this project:
    -learn about cosmic ray bit flips, and implement a bit flip simulator for DNA sequence in C
    -using ctypes to import my custom C library into Python
    -working with JSON string objects in C, and Fetch API in JavaScript

#### Features
    Basic implementation:
        -An interactive flask web app of bit flip simulation in DNA sequences
        -User input of parameters such as DNA target size and amount of radiation.
        -Output is animation simulating bit flips according to user parameters

    Stretch goals:
        -Additional database-scale simulation scoped to page (pixels rather than DNA bases)
        -Update randomization of DNA base selection from Fisher-Yates to binomial probability
        -Improve aesthetics with CSS, such as adding Dark Mode.
<br>
<br>
<br>

#### Description: project background and goals
The original inspiration for this project came from this article, suggesting that modern large-scale databases can detect changes in cosmic radiation:
https://blog.mozilla.org/data/2022/04/13/this-week-in-glean-what-flips-your-bit/

All computers are cosmic ray detectors. Any time a cosmic ray hits a computer chip, it can change a bit (from 0 to 1, for example). Does this matter? How often do bit flips occur?

Bit flips are similar to DNA mutations called SNPs, or single nucleotide polymorphsms. In this type of mutation, a single letter of DNA is changed to a different letter, such as A -> T. SNPs are common in cells through natural mutagenic processes like DNA replicaton. Just like bit flips, SNPs have the potential to do harm, by changing the sequence of data used to store critical information. Changing a 0 to 1 could stop a computer program from working. Changing a DNA nucleotide from A to T could stop a gene from working.

The simplicity of DNA data, containing just 4 letters, might make it a good test subject for bit flip simulation and detection. Bioinformatic tools could be used to detect and analyze DNA 'mutations' created by simulated bit flips. Outside of simulations, real-world sequence repositaries like the National Center for Bioinformatic Information have extra protections against cosmic rays for long-term storage of DNA sequence.  If a bit flip occurred and changed the world's digital record of a DNA reference genome, would we even know? If so, could we fix it?

*Part 1* of this project is to see what I could find out about whether the world's largest DNA databases monitor or protect against cosmic ray bit flips. These databases have a lot of redundancy: many of the same genome sequences are stored in different databases across the globe. But, the databases are also quite large, too large for comprehensive version control, so if one database had a bit flip, how would we know which record is the correct one? I was curious if I could find any info about this. The results of part 1 include links in the FAQs section below.

*Part 2* was to research cosmic ray bit flip information and render a quantitatively accurate simulation of it in C. The program will ideally be able to simulate bit flips, and independently detect bit flips. What is the variation in rates of cosmic ray irradiation on the surface of Earth? How much radiation is required before a bit flip is likely to occur? And, how would would bit flips specifically change the DNA sequence stored on the irradiated database servers? Might we be able to create a 'sentinal' program that does post-hoc detection and correction of bit-flipped sequences of DNA in our database repositories? The result of part 2 is the creation of a C library that simulates pseudo-random bit flips from a DNA sequence.

*Part 3* Is to animate the results of the C program on an interactive web page with the help of Flask and JavaScript.
<br>
<br>
<br>

#### Annotated repo contents: files created, decisions made, obstacles encountered, and project outcomes.

 ###### file name: duckie.c
Cosmic ray bit flips are rare events, meaning that they occur singly, while their neighboring bits nearby remain unaffected.
The C programming language does not have bit-addressable memory. The smallest unit of addressable memory is a byte (8 bits).

Bits can be changed indirectly by mathematical operations. For example, adding or subtracting 1, or mutiplying or dividing by 2. However, these operations will most often change multiple bits per byte, and they are not random.

An input char (one DNA base) is assumed to have any ASCII value between 0 and 255. Several possible strategies were considered to flip a random bit per char (byte), including defining a bit field of size 1, or using bit field operators. I used a bit field operator and some if-then statements to identify the value of a randomly selected bit. I then used binary arithmetic to flip the bit.

For the animation, I wanted to simulate a saturation of random mutagenesis (all chars are flipped once, in random order, but no chars are flipped more than once). I used the Fisher-Yates algorithm to randomize the order of a range of integers. This allowed me select each index (base) of a DNA input once, in random order.

Data types:
I initially chose an array of structs as my bitflip.c output, in order to include a DNA string field and its corresponding mutated DNA base char index field, for each mutation. I was unable to parse an array of structs in JavaScript, so I refactored the entire project using an array of JSON object strings instead, by including cJSON.h software from a third party. Because JSON objects, like Python dictionaries, do not have inherent order, I was concerned that the mutation order would be lost when parsed with JavaScript, interfering with the animation. However, I learned that order is preserved with JSON object strings in the newest version of JavaScript (ES7) for my use case. Unfortunately, I web browsers deploy JavaScript versions unevenly and incompletely, so my program will not animate mutations in the correct order on older web browsers.

 ###### file name: app.py
 C integration:
 I researched several options for combining C and Python, including writing a Python extension module for my C functions. The simpler solution of using the ctypes module was sufficient for this project. I created a dynamically linked library of my C functions, and imported to my flask app using the Python ctypes module.

 Animation:
Flask supports animations through streaming templates, by wrapping a route function around a second generate() function.
 However, this results in an image or string that is continuously appended, rather than an image or string that is replaced in a single location on the page.
For replacement, I can do something more complicated with flask, or I can send my animation all at once, and control the animation with JS. The documentation and Discord support for Flask was insufficient for me at this time. Instead, I used
the setTimeout() function combined with HTML elements allowed me to show one DNA string replaced by another, to simulate an animation with JavaScript.

###### file name: string.js
I used layout.html to establish common web page features such as the navigation bar. I extended layout.html for specific web page features such as the DNA string 'mutation' animation.
I learned how to use JavaScript's Fetch API so that the DNA animation would not require that the web page reload after the user submitted their DNA input. I used the setTimeout() function to simulate animation by updating innerHTML after regular time intervals.
<br>

##### FAQs about gamma rays, DNA databases, bit flips, etc. from project results
Q: Are some or all bit flips in DNA sequence detectable? If so, can they be corrected? \
A: Yes, most bit flips of DNA sequence stored in a computer (both real and simulated) are detectable. Of the total possible bit flip mutations for a single DNA base (letter), 95% or 243 / 255 are detectable. Only those flips that convert one of the four bases to another, such as A --> T, are undetectable post-hoc, unless it is directly compared to a reference known to be free of mutations (version control), or other software method (Hamming code).

Q: How much potentially descructive cosmic radiation reaches the Earth's surface from space? How much of that can get to a typically stored computer chip and cause a bit flip? What percent of potential flips actually cause a flip, versus being reflected or absorbed harmlessly? \
A: Precise numbers were hard to come by, but some references are provided below. A study that used computers inside caves as a negative control (no cosmic rays in caves) established a baseline of 5,950 failures in time (FIT = failures per billion hours) per DRAM ("standard" RAM) chip at sea level on Earth. Cosmic radiation is naturally occurring throughout the universe (a product of nuclear reactions in stars). The Earth's atmosphere screens out the majority, so bit flips increase with Earth elevation. Crunching the available numbers, a conservative estimated 11.9 flips / billion hours of operation / RAM chip for every meter gained in elevation was used.

Q: Can cosmic rays harm humans? For example, can it cause DNA mutation directly in our cells (not a bit flip)? \
A: Yes, humans and other organisms also absorb cosmic rays, and if it reaches the DNA in our cells, it can ionize atoms. Ionizing radiation can cause physical changes in DNA molecules leading to mutation, just like ionizing radiation from cosmic rays can flip bits. Most people's risk of mutation is much higher from other sources. Most sources agree that over 50% of our exposure to environmental background ionizing radiation comes from radon gas, and less than 25% from cosmic rays. Airline pilots and astronauts have higher levels of gamma ray exposure.

Q. Are bit flips too rare to matter on Earth? How many bit flips happen on my desktop computer per year? Is there a way that backend engineers can protect servers storing sensitive data from bit flips, or detect when bit flips occur? \
A. Interestingly, cosmic ray bit flips are a real concern in modern super computing. The concern mostly relates to the "standard" RAM (DRAM), as this presents the largest and ever-growing target for cosmic rays. Petascale supercomputers have O(10-100 TB) of DRAM, growing to Exascale: O(1-100 PB) of DRAM in the future. Modern hard drives use Error Correction Codes (algorithms that detect and correct bit flips from various sources). In space, techniques such as radiation hardening (computer chips made from specially treated silicon) to protect against bit flips. Other missions may use redundant copies of essential programs for real-time detection and correction using version control.
<br>
<br>
<br>

##### References

General overview and real-world examples of bit flips affecting video games, commercial airline flights, and election outcome from computerized ballots:
https://www.youtube.com/watch?v=AaZ_RSt0KP8

Modern supercomputers and very large databases as cosmic ray detectors:
https://blog.mozilla.org/data/2022/04/13/this-week-in-glean-what-flips-your-bit/

https://spectrum.ieee.org/how-to-kill-a-supercomputer-dirty-power-cosmic-rays-and-bad-solder

Sea-level bit flip rate established by using a cave negative control:
"A White Paper on the Benefits of Chipkill-Correct ECC for PC Server Main Memory" Timothy Dell, 1999

The 'OMG' particle: ultra-high energy cosmic ray detected on Earth (1991).
https://www.scientificamerican.com/article/the-brightest-gamma-ray-burst-ever-recorded-rattled-earths-atmosphere/

How bit flips are measured (FIT rates) and current rates for supercomputers in the United States (Los Alamos National Lab, Berkeley National Lab):
https://science.osti.gov/-/media/ascr/ascac/pdf/meetings/201512/ResilienceASCAC2015.pdf

Modern computing protection against soft errors in hard drives, not just RAM:
https://datarecovery.com/rd/what-are-hard-drive-error-correction-codes-eccs/

Individuals trying to induce or detect bit flips in home computers:
https://news.ycombinator.com/item?id=24700987

A program you can run on your own computer to detect bit flips:
https://github.com/Smerity/bitflipped

