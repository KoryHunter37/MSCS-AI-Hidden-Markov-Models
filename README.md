# MSCS Project

This repository overviews an Artificial Intelligence design project which I completed while completing my Master's in Computer Science at Georgia Tech. The source code for this project is not publically available, in compliance with the Georgia Institute of Technology Academic Honor Code<sup><a href="https://policylibrary.gatech.edu/student-affairs/academic-honor-code">[1]</a></sup>.

Instead, I have substituted my raw code with a guided walkthrough of the project itself, the steps I went through to complete it, and the skills I acquired by solving it.

# Overview

Hidden Markov Models are used extensively in Artificial Intelligence, Pattern Recognition, Computer Vision, and many other computer fields. If a system has unobservable (hidden) states and each state is independent of the prior, then I can create a model of that system using probability distributions over a sequence of observations. The idea is that I can provide this system with a series of observations to use to query what is the most likely sequence of states that generated these observations.

# Examining Morse Code Sequences

Morse Code represents each letter by a unique sequence of dots and dashes. In theory, the duration of a dash is three times the duration of a dot. Each dot or dash is followed by a short silence, equal to the dot duration. 

The letters of a word are separated by a space equal to three dots (one dash), and the words are separated by a space equal to seven dots. The dot duration is the basic unit of time measurement in code transmission.

Here is a chart which diagrams these dots and dashes in visual form:

<p align="center"><img width="488" height="517" src=images/morse-code.png></img></p>
<div align="center"><b>Fig 1. Morse Code Chart</b></div>

# Encoding the Hidden Markov Model

Essentially, I wanted to take the idea of Morse Code and model probably sequences of characters. To start, I thought about just one letter, 'A'.

<p align="center"><img width="988" height="483" src=images/A.png></img></p>
<div align="center"><b>Fig 2. 'A' Probabilities</b></div>
