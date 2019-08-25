# NMN and friends

Selected papers on modular neural nets for compositional reasoning.

## Framework

Neural Module Networks. [NMN]

Andreas, Rohrbach, Darrell and Klein. CVPR 16.

http://arxiv.org/abs/1511.02799

Introduces the general module net framework. Neural module networks are
input-specific neural networks built from a discrete library of shallow neural
"modules" that are jointly trained to be freely composable (possibly subject to
type constraints). In question answering settings, we can think of these
question-specific networks as roughly corresponding to formal meaning
representations, and use ideas from model-theoretic semantics to suggest the
relevant primitives and types. But at a high-level this framework is just about
building lots of different network layouts, tying pieces of their parameters
together in complex structured ways, and training everything jointly. We know
from Socher-style tree RNNs that this works for scoring and representing
sentences; what's new with NMNs is that these networks induce not just
representations but _functions_, which compose and generalize for
transformations between real-world inputs and outputs.

W/r/t this paper, the good: then-SOTA on VQA. Maybe more interestingly, SHAPES
experiments show that it's possible to train on simple questions and evaluate on
complex ones without loss in performance; in other words, we can turn the
ability to generalize about _syntax_ immediately into the ability to generalize
about _vision_. As far as I know these experiments haven't been repeated with
any more recent versions of the framework. The bad: the set of delexicalized
high-level structures we use for real vision tasks is tiny. Though VQA results
were better than the baseline, they were just barely so, and in hindsight
probably for reasons unrelated to the NMN part.

---

## Question answering / layout prediction

Learning to Compose Neural Networks for Question Answering.

Andreas, Rohrbach, Darrell and Klein, NAACL 16.

http://arxiv.org/abs/1601.01705

The above, repackaged for the semantic parsing crowd. Goes into a little more
detail about connections to formal representations of meaning in linguistics;
what's new technically is the use of a policy gradient method to learn a little
bit of structure scoring, and empirically is the extension of these techniques
to database-style QA problems. RL helps a tiny bit on VQA. As before, the real
story is in the small-scale experiments: on geography QA, it actually makes a
difference in learning whether to insert quantifiers and what branches to prune
from prospective network layouts.

---

Inferring and Executing Programs for Visual Reasoning. [PG+EE]

Johnson, Hariharan, van der Maaten, Hoffman, Fei-Fei, Zitnick and Girshick. ICCV
17.

https://arxiv.org/abs/1705.03633

This paper begins by observing (correctly) that the original NMN work has a
weird hacky dependence on a syntactic parser because we don't actually get to
observe logical forms in language; it then "fixes" the problem by running only
on fake datasets where we have ground truth LFs. The paper bills learning the
layout predictor with a seq2seq model as one of the main contributions, but
insofar as most modern parsers are basically seq2seq models this isn't actually
any different from starting with a pretrained parser that gets run offline. In
this respect there's basically no difference between this and the SHAPES
experiments in the original CVPR paper.  What is new & cool here is the module
parameterization, which is convolutional but not attentional---there's much less
structure imposed on the latent states in this model than the earlier work, and
the fact that things still compose nicely is significant. But it's a lot of free
parameters for a small vocabulary, and I'm pretty sure it would be impossible to
train this model on any real-world dataset.

---

Learning to Reason: End-to-End Module Networks for Visual Question Answering.
[N2NMN]

Hu, Andreas, Rohrbach, Darrell and Saenko. ICCV 17.

https://arxiv.org/abs/1704.05526

Also tries to relax the tight coupling between the parser and the layout
predictor, but with more of an eye towards performance on real-world data. In
particular, replaces hard assignment of words to modules with a soft, attentive
selection, and uses parse trees only for coarse constituency structure. This
finally makes it possible to use much deeper trees than in the original work.
Much better performance than the earlier work; worse than other things on CLEVR
but mostly because of a weird parameterization of the Transform module (which
acts only on spatial attentions without looking at the underlying visual
features). As a bonus, adds learned structure prediction with RL at the action
level rather than the tree level; this helps a tiny bit on CLEVR but not on real
data.

---

Neural Compositional Denotational Semantics for Question Answering

Gupta and Lewis. EMNLP 2018.

https://arxiv.org/abs/1808.09942

Totally different way to fdoing structure discovery from scratch: builds a
complete parse-chart-shaped NN according to the CKY recurrence (sort of like Le
and Zuidema, EMNLP 18), with soft gating to pick which descendants actually get
used. Works through the kinds of types and operators you need to assign an NMN
to every span of the input question (requires a few more pieces than just the
attentions &larr; attentions and attentions &larr; truth values). Can solve
structure discovery from scratch on symbolic world representations with either
fake language (CLEVR) or real (GENx), even when the test set features more
complex structures than the training set. No experiments on more complex
groundings.


---

## Localization / energy-based view

Modeling Relationships in Referential Expressions with Compositional Modular
Networks. [CMN]

Hu, Rohrbach, Andreas, Darrell and Saenko. CVPR 17.

https://arxiv.org/abs/1611.09978

There's an (informal) duality in formal semantics between variable-binding /
$$\lambda$$-calculus-based meaning representations (like was used in most of the
original CCG semantic parsing work) and variable-free / combinator-logic-based
MRs (like was used in Percy Liang's DCS work). Roughly (setting aside messiness
around quantifiers) we think of $$\lambda$$-calculus forms as functions from
worlds and variable bindings to truth values, and combinator forms as functions
from worlds directly to variable bindings.

Translating logical forms into NMNs, this distinction corresponds directly to
the distinction between energy-based models and ordinary feedforward networks.
While most of the NMN work is in the feedforward framework, the CMN paper
(though it's not presented this way) is essentially the energy-based version: we
resolve referring expressions by optimizing over input groundings to maximize a
score at the output.

---

Using Syntax to Ground Referring Expressions in Natural Images.

Cirik, Berg-Kirkpatrick and Morency. AAAI 18.

https://www.cs.cmu.edu/~vcirik/assets/pdf/AAAI2018_cirik_using.pdf

Referring expression grounding, but in feed-forward mode. Gets basically the
same results as the CMN paper, but identifies all the other referents in the
image more accurately. Also improves dramatically on the hacky
parse-tree-to-NMN-layout heuristic from the original NMN paper.

---

MAttNet: Modular Attention Network for Referring Expression Comprehension

Yu, Lin, Shen, Yang, Lu, Bansal and Berg. CVPR 18.

https://arxiv.org/abs/1801.08186

Quite similar to CMN paper; the main distinction here is that pieces of the
predicate structure can themselves be weighted (which basically corresponds to a
soft selection of all the combinatorially many substructures that can be formed
from the fixed initial layout).

---

## Generalization and interpretability

Transparency by Design: Closing the Gap Between Performance and Interpretability
in Visual Reasoning. [TbD]

Mascharka, Tran, Solaski and Majumdar. CVPR 18.

https://arxiv.org/abs/1803.05268

Improved performance on CLEVR using attentional modules similar to those used in
the N2NMN paper. (But hard, supervised assignment of words to structure pieces
as in PG+EE.) A little over-engineered around CLEVR, but a nice discussion of
how the transparency / debuggability of the NMN framework makes it easy to
iterate on modeling and learning experiments.

---

Systematic Generalization: What is Required and Can It Be Learned?

Bahdanau et al. ICLR 19.

https://arxiv.org/abs/1811.12889

Experiments on synthetic data with NMNs and other VQA architectures about who's
robust to shifts between the training and test distributions. Looking only at
questions of the form $\exists xy. f_1(x) \land r(x, y) \land f_2(y)$, ask: what
happens if $y$s are sparse in training (for a given $x$) and $p(y|x)$ shifts for
the test set? Breaks MAC, RelNet, etc., but NMNs are robust. Interestingly, NMN
structure discovery methods (like N2NMN with RL) don't work terribly well, but
fixing a structure and leaving the attentions latent does a good job (suggesting
that something like a MAC hybrid with a fixed structure might be worth exploring
further).

There's something that feels slightly wrong about the evaluation that's I can't
quite pin down, but related to the fact that we're looking at robustness to
shifts in $p(y|x)$ rather than $p(x, y)$ more generally.

---

## Other applications

Modular Generative Adversarial Networks.

Zhao, Chang, Jie and Sigal.

https://arxiv.org/abs/1804.03343

Module nets for generative modeling. Implements a conditional GAN as an encoder,
a sequence of modular transformations on the latent code, and a decoder. The
encoded representation preserves spatial information, so we can use an attention
mechanism like the one in the original question answering papers to localize the
transformation to particular regions of the input image. Synthesized images look
comparable to SOTA; numerical results are better but I don't really know how
GAN evaluations are supposed to work. In principle you should be able to use
this to learn sequences of transformations that don't commute, but all the
experiments in the paper are just adding / removing attributes.

---

Neural modular control for embodied question answering.

Das, Gkioxari, Lee, Parikh and Batra. CoRL 18.

https://arxiv.org/abs/1810.11181

An options model with
[policy-sketch-style supervision](https://arxiv.org/abs/1611.01796) 
and modular option parameterizations (which factor out verb--argument
combinations). Every option is a "flat" function indexed by an embedding that
takes the current world state onto a next action, but could presumably
incorporate more internal structure by expressing `go-to[sofa]` with a VQA-style
`find` module feeding into a controller. Modules are trained with subgoal
supervision.

---

Visual coreference resolution in visual dialogue using neural module networks.

Kottur, Moura, Parikh, Batra and Rohrbach. ECCV 2018.

https://arxiv.org/abs/1809.01816

N2NMN-style training with supervised attentions and a richer set of primitives
to support visual dialogue. A `refer` module can grab bounding boxes around
regions that have previously been attended to. Handles questions like "what else
is red" by combining this with negation and primitives from previous work. Does
a pretty good job with off-the-shelf parser layouts, getting then-SOTA results
on the visual dialogue challenge.
