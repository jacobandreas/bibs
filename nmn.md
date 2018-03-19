# NMN and friends

Dynamically structured neural module networks for compositional reasoning

## Basics

Neural Module Networks ("NMN").

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

## Question answering / structure prediction

---

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

Inferring and Executing Programs for Visual Reasoning ("PG+EE").

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

Learning to Reason: End-to-End Module Networks for Visual Question Answering
("N2NMN").

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

## Referring expression grounding / energy

Modeling Relationships in Referential Expressions with Compositional Modular
Networks ("CMN").

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

## Interpretability

Transparency by Design: Closing the Gap Between Performance and Interpretability
in Visual Reasoning ("TbD").

Mascharka, Tran, Solaski and Majumdar. CVPR 18.

https://arxiv.org/pdf/1803.05268.pdf

Improved performance on CLEVR using attentional modules similar to those used in
the N2NMN paper. (But hard, supervised assignment of words to structure pieces
as in PG+EE.) A little over-engineered around CLEVR, but a nice discussion of
how the transparency / debuggability of the NMN framework makes it easy to
iterate on modeling and learning experiments.
