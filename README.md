# Chokepoint Mapping
<br />
<br />
<br />
<br />
<br />
<br />
<img width="1091" alt="image" src="https://github.com/user-attachments/assets/c496e25c-e569-4d60-b1e2-5c8c14929ea9" />


## What is Chokepoint Mapping?
Chokepoint Mapping is a methodology, primarily devised to assist Threat Hunters, that enables prioritizing your work by the amount of pain it inflicts upon an adversary's attack path. To accomplish this, Chokepoint Mapping uses two concepts native to Graph Theory: Betweenness Centrality and Eccentricity.

### What problem is this meant to address?
This methodology was created to address one of the biggest challenges Threat Hunters face: volume of potential projects. For Threat Hunters following a TaHiTI Structured Hunt approach, the raw number of potential targets to work on is staggering, intimidating, and almost universally lacking useful context. Chokepoint Mapping is intended to asist us by solving this problem while meeting 3 core criteria.

#### How we should judge this as a solution to the problem
Chokepoint Mapping was developed (is developing?) to meet 3 criteria:
1. It should show Threat Hunting's unique value as a proactive discipline focused on impacting the adversary rather than bolstering the network.
2. It should measure #1.
3. It should be intuitive and easily understood regardless of technical background.
<br />
<br />
<br />
<br />
<br />
<br />

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
<br />
<br />
<br />
<br />
<br />
<br />


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
<br />
<br />
<br />
<br />
<br />
<br />


## How do I use it once it's built?
First and foremost, remember two things about this methodology:
1. This is not intended to be a 1:1 representation of reality.
2. This is intended to _avoid the worst project_ not necessarily identify the _best_ projects.

### Use those data science functions!
Now that the graph is built, run those data science functions to populate both _betweenness centrality_ and _eccentricity_ values for each node. Once that is done, feel free to attack the problem in any way you wish. Seeing as the graph database comes with the added benefit of always being visually intuitive, I'd recommend the following:

![image](https://github.com/user-attachments/assets/7f35150f-f136-4bb3-a0ff-47b823d342d2)

#### The Red X
In the above example, use whatever method you prefer (excel ranking of raw metrics, overlaying metrics on the graph, heat mapping the metrics, just eyeballing it) to roughly rank each node. Nodes similar to the red 'X' are very undesireable, as they are completely optional for an adversary, as the attack path can succeed without them. Even if they are close to the beginning of the attack path, the low betweenness value (0) immediately ranks this type of work as lowest priority.

#### The Yellow Square
This is a slightly more desireable project, as it has a high betweenness value. Unfortunately, it's _very_ late in the attack path. The adversary would likely be well on their way to success by this point! This is still better than the Red X as removing it disables progression until the adversary can develop a workaround.

#### The Green Circle
This is the platonic ideal of what good work should look like: It is near the beginning of the attack path (high eccentricity) and also possesses a good betweenness value. Put simply: it would be very painful for an adversary to lose because of it's mandatory nature and how close to the beginning it is.
<br />
<br />
<br />
<br />
<br />
<br />


## Show Your Work with Metrics!
As this methodology produces actual metrics, we can use these to illustrate exactly how impactful our efforts are.

### Why are the numbers so important?
Because they fulfill the second of our criteria: they _measure_ Hunt's unique contribution to security. A useful way of thinking about this is thinking of the _betweenness centrality_ value as a number provided by the adversary about how valuable that technique is to them. As we give the adversary no benefit of the doubt, we can say that every single technique on a map is there explicitly because an adversary chose that technique as the best means of accomplishing their task. With this methodology, we take them at their word and are able to assign a number to that value judgment. Having that, we can very directly align ourselves with how much pain we cause the adversary.

Here are a few ideas:

<img width="556" alt="image" src="https://github.com/user-attachments/assets/82a257f2-9594-433a-8ff4-c0a1f28fb967" />

This graph shows the distrubution of the _betweenness centrality_ value for each node in our example graph. The average of all of these values is represented by the yellow dot. The average value for all of the nodes that were _hunted_ are in green.

The value of this graph can come from several angles:
1. Our efforts have ~60% more impact on the adversary compared to choosing them purely through subjective means. If we had picked our projects blindly, or based on what was in the news or what our CISO heard from their friend, our projects would likely have been _measurably less painful for the adversary_. This will often be a significant value, based on experience, sometimes more than a 100% improvement!
2. It demonstrates how well postured we are against this adversary in terms of defense-in-depth. It's possible that a very predictable campaign that is already heavily defended against results in hunts that _appear_ less valuable than average, but that will be influenced by how thorough your defense-in-depth already is.

<img width="563" alt="image" src="https://github.com/user-attachments/assets/e96a940e-9075-40cd-a035-ca6aa20b8dbc" />

This graphs shows the distribution of the _eccentricity_ value for each node in our example graph. While not as immediately useful as the first example, over time it will tell us more valuable information. Specifically, it is often very noticeable how a 'center of gravity' tends to exist in each campaign. In our exmaple, it appears around an eccentricity of 10. This means that your own average will likely hover right around the same value as there are simply more choices at that distance from the end of the attack path.
