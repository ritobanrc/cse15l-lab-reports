# Lab Report 3: Researching Commands

The `grep` utility is an extremely useful command available on Linux systems that can search the contents of many files. The command has an imposing number of options, and today we'll investigate four of them: `-r` (or `--recursive`), `-c` (or `--count`), `-n` (or `--line-number`), and `-e` (or `--regexp`). I found all of these options on the manual page for grep, accessible on most Linux systems with the command `man grep`. 

Note that most commonly used options have both a short form (which is one letter long and indicated with a single dash), and a long form, which is prefixed with two dashes, and is usually a full word.

Lets try running each of these commands on the `written_2` dataset from the American National Corpus.

## Recursive Search (-r)

```bash
grep -r "Crags" written_2
```

Output:
```
written_2/travel_guides/berlitz1/WhereToEdinburgh.txt:        nearest the palace takes walkers along the base of Salisbury Crags, a
written_2/travel_guides/berlitz1/WhereToLakeDistrict.txt:        Harrison Stickle and Crinkle Crags — produce some of the finest vistas
```

The `-r` flag tells grep to recurse through the directory -- notice that the files it finds aren't just in `written_2`, but rather one of its subdirectories. Without `-r`, we'd have to explicitly tell `grep` to search in all of these files (perhaps with a `find` command). Here's another example (this time, we'll write out the full flag, `--recursive`):


```bash
grep --recursive "Football" written_2
```

Output
```
written_2/travel_guides/berlitz2/Poland-WhatToDo.txt:Football (soccer). Football is Poland’s most popular spectator sport. Warsaw’s two first-division football teams are Legia Warszawa (ul. Lazienkowska 3, Tel. 628 43 03) and Polonia Warszawa (ul. Konwiktors,a 6, Tel. 635 14 01).
written_2/travel_guides/berlitz2/Amsterdam-WhatToDo.txt:Football (Soccer)
written_2/travel_guides/berlitz2/Athens-WhatToDo.txt:Football (soccer): Football is a national obsession for the Greeks and Athens’ teams (Panathanaikos and AEK Athens) feature prominently in domestic and European competitions. The season runs from September to May. Panathanaikos Football Stadium is located at Kifissia, a northern suburb — this will be the major stadium for the 2004 Olympic Games, which will be held in the city. Ask your hotel if it is possible to get tickets as these are difficult to come by.
written_2/travel_guides/berlitz2/Algarve-WhatToDo.txt:Football (Soccer)
written_2/travel_guides/berlitz1/WhatToEdinburgh.txt:        Football
written_2/travel_guides/berlitz1/WhatToEdinburgh.txt:        Football (soccer) is a passion in Scotland, though the two
```
This searches the entire `written_2` for the text "Football", and shows you that it's mentioned in several of the travel guide files.

## Counting (-c)
Sometimes, you only care about how many matches there are in each file, rather than what exactly they include. For example, in the Rust programming language, you might be interested in how many times a library uses the `unsafe` keyword (to get an idea of how much code you might need to audit, for example). The `-c` or `--count` flag gives you just this information. 

As an example, suppose you wanted to know which cities had the best nightlife, but were too lazy to actually read the travel guides. You could run:

```bash
grep -c "nightlife" written_2/travel_guides/berlitz1/WhatTo*
```

and get this:
```
written_2/travel_guides/berlitz1/WhatToDublin.txt:0
written_2/travel_guides/berlitz1/WhatToEdinburgh.txt:0
written_2/travel_guides/berlitz1/WhatToEgypt.txt:0
written_2/travel_guides/berlitz1/WhatToFrance.txt:0
written_2/travel_guides/berlitz1/WhatToFWI.txt:1
written_2/travel_guides/berlitz1/WhatToGreek.txt:0
written_2/travel_guides/berlitz1/WhatToHawaii.txt:0
written_2/travel_guides/berlitz1/WhatToHongKong.txt:2
written_2/travel_guides/berlitz1/WhatToIbiza.txt:2
written_2/travel_guides/berlitz1/WhatToIndia.txt:0
written_2/travel_guides/berlitz1/WhatToIsrael.txt:2
written_2/travel_guides/berlitz1/WhatToIstanbul.txt:0
written_2/travel_guides/berlitz1/WhatToItaly.txt:1
written_2/travel_guides/berlitz1/WhatToJamaica.txt:0
written_2/travel_guides/berlitz1/WhatToJapan.txt:0
written_2/travel_guides/berlitz1/WhatToLakeDistrict.txt:0
written_2/travel_guides/berlitz1/WhatToLasVegas.txt:3
written_2/travel_guides/berlitz1/WhatToLosAngeles.txt:1
written_2/travel_guides/berlitz1/WhatToMadeira.txt:4
written_2/travel_guides/berlitz1/WhatToMalaysia.txt:0
written_2/travel_guides/berlitz1/WhatToMallorca.txt:2
```
which unambiguously proves that Maderia has the best nightlife, with Las Vegas being a close second, simply based on the number of occurrences of the word "nightlife" in each of these files.

Alternatively, you might want to know which chapter of a particular book covered a particular topic, like Quantum Mechanics. You could run
```bash
grep -c "quantum" -r written_2/non-fiction/OUP/Kauffman
```
which would give you,
```
written_2/non-fiction/OUP/Kauffman/ch5.txt:0
written_2/non-fiction/OUP/Kauffman/ch3.txt:0
written_2/non-fiction/OUP/Kauffman/ch4.txt:2
written_2/non-fiction/OUP/Kauffman/ch1.txt:2
written_2/non-fiction/OUP/Kauffman/ch8.txt:0
written_2/non-fiction/OUP/Kauffman/ch9.txt:0
written_2/non-fiction/OUP/Kauffman/ch10.txt:36
written_2/non-fiction/OUP/Kauffman/ch7.txt:6
written_2/non-fiction/OUP/Kauffman/ch6.txt:3
```
telling you the answer is Chapter 10, since it has 36 occurrences of the text "quantum" far omre than any other chapter.

## Line Numbers (-n)
Often, when working on a large codebase, the quickest way to find where a particular type is defined will be by `grep`-ing for it. But even then, files might be quite large, so it might be important to know exactly which line of a file it was defined on, so you can go there directly. The `-n` flag can help with this: it gives you the line number of a particular search result.

Picking up from the last example, suppose you got bored with dead and alive cats and decided to switch majors to computer science. To get caught up, you decide to do some reading on Turing machines -- unfortunately, the Kauffman text is too long and covers to many things! No problem, `grep -n` to the rescue -- it'll give you the line numbers.

```bash
grep -n "Turing" -r written_2
```

Voila! Now you know where to look -- Line 15 of Chapter 1, and lines 24, and 37 of Chapter 6 from the Kauffman text (notice that the line number is right after the colon after the file name -- this is standard notation to indicate line numbers, it shows up in many programs like `gdb` and `git`).
```
written_2/non-fiction/OUP/Kauffman/ch1.txt:15:The next step is simple. Any such program can be realized on a universal Turing machine such as the familiar computer. But that computer is an open nonequilibrium thermodynamic system, its openness visibly realized by the plug and power line that connects the computer to the electric power grid. Therefore, and I think this conclusion is cogent, there can be no general law for all possible nonequilibrium thermodynamic systems.
written_2/non-fiction/OUP/Kauffman/ch6.txt:24:Walter Fontana is a theoretical chemist trained by Peter Schuster in Vienna. Walter came to the Santa Fe Institute and made a major intellectual step called “Alchemy.” In chapter 2, I described the emergence of autocatalytic sets of molecular species in a chemical reaction graph. By rather independent intellectual routes that began with physicist John McCaskill’s eVorts to create a computer soup of Turing machines that “operated” on one another, Walter invented “algorithmic chemistry.” Naturally, and most naturally in Santa Fe, where one can be healed by means known nowhere else in the universe, Walter nicknamed algorithmic chemistry “Alchemy.” Unlike the alchemy of Newton’s time, Walter’s works.
written_2/non-fiction/OUP/Kauffman/ch6.txt:37:It is possible that the constraint to algorithmic critters may be the problem. Indeed, I will suggest that the biosphere is richer than that which can, in the normal senses I know, be called algorithmic. That which is algorithmic is eVectively constructable by a formal procedure that begins with definable input “data” and is operated upon by a “program” in the Turing or von Neumann sense. But I will argue that we cannot prestate some biological analogue of the input data, nor is there some biological analogue of the program governing the unfolding of a biosphere. I will argue that the configuration space of a biosphere cannot be finitely prestated, that persistent novelty occurs in the biosphere and universe as a whole. And I will opine that if we cannot finitely prestate the configuration space of a biosphere, then something is odd with how we have been taught to do our science, for in Newtonian physics, Einstein’s physics, and Bohr’s physics, one can finitely prestate the configuration space in question. In chapter 10, borrowing on joint work with quantum gravity scholar and friend, Lee Smolin, I will suggest that if we cannot prestate the configuration space of a universe then “time” is real and necessary, and that the way a universe constructs itself may have analogies to the way a biosphere constructs itself.
```

You get distracted by an offhand comment about Santa Claus and decide to see if your bizarre dataset of English text mentions him at all. Once again, you use the `-n` flag so you can find the line numbers in your files easily.
```bash
grep -n "Santa Claus" -r written_2
```
```
written_2/travel_guides/berlitz2/Amsterdam-WhatToDo.txt:66:If you visit Amsterdam in early December children will enjoy the parade as Sinterklaas (Santa Claus) makes his visit to the city on 5 December. He parades through the streets on horseback.
written_2/travel_guides/berlitz2/Algarve-WhatToDo.txt:66:If you visit Amsterdam in early December children will enjoy the parade as Sinterklaas (Santa Claus) makes his visit to the city on 5 December. He parades through the streets on horseback.
```
Interestingly, there's only two references to it, referring to the Netherlands version, called Sinterklass. Notice that `grep` has once again told you that they appear on line 66 -- so you can jump there directly.

## Regular Expressions
Finally, we get to one of the most powerful tools in the `grep` command: regular expressions! Regular expressions are an extremely sophisticated syntax for making text-matching queries. Indeed, the name `grep` originally comes from the `ed` (which is an _extremely_ old text editor) command `g/re/p`, which means "globally (g) search for a regular expression (re) and print (p) it out" [^1]. Learning to use and parse regular expressions can be really tricky, and websites like [https://regex101.com/] are really helpful in debugging regular expressions.

Here's a few simple examples:
```bash
grep -e "^Life" -r written_2/
```
The `^` indicates the beginning of a line, so this command recursively searches for files that have the string `Life` at the beginning of the line. The output is this:

```
written_2/travel_guides/berlitz2/Cancun-History.txt:Life was difficult in northern Yucatán, as the lack of surface water and the limestone sub-surface made it difficult to grow commercial crops or raise cattle. However, in the late 19th century, the hacienda or plantation owners found a crop that grew successfully and was much in demand around the world — the henequen plant. The fibers produced by henequen could be made into rope and twine, indispensable for sea-faring and international trade. Yucatán was the principal producer, exporting their goods through the port of Sisal on the northern coast. Processed henequen soon became known around the world as sisal.
written_2/non-fiction/OUP/Kauffman/ch1.txt:Life as an Emergent Collective Behavior of Complex Chemical Networks
written_2/non-fiction/OUP/Kauffman/ch9.txt:Life takes its unexpected turns. I have been an academic scientist, a biologist, for thirty years at the University of Chicago, the National Institutes of Health, the University of Pennsylvania, then twelve stunningly exciting years at the Santa Fe Institute. After thirty years, I’ve written the canonical hundred or more scientific articles, was fortunate enough to receive a MacArthur Fellowship, during whose five years my IQ went up and then slumped back to normal as the funding ended, invented and patented this and that, and published two previous books of which I am proud, Origins of Order and At Home in the Universe, both by Oxford University Press.
written_2/non-fiction/OUP/Castro/chR.txt:Life is worth nothing
```

Alternatively, suppose you wanted to learn about important years in the history of Nepal. You could run
```bash
grep -e "In [0-9]\{4\}" -r written_2/travel_guides/berlitz2/Nepal-History.txt
```
This tells grep to search for a string at the beginning of the line (indicated by `^`), that begins with the characters "In ", then is followed by exactly 4 characters from the set of characters [0-9] (i.e. a four-digit number). Be careful with special characters like `{` and `+` in Bash -- they sometimes need to be escaped with backslashes. The command above gives you the output:
```
In 1846, during the course of an unsavory palace intrigue involving an unsolved murder, meddling by the queen, and the massacre of some 100 high-ranking court figures who had been lured to a palace courtyard, the instigator of these events became the power behind the throne. He was an ambitious and unscrupulous 29-year-old named Jung Bahadur Rana. Before long he had exiled the king, replaced him with the youthful crown prince, and arranged to have himself and his family made hereditary prime ministers.
In 1990, in the wake of the Soviet Union’s collapse, Nepalis once again took to the streets to demand an end to the panchayat system. After sometimes bloody protests and under pressure from India’s Congress party, King Birendra finally conceded to allow political parties and the kingdom’s first truly democratic elections were held in 1991. Three years later, the Communist party won a plurality of seats in parliament. However, without a majority, the communists were forced to form a coalition government, and in fact elections since 1994 have repeatedly ended in coalition governments being formed. These governments have proved highly ineffectual in running the country, and by the late 1990s there was growing discontent with the entire democratic process. There was also a growing Maoist uprising taking place in the central hills of the kingdom.
```
Which are certainly two very intriguing and significant years in Nepali history.


[^1]: [http://www.catb.org/~esr/jargon/html/G/grep.html]

P.S. excuse my humor -- I was _very bored_ when writing this.
