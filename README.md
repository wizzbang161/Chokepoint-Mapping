# Chokepoint Mapping

## What is Chokepoint Mapping?
Chokepoint Mapping is a methodology, primarily devised to assist Threat Hunters, that enalables prioritizing your work by the amount of pain it inflicts upon an adversary's attack path. To accomplish this, Chokepoint Mapping uses two concepts native to Graph Theory: Betweenness Centrality and Eccentricity.

### What problem is this meant to address?
This methodology was created to address one of the biggest challenges Threat Hunters face: volume of potential projects. For Threat Hunters following a TaHiTI Structured Hunt approach, the raw number of potential targets to work on is staggering, intimidating, and almost universally lacking useful context. Chokepoint Mapping assists us by providing a tool to measure, with numbers, the direct impact on the adversary our proactive approach produces and does so in a way that is visually intuitive and easy to digest across the Security heirarchy.

### Betweenness Centrality
Roughly put, _Betweenness Centrality_ measures for us the relative importantance of a given technique for an adversary's attack path. The more variations of an attack path that run through a given node, the higher its score. As Defenders, we want to shut down nodes with the biggest scores and avoid working on nodes with small (or absent) scores.

### Eccentricity
Eccentricity in a directed, A-to-Z style graph measures how far from the end of the graph the node sits. Essentially, the closer the node is to the very last step in an attack path, the lower its number. Given that Defenders want to shut down attacks as early as possible, we want to focus on nodes that score high.
