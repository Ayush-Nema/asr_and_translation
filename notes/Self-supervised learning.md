Quick notes on article by ==Meta AI Research==
Source article: [Click here](https://ai.facebook.com/blog/self-supervised-learning-the-dark-matter-of-intelligence/)
Article title: ==Self-supervised learning: The dark matter of intelligence==

### Pitfalls of Supervised learning (SL)
- There’s a limit to how far the field of AI can go with SL alone.
- SL is bottleneck for building more intelligent _generalist_ models that can do multiple tasks and acquire new skills _without_ massive amounts of labeled data.
- Especially hard for tasks requiring labelled data like training translation systems for low-resource languages.
- It will be useful if AI systems can glean a deeper, more nuanced understanding of reality beyond what’s specified in the training data set.


### Common sense as the dark matter
-  Common sense helps people learn new skills without requiring massive amounts of teaching for every single task.
- Common sense ability is taken for granted in humans and animals, but has remained an open challenge in AI research since its inception. In a way, common sense is the dark matter of artificial intelligence.
- The short answer is that humans rely on their previously acquired background knowledge of how the world works. 
	- Open question: How do we get machines to harness the power of common sense?


``` ad-important
_We believe that self-supervised learning (SSL) is one of the most promising ways to build such background knowledge and approximate a form of common sense in AI systems._
```


###  SSL paradigm
- SSL enables AI systems to learn from orders of magnitude more data, which is important to recognize & understand patterns of more subtle, less common representations of the world.
- SSL has long had great success in advancing the field of NLP, including the [Collobert-Weston 2008 model](https://ronan.collobert.com/pub/matos/2008_nlp_icml.pdf), [Word2Vec](https://arxiv.org/pdf/1301.3781.pdf), [GloVE](https://l.facebook.com/l.php?u=https%3A%2F%2Fnlp.stanford.edu%2Fpubs%2Fglove.pdf&h=AT37d2Gyyxx2O42jMmbabcwkifoECSA9LaK4gdMay5Hc4LzSyoPOkT0hzWcdpZTv6fxuDljcnJ1jLVb24kmloVdq6mQfnoZR2C7jbFdlttFLFopQ-BY6Axj6297tPJkSeFLjR1-NCi4p3utjC79DKkqoR0I), [fastText](https://arxiv.org/pdf/1607.01759.pdf), and, more recently, [BERT](https://l.facebook.com/l.php?u=https%3A%2F%2Farxiv.org%2Fpdf%2F1810.04805.pdf&h=AT1VKsr03Ce1CijLOQer8BwSC9tyS9ruVYKAgfcQcRAOTMmLYFcUh3qQkaVM_Ro3ttYD3GcRyGBJVZtUlZa9HlGt51rBkENEOAUkttVQvGJL8GhyD5jACP7YPwA_B8g0eWAGArhY2eZiowAFIEDb1mzWHQs), [RoBERTa](https://l.facebook.com/l.php?u=https%3A%2F%2Farxiv.org%2Fpdf%2F1907.11692.pdf&h=AT1qiQAjTLIwbnMjasPOr7wvx46XqLhILRNCsnX0xNsGm9YfZCi3b84rCLJ2Gg8mpSexE_RZaO4H3yH0n5YYOOZMgJHW97SyeAu1JpTTmeiZ9Dhl_rmjRpmj5HS_R51LVc92UMxavLw5pDd-MZ69kn05rAA), [XLM-R](https://ai.facebook.com/blog/-xlm-r-state-of-the-art-cross-lingual-understanding-through-self-supervision/), and others.
- Systems pretrained this way yield _considerably higher_ performance than when solely trained in a supervised manner.


``` ad-important
### Article overview
- This article → details → why SSL may be helpful in unlocking the dark matter of intelligence — and the next frontier of AI.
- Overview of promonent models:
	- energy-based models for prediction in the presence of uncertainty
	- joint embedding methods
	- latent-variable architectures
```

## SSL is predictive learning
- SSL obtains supervisory signals from the data itself, often leveraging the underlying structure in the data.
- The general technique of SSL is to _predict_ any unobserved or hidden part (or property) of the _input_ from any observed or unhidden part of the _input_.
- Ex's:
	- NLP: Masked-language modelling (MLM) →hide part of a sentence and predict the hidden words from the remaining words.
	- CV: predict past or future frames in a video (hidden data) from current ones (observed data).
- ∵SSL uses the structure of the data itself, it can make use of a variety of supervisory signals across co-occurring modalities (e.g., video and audio) and across large data sets — _all without relying_ on labels.
- ![[SSL_1.png]]
	- In self-supervised learning, the system is trained to predict hidden parts of the input (in gray) from visible parts of the input (in green).

- As a result of the supervisory signals that inform SSL, the term “self-supervised learning” is more accepted than the previously used term “unsupervised learning.” 
	- UL is an ill-defined and misleading term that suggests that the learning uses no supervision at all.
	-  In fact, SSL is not unsupervised, as it _uses far more feedback_ signals than standard supervised and reinforcement learning methods do.

## SSL for _language_ vs _vision_
#### SSL for NLP
- SSL has had a particularly profound impact on NLP, allowing us to train models such as BERT, RoBERTa, XLM-R, and others on _large unlabeled text data sets_ and then use these models for downstream tasks.
- These models are pretrained in a self-supervised phase and then fine-tuned for a particular task, such as classifying the topic of a text.
- Self-supervised pretraining phase:
	- Predicting missing parts of the input is one of the more standard tasks for SSL pretraining.
	- the system is shown a short text (typically 1,000 words) in which some of the words have been masked or replaced.
	- →system is trained to predict the words that were masked or replaced (MLM).
	- →system learns to represent the meaning of the text so that it can do a good job at filling in “correct” words, or those that make sense in the context. It learns to represent the meaning of words, the syntactic role of words, and the meaning of entire texts.
	- Ex:
		- To complete a sentence such as “The (blank) chases the (blank) in the savanna,” the system must learn that lions or cheetahs can chase antelope or wildebeests, but that cats chase mice in the kitchen, not the savanna.

#### SSL for CV
- These techniques, however, can’t be easily extended to new domains, such as CV. Despite promising early results, SSL has not yet brought about the same improvements in CV that we have seen in NLP (though this will change).
- Reason
	- →it is considerably more difficult to represent uncertainty in the prediction for images than it is for words.
	- When the missing word cannot be predicted exactly (is it “lion” or “cheetah”?), the system can associate a score or a probability to all possible words in the vocabulary.
	- Training models at this scale also required a model architecture that was efficient in terms of both runtime and memory, without compromising on accuracy. New innovation called [RegNets](https://arxiv.org/abs/2003.13678) fits perfectly to these needs. RegNet models are ConvNets capable of scaling to billions or potentially even trillions of parameters, and can be optimized to fit different runtime and memory limitations.
	- Summary: 
		- uncertainity in prediction (Major issue)
		- memory and runtime optimization
- But we do not know how to efficiently represent uncertainty when we predict missing frames in a video or missing patches in an image. We cannot list all possible video frames and associate a score to each of them, because there is an infinite number of them.

## Modelling the uncertainity in prediction
![[SSL_2.png|700]]

- Modelling uncertainity in NLP:
	- predicting the missing words involves computing a prediction score for every possible word in the vocabulary. 
	- →While the vocabulary itself is large and predicting a missing word involves some uncertainty, it’s possible to produce a list of all the possible words in the vocabulary together with a probability estimate of the words’ appearance at that location. 
	- Typical ML systems do so by treating the prediction problem as a classification problem and computing scores for each outcome using a giant so-called softmax layer, which transforms raw scores into a probability distribution over words. With this technique, the uncertainty of the prediction is represented by a probability distribution over all possible outcomes, provided that there is a finite number of possible outcomes.
- Modelling uncertainity in CV:
	- the task of predicting “missing” frames in a video, missing patches in an image, or missing segment in a speech signal involves a _prediction of high-dimensional continuous objects rather than discrete outcomes_. 
	- → there are an infinite number of possible video frames that can plausibly follow a given video clip. It is not possible to explicitly represent all the possible video frames and associate a prediction score to them. In fact, we may never have techniques to represent suitable probability distributions over high-dimensional continuous spaces, such as the set of all possible video frames.


## A unified view of self-supervised methods
- There is a way to think about SSL within the unified framework of an ==Energy-based model (EBM)==.
- **EBM**
	- An EBM is a trainable system that, given two inputs, x and y, tells us how _incompatible_ they are with each other.
	- Ex: 
		- x could be a short video clip, and y another proposed video clip. The machine would tell us to what extent y is a good continuation for x.
	- To indicate the incompatibility between x and y, the machine produces a single number, called an _energy_. 
	- If the energy is low, x and y are deemed compatible; if it is high, they are deemed incompatible.
		- $energy$ ⇟ ≡ $(x, y)$ are compatible
		- $energy$ ⇞ ≡ $(x, y)$ are incompatible
	- ![[SSL_3.png | 600]]
		- An EBM measures the compatibility between an observation $x$ and a proposed prediction $y$. If $x$ and $y$ are compatible, the energy is a small number; if they are incompatible, the energy is a larger number.

- Parts of training EBM:
	- showing it examples of $x$ and $y$ that are compatible and training it to produce a low energy. ☞ _Easy_
	- finding a way to ensure that for a particular $x$, the $y$ values that are incompatible with $x$ produce a higher energy than the $y$ values that are compatible with $x$. ☞ _Hard_

- For image recognition, our model takes two images, x and y, as inputs. If x and y are slightly distorted versions of the same image, the model is trained to produce a low energy on its output. 

## Joint embedding, Siamese networks
- A particular well-suited DL architecture for EBM is the so-called _Siamese networks_ or _joint embedding architecture_.
- Joint embedding architechture:
	- composed of two identical (or almost identical) copies of the same network. 
	- One network is fed with x and the other with y. 
	- The networks produce output vectors called _embeddings_, which represent x and y. 
	- A third module, joining the networks at the head, computes the energy as the distance between the two embedding vectors.
- When the model is shown distorted versions of the same image, the parameters of the networks can easily be adjusted so that their outputs move closer together. This will ensure that the network will produce nearly identical representations (or embedding) of an object, regardless of the particular view of that object.
- ![[SSL_4.png|600]]
	- Joint embedding architecture. The function C at the top produces a scalar energy that measures the distance between the representation vectors (embeddings) produced by two identical twin networks sharing the same parameters (w). When x and y are slightly different versions of the same image, the system is trained to produce a low energy, which forces the model to produce similar embedding vectors for the two images. The difficult part is to train the model so that it produces high energy (i.e., different embeddings) for images that are different.

- Challenge:
	- The difficulty is to make sure that the networks produce high energy, i.e. different embedding vectors, when x and y are different images. 
	- Without a specific way to do so, the two networks could happily ignore their inputs and always produce identical output embeddings. This phenomenon is called a _collapse_. When a collapse occurs, the energy is not higher for nonmatching x and y than it is for matching x and y.

There are two categories of techniques to _avoid collapse_: 
- contrastive methods
- regularization methods

### Contrastive energy-based SSL
- Contrastive methods are based on the simple idea of _constructing pairs_ of x and y that are not compatible, and adjusting the parameters of the model so that the corresponding output energy is _large_.
- ![[SSL_5.gif | 500]]
	- Training an EBM with a contrastive method consists in simultaneously pushing down on the energy of compatible (x,y) pairs from the training set, indicated by the blue dots, and pushing up on the energy of well chosen (x,y) pairs that are incompatible, symbolized by the green dots. In this simple example, x and y are both scalars, but in real situations, x and y could be an image or a video with millions of dimensions. Coming up with incompatible pairs that will shape the energy in suitable ways is challenging and expensive computationally.

- The method used to train NLP systems by masking or substituting some input words belongs to the category of contrastive methods.
	- But they don’t use the joint embedding architecture.
	- Instead, they use a predictive architecture in which the model directly produces a prediction for y.

- Steps
	- One starts for a complete segment of text y, then corrupts it, e.g., by masking some words to produce the observation x. 
	- The corrupted input is fed to a large neural network that is trained to reproduce the original text y. 
	- An uncorrupted text will be reconstructed as itself (low reconstruction error), while a corrupted text will be reconstructed as an uncorrupted version of itself (large reconstruction error). 
	- If one interprets the reconstruction error as an energy, it will have the desired property: low energy for “clean” text and higher energy for “corrupted” text.
- The general technique of training a model to restore a corrupted version of an input is called _denoising auto-encoder_.

- ![[SSL_6.png]]
	- A MLM, which is an instance of denoising auto-encoder, itself an instance of contrastive SSL. Variable y is a text segment; x is a version of the text in which some words have been masked. The network is trained to reconstruct the uncorrupted text.

- a predictive architecture of this type can produce only a single prediction for a given input. 
- Since the model must be able to predict multiple possible outcomes, the prediction is not a single set of words but a series of scores for every word in the vocabulary for each missing word location.
- But we cannot use this trick for images because we cannot enumerate all possible images. Is there a solution to this problem? The short answer is no. One interesting avenue is _latent-variable (LV) predictive architectures_.

- ![[SSL_7.png | 650]]
	- A LV predictive architecture. Given an observation x, the model must be able to produce a set of multiple compatible predictions symbolized by an S-shaped ribbon in the diagram. As the latent variable z varies within a set, symbolized by a gray square, the output varies over the set of plausible predictions.

LV predictive models contain an extra input variable (z). It is called latent because its value is never observed. With a properly trained model, as the LV varies over a given set, the output prediction varies over the set of plausible predictions compatible with the input x.
	LV models can be trained with contrastive methods. A good example of this is a generative adversarial network (GAN). The critic (or discriminator) can be seen as computing an energy indicating whether the input y looks good. The generator network is trained to produce contrastive samples to which the critic is trained to associate high energy.

Issues in Contrastive learning
- very inefficient to train.
	- In high-dimensional spaces such as images, there are many ways one image can be different from another. Finding a set of contrastive images that cover all the ways they can differ from a given image is a nearly impossible task.

Major question:
	What if it were possible to make sure the energy of incompatible pairs is higher than that of compatible pairs without explicitly pushing up on the energy of many incompatible pairs?

### Non-contrastive energy-based SSL
- Non-contrastive methods applied to joint embedding architectures is possibly the hottest topic in SSL for vision at the moment. 
- The domain is still largely unexplored, but it seems very promising.

Perhaps a better alternative in the long run will be to devise non-contrastive methods with latent-variable predictive models. The main obstacle is that they require a way to minimize the capacity of the latent variable. The volume of the set over which the latent variable can vary limits the volume of outputs that take low energy. By minimizing this volume, one automatically shapes the energy in the right way.

The challenge of the next few years may be to devise non-contrastive methods for latent-variable energy-based model that successfully produce good representations of image, video, speech, and other signals and yield top performance in downstream supervised tasks without requiring large amounts of labeled data.


#### Legends
| Acronym | Full form                            |
| ------- | ------------------------------------ |
| SL      | Supervised learning                  |
| SSL     | Self-supervised learning             |
| NLP     | Natural Language Processing          |
| UL      | Unsupervised learning                |
| MLM     | Masked Language Modelling (or model) |
| CV      | Computer Vision                      |
| EBM     | Energy-based model                   |
| LV      | Latent-variable                                     |
