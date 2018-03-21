# Language and behavior learning

Learning to follow instructions, interpret goals, model environment dynamics and
select features in interactive environments.

---

Reinforcement Learning for Mapping Instructions to Actions.

Branavan, Chen, Zettlemoyer and Barzilay. ACL 09.

http://people.csail.mit.edu/branavan/papers/acl2009.pdf

The original. Forms a joint MDP over the underlying environment and
accompanying command, with the state space augmented to include the span of the
command currently being processed and the action space augmented to move this
span around. (This is an early version of hard attention!) Good results on a
Windows troubleshooting task (apparently a hellish engineering effort) with
carefully shaped rewards, as well as a block game with sparse rewards (for
which I have a Scala reimplementation in
https://github.com/jacobandreas/align-comp).

---

Learning to Interpret Natural Language Navigation Instructions from
Observations.

Chen and Mooney. AAAI 11.

http://www.cs.utexas.edu/~ml/papers/chen.aaai11.pdf

This is "supervised" in the sense that training commands are accompanied by
demonstrations in the low-level action space. But there are many utterances
with different meanings that might be consistent with a particular sequence of
low-level actions ("go to the chair" vs "go to the red hallway" when the chair
is located in the red hallway---the commands have the same _extension_ but
different _intensions_). Here we resolve the issue with a pipeline approach:
first align words and phrases to actions and observations with which they are
consistent across multiple paths, then use the lexicon to assign a specific
high-level plan to each labeled low-level action sequence, and finally train a
UT-style semantic parser on (command, high-level plan) pairs.

[N.B. People should stop using the SAIL dataset---it's tiny, existing work uses
multiple versions of the data and multiple validation folds, lots of work just
reports cross-validation rather than a held-out test set. See also note on Mei
et al. 11 about evaluation metrics.]

---

Weakly Supervised Learning of Semantic Parsers for Mapping Instructions to
Actions.

Artzi and Zettlemoyer. TACL 13.

http://yoavartzi.com/pub/az-tacl.2013.pdf

As in Chen and Mooney 11, but with a more sophisticated logical language for
describing plans and a proper latent-variable semantic parser &agrave; la Liang
et al 11 (but Luke, so CCG). Can learn from both demonstrations and sparse
reward on successful execution.

---

Listen, Attend, and Walk: Neural Mapping of Navigational Instructions to Action
Sequences.

Mei, Bansal and Walter. AAAI 16.

https://arxiv.org/pdf/1506.04089.pdf

First simple neural instruction follower for low-level actions: learns an
instruction follower with seq2seq+attention and gets pretty good performance.
The dataset is tiny by neural standards, so kind of surprising that this works
at all.

[Results are not actually SOTA, as claimed in the paper---previous work starts
with the agent facing in a fixed absolute direction, but this paper starts with
the agent facing in a fixed direction _relative_ to the correct move, which
makes it trivial to guess the first action. Scores are artificially inflated as
a result.]

---

Unified Pragmatic Models for Generating and Following Instructions

Fried, Andreas and Klein. NAACL 18.

https://arxiv.org/pdf/1711.04987.pdf

Train a Mei et al.-style RNN model as well as an inverse model for generating
instructions given actions. Ensembling these together corresponds to an
implementation of a Rational Speech Acts model (Frank & Goodman 12), and gives
good performance on both instruction following and generation. Not quite as good
as semantic parsers on SAIL, but better on a bunch of other tasks.

---

Grounded Language Learning in a Simulated 3D World

Hermann, Hill, and a multitude.

https://arxiv.org/abs/1706.06551

Obvious: Mei et al without attention, but trained via RL instead of
demonstrations using standard tricks. Fake language, fake environments, but
hard vision; shows that you can scale simple instruction following models up if
you have more compute than God.

---

Understanding Natural Language Commands for Robotic Navigation and Mobile
Manipulation

Tellex, Kollar, Dickerson, Walter, Banerjee, Teller and Roy. AAAI 11.

http://h2r.cs.brown.edu/wp-content/uploads/2015/07/tellex11language.pdf

First example I know of trying to predict a high-level cost function for
consumption by a planner, rather than a low-level sequence of actions.  Here
the structure of a command is used to deterministically generate a
command-specific CRF specifying a cost function that can be used for planning.
The model is initially trained essentially discriminatively, to maximize the
probability of correct groundings given a cost function and a command. These
are predicted on held-out data with high accuracy, but this doesn't get us the
ability to actually follow instructions. So there is also an "end-to-end"
evaluation section in which groundings and cost functions are jointly inferred,
and users asked if the resulting trajectories match the natural language
descriptions. (This evaluation is only performed without any ground truth info
for the 30 most confident test set predictions and works about half the time.)

---

Alignment-Based Compositional Semantics for Instruction Following

Andreas and Klein. EMNLP 15.

https://arxiv.org/pdf/1508.06491.pdf

Similar in spirit to the Tellex et al. 11, but learns from demonstrations
rather than cost functions by putting the low-level action sequence into the
CRF. "Plans" at test time by doing inference over CRF variables.

---

Grounding English Commands to Reward Functions

MacGlashan et al. RSS 15

http://h2r.cs.brown.edu/wp-content/uploads/2015/07/macglashan15grounding.pdf

As in Andreas & Klein 15, but explicitly formulates the learning problem as IRL
and uses a more sophisticated class of MDPs. (Note that IRL is really the same
thing as CRF inference in both the maxent and max-margin formulations.) Toy
experiments.

---

Learning to Win by Reading Manuals in a Monte Carlo Framework

Branavan, Silver and Barzilay. ACL 11.

http://people.csail.mit.edu/branavan/papers/acl2011.pdf

This (2011!) paper is pretty wild: it learns a policy represented as a deep Q
network (!) that scores actions based on the current environment state and soft
attention (!) to dependency parses (!) extracted from a Civ II strategy guide.
The Q network is trained from Monte-Carlo reward estimates rather than normal Q
learning updates. Notably, we're just learning to play one game here; the
"text" is really just a fixed blob of features that doesn't change during
training time. So information-theoretically it shouldn't help at
all---presumably what makes any of this work is that text-overlap features bias
the learner toward particular strategies by correlating moves across turns.

---

Reading to Learn: Constructing Features from Semantic Abstracts

Eisenstein, Clarke, Goldwasser and Roth. EMNLP 09.

http://cogcomp.org/papers/ECGR09.pdf

Approximately as in Branavan et al. 11, aims to provide good high-level
features to a learner by extracting them from textual side-information.
Specifies a generative model of text given logical specifications of high-level
features using a glorious Bayesian nonparametric model. Evaluated on strategy
games by training a classifier to predict legal moves from an extremely small
number of examples.

---

Deep Transfer in Reinforcement Learning by Language Grounding

Narasimhan, Barzilay and Jaakkola

https://arxiv.org/pdf/1708.00133.pdf

Deep RL through a value iteration network, using side-information from text to
predict representations of entities (from which it can be inferred how they
move, what rewards they provide, etc.). Faster learning on the training task,
and fast adaptation to held-out tasks.

---

TODO:

http://h2r.cs.brown.edu/wp-content/uploads/2017/06/arumugam17.pdf
