# Chokepoint Mapping

## What is Chokepoint Mapping?
Chokepoint Mapping is a methodology, primarily devised to assist Threat Hunters, that enalables prioritizing your work by the amount of pain it inflicts upon an adversary's attack path. To accomplish this, Chokepoint Mapping uses two concepts native to Graph Theory: Betweenness Centrality and Eccentricity.

### What problem is this meant to address?
This methodology was created to address one of the biggest challenges Threat Hunters face: volume of potential projects. For Threat Hunters following a TaHiTI Structured Hunt approach, the raw number of potential targets to work on is staggering, intimidating, and almost universally lacking useful context. Chokepoint Mapping is intended to asist us by solving this problem while meeting 3 core criteria.

#### How we should judge this as a solution to the problem
Chokepoint Mapping was developed (is developing?) to meet 3 criteria:
1. It should show Threat Hunting's unique value as a proactive discipline focused on impacting the adversary rather than bolstering the network.
2. It should measure #1.
3. It should be intuitive and easily understood regardless of technical background.

## What does it need to work?

Chokepoint Mapping needs a few things to function:
1. A graph database or link analysis tool that supports directionality. [Gephi](https://gephi.org/) is a great free tool to start with!
2. Intelligence that arranges attacks in chronological order.
3. Basic data science capability (Betweenness Centrality and Eccentricity, specifically).

### A Graph Database
Graph databases or link analysis tools will allow us to actually paint a picture of attack patterns. [Gephi](https://gephi.org/) is a very accessible starting point, as it is free, runs locally, and is easily GUI-operated. It also possesses the data science capability that we require elsewhere in this methodology.

### Chronological Intelligence Reporting

This is straight forward: we want reporting that is explicitly chronological. This can be narratively chronological (First, then, etc.) or graphically chronological (flow charts). If reporting does not specify what order actions take place in, unless you can infer with 100% accuracy you should avoid using that data with this technique. It's still useful to us as defenders, but we need that context to be confident in our model!

It is _critical_ that we do not give the adversary the benefit of the doubt. You will be tempted to think "well, in order to go from A to C, they likely did B". Don't insert hypothetical nodes, even if the guesses are _very_ educated. That line of thought leads us right back to where we started: the entire MITRE ATT&CK model. Further, the intent of this methodology isn't to create a perfect one-to-one representation of reality; the purpose is to help us avoid low-impact projects.

A good example of reporting that has a chronological structure would be [this DFIR report](https://thedfirreport.com/2025/01/27/cobalt-strike-and-a-pair-of-socks-lead-to-lockbit-ransomware/). This specific example is what has been used to build the example materials in this repo.

### Data Science Capability

#### Betweenness Centrality
Roughly put, _Betweenness Centrality_ measures for us the relative importantance of a given technique for an adversary's attack path. The more variations of an attack path that run through a given node, the higher its score. As Defenders, we want to shut down nodes with the biggest scores and avoid working on nodes with small (or absent) scores.

#### Eccentricity
Eccentricity in a directed, A-to-Z style graph measures how far from the end of the graph the node sits. Essentially, the closer the node is to the very last step in an attack path, the lower its number. Given that Defenders want to shut down attacks as early as possible, we want to focus on nodes that score high.
