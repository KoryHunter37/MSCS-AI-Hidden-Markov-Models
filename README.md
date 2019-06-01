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

Essentially, I wanted to take the idea of Morse Code and model probable sequences of characters. To start, I thought about just one letter, 'A'.

<p align="center"><img width="988" height="483" src=images/A.png></img></p>
<div align="center"><b>Fig 2. 'A' Probabilities</b></div>

To break this down, here are some components of this hidden markov model:

* There are 4 states (A1, A2, A3, Aend) [seen in purple].
   * A1 - 1 (dot) is observed.
   * A2 - 0 (silence) is observed.
   * A3 - three 1s (dash) are observed.
   * Aend is the special state transitioned to after the HMM exits (no more letters or spaces).
* P(prior) is the probability of starting in a particular state [seen in blue].
   * In the example 'A', and for all letters, I start with the first dot/dash state.
* P(transition) is the probability of transitioning from one state back to itself or to a new state [seen in red].
   * A dot always transitions to a silence state.
   * A dash is represented by three 1s. This is done by visiting A3 three times.  First from A2, and twice it self-transitions (really stay put) to A3.
   * Silence always transitions to a dot or dash state.
* P(emission) is the probability of seeing an observation. [in green above].
   * In the case of Morse code, the output will either be a 1 (representing a dot or part of a dash) or a 0 (representing silence).
   
I encoded this model into Python, paying great attention to each listed detail.

After this, I was tasked with generating the transition, prior, and emission probabilities for other letters. To do this, I needed to re-implement the work I had just done, with new values (specifically considering the dot, dash, and silence in new ways).

<p align="center"><img width="310" height="378" src=images/dot.png></img></p>
<div align="center"><b>Fig 3. 'DOT' Probabilities</b></div>
<br/><br/>
<br/><br/>

<p align="center"><img width="310" height="378" src=images/dash.png></img></p>
<div align="center"><b>Fig 4. 'DASH' Probabilities</b></div>
<br/><br/>
<br/><br/>

<p align="center"><img width="310" height="378" src=images/silence.png></img></p>
<div align="center"><b>Fig 5. 'SILENCE' Probabilities</b></div>

# Viterbi Trellis

The goal of all this was to use the provided hidden markov model (states, prior probabilities, transition probabilities, and emission probabilities) to build a viterbi trellis<sup><a href="https://en.wikipedia.org/wiki/Viterbi_algorithm">[2]</a></sup>.

When provided with an evidence vector (list of observations), the function will return the most likely sequence of states that generated the evidence-- as well as the probability of that sequence being **correct.**

If only an incomplete sequence is found with some probability, my algorithm can also find the probability of said incomplete sequence.

I kept track of a back-pointer at each state, which directed me to each state's most likely predecessor. This allowed me to reconstruct the most likely path *after* running my implementation of Viterbi.

# Noise

Of course it would be a bit too easy if the state outputs were always guaranteed with a 100% probability. To make things a little more realistic, I was tasked with handling artificial noise in my model. Now with a slight change to our emission probabilities, its possible for a state to produce the wrong output. To make it even more difficult, I also had to consider transition states which don't always behave as expected.

Here is an example of the previous 'A' diagram shown in Figure 2, but with noise:

<p align="center"><img width="988" height="483" src=images/A-noise.png></img></p>
<div align="center"><b>Fig 6. 'A' Noisy Probabilities</b></div>

As well as other new probabilities to consider under noisy conditions:

<p align="center"><img width="310" height="378" src=images/dot-noise.png></img></p>
<div align="center"><b>Fig 7. 'DOT' Noisy Probabilities</b></div>
<br/><br/>
<br/><br/>

<p align="center"><img width="310" height="378" src=images/dash-noise.png></img></p>
<div align="center"><b>Fig 8. 'DASH' Noisy Probabilities</b></div>
<br/><br/>
<br/><br/>

<p align="center"><img width="310" height="378" src=images/silence-noise.png></img></p>
<div align="center"><b>Fig 9. 'SILENCE' Noisy Probabilities</b></div>

And on top of all this, I had to also implement consideration for more than just *single letter systems.* Adding more than just one letter means that the rules for spaces between letters and words from Figure 1 now apply. And they have noise too!

<p align="center"><img width="310" height="378" src=images/letter-space.png></img></p>
<div align="center"><b>Fig 10. 'Letter Space' Noisy Probabilities</b></div>
<br/><br/>
<br/><br/>

<p align="center"><img width="310" height="378" src=images/word-space.png></img></p>
<div align="center"><b>Fig 11. 'Word Space' Noisy Probabilities</b></div>

# Breaking it Down

That's a lot to consider, so I'll break this down into some examples.

Suppose the evidence_vector [1,1,1,0,1,1,1,0,1,0,0,0,1,0,0,0,1,1,1,1,1,0,1,1,1,0,1,0,0,0,0,0,1,1,1] is received. Only A, Y, Z, letter pause, and word space are involved. A sequence will start with a letter and end with a letter. The same letter may be repeated. The sequence can be made up of one or more words. And a word can be made up of one or more letters.

The beginning of the sequence can be any letter with equal probability. After each letter there is a letter pause or a word space, both with equal probability. A letter pause or word space can transition to any letter with equal probability. When it reaches the end state, it stays at the end state.

Knowing all the information above, which I implemented into my code, my viterbi algorithm should have been able to decode strings of letters even with noise. However, it was quite inaccurate.

# Debugging

I made several mistakes early on, which were simple erroneous considerations. I did not consider that *all letters can transition to both a pause between letters and a pause between words.* I had to go back and add this to my model!

Instead of thinking "The final state of A is the final state of A", I had to think, "This transition needs to also consider ending A, transitioning to a pause between letters, and transitioning to a pause between words."

Some manual probability calculation was needed here, and once I understood the concept, I was able to automate the process for other letters!

I also updated the letter space and word space states themselves, to better consider any of the letters which they could possibly transition to.

# Furthering the Viterbi Trellis!

I was able to use my now upgraded implementation to take on more complicated sequences. Much of my time spent on this assignment was simply implementing the hidden markov models for various states, and fixing the many probability calculation errors I made along the way!

Fortunately, I was able to successfully build a model which considered all relevant letters, and assessed the correct transition probabilities between each state.

I used my improvements to decode the most probable letter sequence from the evidence sequence-- and returned its probability!

While the goal of this assignment was merely to find the *most probable* sequence, I decided to test with actual accuracy metrics to see how close I was getting to the real answer. When run over several hundred randomly generated sequences, my model was able to generate the most probable output sequence as the real output sequence 86.8% of the time.

In situations where the most probably sequence was *not* the correct output, the correct output was always within the top few most probable sequences returned by my algorithm!

# Reflection

This was the final project I completed in my graduate AI studies, and it brought me into an area of the field which I had never explored before. I was reminded that much of implementing AI in the real world is gated behind a rigorous manual setup process, and I feel that going through this lengthy project helped me better recognize and prepare for that.

Much of this course's lecturing was focused on theory, and understanding why things work. This is great for design and discussion, but not always the most ideal learning for real world implementation. Fortunately, the projects we did (especially this one) helped me to hone my actual implementation skills and feel confident that I can overcome the challenges of manual setup so that I can then focus my efforts on the much more interesting and fun part-- designing the ideal automated solution to tackle any problem!
