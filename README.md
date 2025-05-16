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

## What do I need to keep in mind as I build it?

#### Never give the Adversary the benefit of the doubt
Making assumptions, such as "well in order to go from A to C, they probably used B" inevitably leads back to wrestling with the entirety of the MITRE ATT&CK model.

#### Make sure the graph is directed.
You're representing things that follow a chronological order and obey the laws of causality. A _leads to_ B.

#### Use MITRE ATT&CK technique IDs as the primary nodes you intend to map.
Other types of information can be usefully represented as nodes in a graph database, such as MITRE ATT&CK Tactics, but attack paths are composed of techniques. These techniques are our basic building blocks. It is also an almost universal language, so it makes the graph more useful to others!

#### Ask these two questions as you populate each node:
1. Is this explicitly stated to have been used by the adversary?
2. Is this technique required to succeed for the adversary to reach the end of the attack path?

### Considerations for associating your nodes

#### Multiple necessary techniques occuring simultaneously due to the same action.
For example: An initial payload script that Discovers local admin accounts, disables key defenses, and calls back for the second stage payload may appear tempting to represent by connecting each technique directly to the original script's execution node. This, however, is interpreted by our math as each technique being _independent_ of each other in reaching the final step in the attack path. As these are all actually _dependent_ upon each other, it is preferred to connect them all in a line, with only one node directly connected to the original script. Example below:
![image](https://github.com/user-attachments/assets/503501c0-c9b5-46a2-8020-ecfd4bfd2119)

#### Optional Branches
Not all techniques are required to succeed for the adversary to move from Initial Access to Impact. When this is the case, you'll want to branch that optional technique away from the main line of activity where it terminates by itself. Whenever this is the case, you will break one of the cardinal rules mentioned earlier and ensure that every optional branch node has a directional relationship going back to the main line. This will ensure a more accurate rating for Eccentricity. Example below:
![image](https://github.com/user-attachments/assets/dabf6042-bd95-4197-808b-b0b54cf74064)
