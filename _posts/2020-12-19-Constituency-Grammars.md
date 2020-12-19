---
title: "Constituency-Grammars"
categories:
  - Bài toán NLP cơ bản
tags:
  - constituency
---

# 1. Constituent

Constituent is a word or a group of words that function as a single unit.

Constituent can be a **noun** (N), **noun phrase** (NP), **verb phrase** (VP), ...

For example, NPs like *"the yellow cat"*, *"three parties from Brooklyn"*, *"they"*, ... are constituents.

Constituents can be placed at different positions in the sentence, but the individual words making up the phrase cannot be

E.g:
- *"on September seventeenth"* can be placed at different positions in the sentence:
	-  **On September seventeenth**, I'd like to fly from Atlanta to Denver
	- I'd like to fly **on September seventeenth** from Atlanta to Denver
	- I'd like to fly from Atlanta to Denver **on September seventeenth**
- but the individual words cannot be:
	- \* **On September**, I'd like to fly **seventeenth** from Atlanta to Denver
	- \* **On** I'd like to fly **September seventeenth** from Atlanta to Denver
	- \* I'd like to fly **on September** from Atlanta to Denver **seventeenth**

# 2. Context-Free Grammars

CFG is used for modeling constituent structure.

CFG consists of a set of rules.

![rules](/assets/images/2_rules.png){: .align-center}

![](/assets/images/2_parsetreeNrules.png){: .align-center}


CFG can be thought in two ways:
- a device for generating sentences
- a device for assigning a structure to a given sentece.

A formal CFG must have one start symbol **S**.

fig 12.4

# 3. Some grammar rules for English

## 3.1 Sentence-level Construction

There are 4 common and important construction in English:
- **declarative**: NP VP
	- *I &emsp; want a flight from Ontario to Chicago*
	- *The flight &emsp; should be eleven a.m. tomorrow*
	- *The return flight &emsp; should leave at around seven p.m.*
- **imperative** (command & suggestion): VP
	- *Show the lowest fare*
	- *Give me Sunday’s flights arriving in Las Vegas from New York City*
	- *List all flights between five and seven p.m.*
- **yes-no question**: Aux NP VP
	- *Do &emsp; any of these flights &emsp; have stops?*
	- *Does &emsp; American’s flight eighteen twenty five &emsp; serve dinner?*
	- *Can &emsp; you &emsp; give me the same information for United?*
- **wh-subject question** (Wh-NP is subject): Wh-NP VP
	- *What airlines &emsp; fly from Burbank to Denver?*
	- *Which flights &emsp; depart Burbank after noon and arrive in Denver by six p.m?*
	- *Whose flights &emsp; serve breakfast?*
- **wh-non-subject question** (Wh-NP isn't the subject): Wh-NP Aux NP VP
	- *What flights &emsp; do &emsp; you &emsp; have from Burbank to Tacoma Washington?*
		- > Note: *filghts* (Wh-NP) is far away from main verb *have* in VP => **long-distance dependencies**

## 3.2 Noun Phrase

A noun phrase can consist of a **head** and **modifiers**.

NP &#8594; pre-modifiers &emsp; head &emsp; post-modifiers

- head: the central noun in the noun phrase
- pre-modifiers: 
	- determiner (*a, the, any, this, some,...*): *the flights*, *some flights*
	- predeterminer (*all*): *all the flights*
	- ordinal number (*first, second,..., next, last,...*): *the last flight*, *the first one* 
	- ...
- post-modifiers:
	- PP: *all flights [from Cleveland]*
	- V_ing: *any flights [arriving after eleven a.m.]*
	- to V: *the last flight [to arrive in Boston]*
	- V_ed: *the aircraft [used by this flight]*
	- Relative pronoun: *a flight [that serves breakfast]*
	- ...

fig12.5

## 3.3 Verb Phrase

Consists of the verb and other constituents (modifiers/complements)

VP &#8594; Verb &emsp; complements

4 lines page 12

Not every verb is compatible with every verb phrase. For example:
- want:
	- VP &#8594; V NP &emsp; *I want a flight*
	- VP &#8594; V to-V &emsp; *I want to fly to ...*
- find:
	- VP *&#8594; V to-V &emsp; \* *I found to fly to ...*

Approach to solve the above problem: **subcategorize** verbs into smaller categories

6 lines

> Note: A problem with this approach is the significant increase in the number of rules
and the associated loss of generality.

## 3.4 Coordination

A coordinate NP has the form:

NP &#8594; NP conj NP

where conj is conjunctions like *and*, *or*, *but*,... 

The general form of a coordinate constituent that has type X is:

X &#8594; X conj X

examples [] [] []





	
