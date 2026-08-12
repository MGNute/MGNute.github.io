+++
title = "The Phylostrator & PICAN-PI Graphs"
description = "A GUI and library for creating very specific visualizations of large-scale phylogeny and phylogenetic placement output."
author = "Mike Nute"
date = "2023-04-09"
tags = ["python", "bioinformatics", "metagenomics"]
# categories = ["themes", "syntax"]
+++

One of the chapters of my dissertation in grad school was a data visualization method for phylogenetic placement output. The idea behind phylogenetic placement is that if you have a phylogenetic tree $T$ that has been estimated from some set of sequences $\scriptS={s_1,...,s_n}$, you may find you have a new sequence $s^*$ that you want to put into the tree $T$ without actually re-estimating the entire tree. 
That task ends up being essentially choosing two parameters: the attachment point in $T$ and the length of the new branch, where the attachment point is broadly a reflection of which sequences in $\scriptS$ the new sequence $s^*$ most closely resembles, and the branch length reflects the degree to which it is *novel*. 

Back in 2016, a lot of the common methods for characterizing a microbiome involved identifying clusters of sequences that probably represent a single organism and then labeling those clusters based on their closest representative in a database. That works fine for a lot of applications, but the problem is that it omits just how novel the cluster might be from the nearest database representative. In some cases though, that degree of novelty might contain some pretty useful information. I did a re-analysis of a paper my co-advisor had writteen characterizing the vaginal microbiome of non-human primates and found that the degree of novelty tracked very closely with how evolutionarily related the host species was to humans. I'll describe the figure below in more detail in a second, but it contains this graphic for a human, a chimp, a howler monkey, and a lemur. The important distinction is that chimps are a great ape (as are humans), so they are the most closely related. Howlers are an old-world monkey, so they are second closest, and the lemur is the most distantly related to humans. 

![Visualization of the vaginal microbiome of 4 primates](/phylostrator_graphic.png)

There is a lot going on in these graphics, but here is how to think about them. In each of the four figures the tree drawn in grey is the same. That is essentially the tree-of-life for all known bacteria as of 2016. For our purposes it doesn't totally matter which parts of it represent which microbes, but it's drawn the same way in each figure so the same position in different figures represents the same organism. So think of that as a "map" of the bacterial world. For each host, we want to plot (a) how much of the vaginal microbiome is related to each part of the map, and (b) how "novel" it is relative to the database that the map was built from. And we do that with the colored dots overlaid on the tree.

The way the figure works is that more vivid colors represent larger quantitites, and the hue represents the novelty (where red is low novelty, or a close match to the database, and purple is high novelty, or a poor match to the closest database sequence, with the colors of the rainbow in between reflecting the varying degrees of novelty). I apologize to anyone who is colorblind because the hue coding is not fully accessible, but I find it necessary to have the extra gradations here. But the key to these figures is that the chimp figure is dominated by red dots, while the howler monkey has a combination of red with some blue-purple and some green-yellow in the lower left corner, and the lemur has hardly any red at all and is almost entirely either yellow to purple. These figures are for single samples from each host, but the trend was highly consistent. In other words, the further the host was evolutionarily from humans, the further their resident microbes were evolutionarily from the resident microbes in humans. That is a non-trivial observation! 

So this method of visualizing the content of a bacterial community is simplistic, but it's also rich and highly informative. I put together a library for making these graphics as well as a crude GUI for toying with them and interacting. I thought it would be cute to name this techinique after everybody's least favorite data-viz, so we called it a PICAN-PI graph ("Population-level Identity, Composition and Novelty via Phylogenetic Insertion"). 

Visualization Library: [https://github.com/MGNute/pican_pi](https://github.com/MGNute/pican_pi)

GUI: [https://github.com/MGNute/Phylostrator](https://github.com/MGNute/Phylostrator)