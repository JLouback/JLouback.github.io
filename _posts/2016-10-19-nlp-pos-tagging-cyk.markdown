---
layout: post
title:  "NLP: Part-Of-Speech Tagging with CYK"
date:   2016-10-22 10:06:52
categories: tech
---

During my MSc program, I was lucky to squeeze into [Michael Collin](http://www.cs.columbia.edu/~mcollins/)'s NLP class. We used his [Coursera course](https://www.coursera.org/course/nlangp) as part of the program, which I'd highly recommend.

Recently I decided to review my NLP studies and I believe the best way to learn or relearn a subject is to teach it. This is the second in a series of 4 posts with a walk-through of the algorithms we implemented during the course. I'll provide links to my code hosted on Github.

*Disclaimer: Before taking this NLP course, the only thing I knew about Python was that 'it's the one without curly brackets'. I learned Python on the go while implementing these algorithms. So if I did anything against Python code conventions or flat-out heinous, I apologize and thank you in advance for your understanding. Feel free to write and let me know.*

## The Concept

The Cocke–Younger–Kasami algorithm is used to parse context free grammars. A context free grammar (CFG) contains a set of rules that describe a [formal] language. For example, for the English language we would set a rule that says an article is always followed by a noun or an adjective(s) + noun combo. A context free grammar has to have enough rules to cover all the possible variations in the language. The rules can be one -> one, one -> many or one -> stop. There are two types of building blocks for these rules; _non-terminals_ and _terminals_. A non-terminal contains one or more terminals, for example, a nominal phrase. A nominal phrase contains a noun and maybe also an article and/or adjectives. The noun, article, and adjectives are all _terminals_. So for the sentence _"The dog barked."_ we could establish the following rules:

_S -> NP VP_

_NP -> DET N_

_VP -> V_

_DET -> The_

_NOUN -> dog_

_VERB -> barked_

Where _S_ is the sentence (or rather, the start symbol of the sentence), _NP_ is the nominal phrase, _VP_ is the verbal phrase, _DET_ a determinant, _NOUN_ a noun, _VERB_ a verb.

These rules can create a part-of-speach parse tree for any given sentence providing an understanding of how different parts of the sentence interact with and affect each other. This can be useful for machine translation, information extraction and other scenarios. However, there are cases where there is more than one grammatically correct parsing option. Take the sentence _"He fed her dog food"_. This could be interpreted as (A) A man feeding a woman's dog some food or (B) A man feeding a woman some dog food. A human would expect option (A) to be the correct interpretation (hopefully?), but would an algorithm?

The CYK algorithm enriches the CFG by adding a parameter _q(X -> Y)_ that could be described as the probability of choosing rule _X -> Y_ (as opposed to some other rule _X -> Z_) given that we are currently at _X_. The probability of a parse tree is the product of all _q_ parameters for the rules that compose the tree. Given these _q_ parameters, the CYK algorithm chooses the most likely parse tree out of all possible parse trees. Note that a parse tree has a cascading effect, you may chose a rule _X -> Y_ because it has a higher _q_ parameter than _X -> Z_ but then the subtree starting from _Y_ may not have a valid rule to fit the sentece (_q_ parameter 0) or is much less likely than a subree starting from _Z_. In that case, the rule _X -> Z_ would have been the best choice. In other words, it's not enough to just use a "greedy" technique and always choose the rule with the highest _q_ parameter.

## The Requirements

Training data: The file [parse_train.dat](https://github.com/JLouback/nlp-CKY/blob/master/parse_train.dat) provided by prof. Michael Collins (from the Wall St. Journal corupus) has a series of sentences already tagged with their respective POS and parsed into a tree. Each line is one tree, each level of a tree separated by square braquets.

Development data: The file [parse_dev.dat](https://github.com/JLouback/nlp-CKY/blob/master/parse_dev.dat) provided by prof. Michael Collins has a list of sentences (one per line).

Script for rule count: The file [count_cfg_freqs.py](https://github.com/JLouback/nlp-viterbi/blob/master/count_cfg_freqs.py) provided by prof. Michael Collins contains a script to produce the rule counts.

Training data rule count: Run **python count cfg freqs.py parse train.dat > cfg.counts** to obtain the list of rule counts from the training data. The format is **count ruleType rule** separated by tabs. Rules can be *unary* (example: _V -> run_), *binary* (example: _Sentence -> NP VP_) or *nonterminal* (list of acronyms representing nonterminals, example: _NP_).


## The Algorithm
Python code: [cyk.py](https://github.com/JLouback/nlp-CKY/blob/master/cyk.py)
Usage: python cyk.py cfg_vert.counts parse_dev.dat cfg.counts > [output_file]
Summary: 

**Optional Preprocessing**

Re-label words in training data with frequency < 5 as '_RARE_' - This isn't required, but useful to increase precision. If used, perform Step 1 (see below) then re-label the words to redo Step 1.

Python code: [relabel_rare.py](https://github.com/JLouback/nlp-CKY/blob/master/relabel_rare.py)

Usage: python relabel_rare.py [input_file]

Pseudocode:

1. Uses Python Counter to obtain word counts in [input_file]; removes all word-count pairs with count < 5, store remaining pairs in a dictionary named rare_words.
2. Iterates through each line in [input file], checks if word is in rare words dictionary, if so, replaces word with _RARE_.


**Step 1. Get binary, unary and terminal rule counts**

Python code: [count_cfg_freq.py](https://github.com/JLouback/nlp-CKY/blob/master/count_cfg_freq.py)

Pseudocode: Iterate through each line in the **parse_train.dat** file and obtain counts for binary rules (example: S -> NP VP), unary rules (DET -> the) and nonterminals (examples: ADJ, VERB, etc). The output has one count+rule per line, each item of the line is separated by spaces. The line format is

{count} NONTERMINAL {X} in the case of listing a nonterminal.

{count} UNARY {X} {Y} in the case of a unary rule _X -> Y_

{count} BINARY {X} {Y} {Z} in the case of a binary rule _X -> Y Z_

The abbreviations used are pretty intuitive. It's not entirely necessary to understand the meaning of said abbreviations, but here's a list of the most common nonterminals:

S   start of sentence

NP  nominal phrase

VP  verbal phrase

NOUN  noun

VERB  verb

ADJ   adjective

ADV   adverb

PP  preposition

DET   determinant

The script listed above (method get_counts) will return 3 dictionaries. The dictionary with terminal counts has a string as key and an int as value. The key is the terminal symbol, value is the count. For example, {"DET":5623}. The unary counts have a string as key and a dictionary with {string:int} as value. The primary key is the unary symbol; the value has the actual word as key and it's count. For example, {"DET":{"the":4833}}. Finally the binary counts are also a string as key and a dictionary with {string:int} as value. The primary key is still the X non-terminal in the binary rule X -> Y Z; the value has a dictionary with "Y Z" (the right had part of the binary rule) with the respective count as value. For example, {"S":{"NP VP":101788}}.

It's not as bad as it sounds.


**Step 2. Context-Free Grammar to Probabilistic Context-Free Grammar: calculate rule parameters**

Python code: [cyk.py](https://github.com/JLouback/nlp-CKY/blob/master/cyk.py)

1. Get the 3 dictionaries with terminal, unary and binary rule counts.
The following steps are done for each line (sentence) in the development data. Basically this is where it all happens, after all you want to parse a sentence with POS tags. The amount of sentences is irrelevant; just useful to measure accuracy and performance.
2. Create two matrices that are nx(n+1) where n is the length of the sentence (number of words and punctuation symbols). Both matrices contain dictionaries. One will be used to store probabilities of a unary rule (example: {"DET":0.28}) the other will contain the 'backpointer', rather the actual word the unary rule is being applied to (example: {"DET":"the"}). These probabilities and backpointers will be stored on the diagonal starting at [0][1] of their respective matrices.
3. Here's the part that gets slightly confusing to read the code. Starting from the upper left corner (i.e. coordinates, 0 and 1 if your language starts with 0 as the first position of an array), go adding binary rules to the diagonal of probabilities/backpointers:
  * For all the binary rules in your list (dictionary of counts) check if the non-terminal at the current position in the probability matrix (say at [0][1]) and the one "bellow" on the diagonal (in this case, [1][2]) are existing keys in the probability matrix. 
  *  If so, calculate the binary rule probability as
  (binary _X -> Y Z_ rule count / non-terminal count of _X_) * (probability of unary rule for _Y_) * (probability of unary rule for _Z_)
  *  If there hasn't yet been a key-value pair stored in the probability matrix [0][2] with the key _X_, set the value of key _X_ to the probability above. If not, check if the current binary rule has higher probability and if so, update the _X_ entry. Update the backpointer matrix at [0][2] to contain {"X":"Y Z"} (if it's a new key or if the current probability is the highest yet).

4. The upper right corner of the matrix should have an entry with the sentence starter symbol S. This dictionary would only have one entry because it would use the highest probability rule (it adds up). If it doesn't (say your set doesn't use starter symbols) it's no issue, just 'trace' each value in the dictionary (recursively) to see which alternative has the highest probability.

5. Return the parse tree with highest probability.

Step 3 is easier with examples. Take the sentence "The dog barked". So in step 2 you'll probably have entered the pair {"DET":"the"} in the backpointer matrix at [0][1] and {"DET":0.12} (random probability value used here) in the probability matrix at [0][1]. You'd also enter {"NOUN":"dog"} and {"NOUN":0.2} at positions [1][2] in each matrix. So take the rule _NP -> ADJECTIVE NOUN_. Although _DET_ and _NOUN_ will likely have been matched to "the" and dog" and would have been included in the probability/backpointer matrix, the non-terminal _ADJECTIVE_ wouldn't match to "the" so this binary rule is a no-go. Now for the rule _NP -> DET NOUN_ we have a match, so we add an entry {"NP":0.23} to the probability matrix at [0][2] and {"NP":"DET NOUN"} to position [0][2] in the backpointer matrix. We fill up the upper right triangle of the matrix.


The great thing is, this algorithm can tolerate tagging errors. Let's say once or a few times the word "the" was tagged as "ADJECTIVE". So in the backpointer matrix [0][1] you would store the original entry {"DET":"the"} but also {"ADJECTIVE":"the"}. In the probability matrix at [0][1] you'd store {"DET":q<sub>1</sub>} and {"ADJECTIVE":q<sub>2</sub>} where q<sub>1</sub> and q<sub>2</sub> are the probabilities of the unary rules _DET -> the_ and _ADJECTIVE -> the_. Now it's very likely that q<sub>1</sub> > q<sub>2</sub> (unless you have a really badly tagged training set). When checking which binary rule that starts with _NP_ is best, you'll probably run into _NP -> DET NOUN_ and _NP -> ADJECTIVE NOUN_. These two binary rules could both be equally likely in their own right, let's say they both equal b.  

p(NP -> DET NOUN) == p(NP -> ADJECTIVE NOUN) == b

p(NOUN -> dog) == c

p(DET -> the) == q1

p(ADJECTIVE -> the) == q2

and q1 > q2

then b * q1 * c > b * q2 * c

So you will chose the correct option _NP -> DET NOUN_ as the subtree root at [0][2].

**Evaluation**

Prof. Michael Collins provided a [script to pretty print the parse trees](https://github.com/JLouback/nlp-CYK/blob/master/pretty_print_parse.py) and a [performance evaluation script](https://github.com/JLouback/nlp-CYK/blob/master/eval_parser.py).

Usage: 
python pretty_print_parse.py [prediction_file]
python eval_parser.py parser_dev.key [prediction_file]

