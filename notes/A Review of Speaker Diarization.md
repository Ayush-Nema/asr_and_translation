#Paper 
# A Review of Speaker Diarization: Recent Advances with Deep Learning

**Paper source link**: https://arxiv.org/pdf/2101.09624.pdf
**Authors**: Tae Jin Park, Naoyuki Kanda, Dimitrios Dimitriadis, Kyu J. Han, Shinji Watanabe, Shrikanth Narayanan

## Abstract
_Speaker diarization is a task to label audio or video recordings with classes that correspond to speaker identity, or in short, a task to identify "who spoke when". In the early years, speaker diarization algorithms were developed for speech recognition on multispeaker audio recordings to enable speaker adaptive processing. These algorithms also gained their own value as a standalone application over time to provide speaker-specific metainformation for downstream tasks such as audio retrieval. More recently, with the emergence of deep learning technology, which has driven revolutionary changes in research and practices across speech application domains, rapid advancements have been made for speaker diarization. In this paper, we review not only the historical development of speaker diarization technology but also the recent advancements in neural speaker diarization approaches. Furthermore, we discuss how speaker diarization systems have been integrated with speech recognition applications and how the recent surge of deep learning is leading the way of jointly modeling these two components to be complementary to each other. By considering such exciting technical trends, we believe that this paper is a valuable contribution to the community to provide a survey work by consolidating the recent developments with neural methods and thus facilitating further progress toward a more efficient speaker diarization._

## 1. Introduction
- “Diarize” means making a note or keeping an event in a diary. Speaker diarization, like keeping a record of events in such a diary, addresses the question of “who spoke when” by logging speaker-specific salient events on multiparticipant (or multispeaker) audio data.
- Throughout the diarization process, the audio data would be divided and clustered into groups of speech segments with the same speaker identity/label. As a result, salient events, such as non-speech/speech transition or speaker turn changes, are automatically detected.
- Traditional SD system:
![[traditional SD system.png]]
- Preprocessing: To mitigate any artifacts in acoustic environments, various front-end processing techniques, for example, ==speech enhancement==, ==dereverberation==, ==speech separation== or ==target speaker extraction==, are employed.
- Voice or speech activity detection (SAD) is then applied to separate speech from non-speech events. Raw speech signals in the selected speech portion are transformed to acoustic features or embedding vectors. In the clustering stage, the transformed speech portions are grouped and labeled by speaker classes and in the post-processing stage, the clustering results are further refined. Each of these sub-modules is optimized individually in general.

### 1.1 Historical Development of Speaker Diarization
- In 1990s (early years of SD tech):
	- research obj → benefit ASR on Air Traffic Control & broadcast news recording by eparating each speaker’s speech segments and enabling speaker-adaptive training of acoustic models
	- techniques→clustering viz. Generized likelihood ratio (GLR) and Bayesian information criterion (BIC) + some fundamental approaches for measuring the distance b/w speech segments for speaker change detection.
- In early 2000s:
	- Development of new algorithms like: beamforming, information bottleneck clustering (IBC), variational Bayesian (VB) approaches, joint factor analysis (JFA).
- Speaker specific representation in a total variability space derived from simplified JFA, known as ==i-vector==, →found great success in SR and was quickly adopted by SD systems as feature representation for short speech segments, segmented in an unsupervised fashion. 
	- i-Vector successfully replaced its predecessors such as →
		- merely mel-frequency cepstral coefficient (MFCC) or speaker factors (or eigenvoices) to bolster clustering performance in speaker diarization, being combined with principal component analysis (PCA)
		- variational Bayesian Gaussian mixture model (VB-GMM)
		- mean shift and probabilistic linear discriminant analysis (PLDA)
- 2010s → advent of Neural networks
	- extraction of  the speaker embeddings using NN, such as the d-vectors or the x-vectors, which most often are embedding vector representations based on the bottleneck layer output of a DNN trained for SR.
	- The shift from i-vector to these neural embeddings contributed to enhanced performance, easier training with more data, and robustness against speaker variability and acoustic conditions.
- More recently, end-to-end neural diarization (EEND) where individual sub-modules in the traditional speaker diarization systems (Fig. 1) can be replaced by one NN gets more attention with promising results.


### 1.2 Motivation
Till now, there are 2 well-rounded overview papers that describe the development of SD→
- [An overview of automatic speaker diarization systems](https://ieeexplore.ieee.org/document/1677976)
	- various SD systems and their subtasks in the context of broadcast news and CTS data are reviewed up to till mid 2000s.
	- historical progress of SD development in the 1990s and early 2000s are covered
- [Speaker Diarization: A Review of Recent Research](https://ieeexplore.ieee.org/document/6135543)
	- focus was to put more on SD for meeting speech and its respective challenges.
	- weighs more in the corresponding technologies to mitigate problems from the perspective of meeting environments, where there are usually more participants than broadcast news or CTS data and multi-modal data is frequently available.

This paper: Since these two papers were published, SD systems have gone through a lot of notable changes, especially from the leap-frog advancements in DL approaches  
addressing technical challenges across multiple ML domains. → thus this paper curates recents advances.


### 1.3 Overview and Taxonomy of Speaker Diarization
Relevant literatures are categorized into 4 category (based on 2 criteria):
1. whether the model is trained based on speaker diarization-oriented objective function or not. Any trainable approaches to optimize models in a multispeaker situation and learn relations between speakers are categorized into the “diarization objective” class.
2. whether multiple modules are jointly optimized toward some objective function. If a single sub-module is replaced into a trainable one, such method is categorized into the  “Single-module optimization” class. Conversely, joint modeling of segmentation and clustering, joint modeling of  speech separation and SD or fully end-to-end neural diarization system is categorized into the  “Joint optimization” class.

| -                          | Trained based on non-diarization obj | trained based on diarization obj |     |     |
| -------------------------- | ------------------------------------ | -------------------------------- | --- | --- |
| single module optimization | -                                    | -                                |     |     |
| joint optimization         | -                                    | -                                 |     |     |

### 1.4 Diarization evaluation metrics
#### 1.4.1. Diarization Error Rate
The accuracy of speaker diarization system is measured using ==diarization error rate (DER)== where DER is the sum of three different error types: False alarm (FA) of speech, missed detection of speech and confusion between speaker labels.  
$$DER = \frac{(FA+missed+speaker\ confusion)}{total\ duration\ of\ time}$$

#### 1.4.2. Jaccard Error Rate
The goal of JER is to evaluate each speaker with equal weight. Unlike DER which is estimated for the whole utterance altogether, per-speaker error rates are first computed and then averaged to compute JER. Specifically, JER is computed as follows.
$$JER= \frac{1}{N}\sum_i^{N_{ref}}\frac{FA_i + MISS_i}{TOTAL_i}$$
$TOTAL_i$ is the union of the $i^{th}$ speaker’s speaking time in the reference transcript and the $i^{th}$ speaker’s speaking time in the hypotheses. $N_{ref}$ is the number of speakers in the reference script. Note that the Speaker-Confusion in DER is reflected in the part of $FA_i$ in the calculation of JER. Since JER is using union operation between reference and the hypotheses, JER never exceeds 100%, whereas DER can become much larger than 100%. DER and JER are highly correlated but if a subset of speakers are dominant in the given audio recording, JER tends to be higher than the ordinary case.

#### 1.4.3. Word-level Diarization Error Rate
While DER is based on the duration of the speaking time of each speaker, word-level DER (WDER) is designed to measure the error that is caused in the lexical (output transcription) side. The motivation of WDER is the discrepancy between DER and the accuracy of the final transcript output since DER relies on the duration of the speaking time that is not always aligned with the word boundaries.


## 2. Modular Speaker Diarization Systems
- overview of algorithms for speaker diarization belonging to the “Non-diarization objective” class
- each subsection in this section corresponds to the explanation of each module in the _traditional_ SD system

```ad-important
Skipping Section-II to catchup with latest trends in SD tech.
```


## 3. Recent advances in SD using DL
![[advances in SD.png]]

- Section 3.1 →methods that incorporate DL into a single component of SD such as clustering or post-processing
- Section 3.2 →methods that unify several components of SD into a single NN

### 3.1 Single-module optimization
#### 3.1.1 Speaker clustering enhanced by DL
`skipped`
#### 3.1.2 Learning the distance estimator
`skipped`
#### 3.1.3 Post processing based on DL
`skipped`

### 3.2 Joint optimization for SD
#### 3.2.1 Joint segmentation and clustering
`skipped`
#### 3.2.2 Joint segmentation, embedding extraction, and re-segmentation
`skipped`
#### 3.2.3 Joint speech separation and diarization
`skipped`
#### 3.2.4 Fully end-to-end diarization
- EEND ≡End-to-end neural diarization
	- performs all SD procedures based on a single NN
- ![[EEND.png | 420]]
- Input:
	- T-length sequence of acoustic features (i.e. log Mel-filterbank)
	- $X=(x_t \in \mathbb{R}^f | k=1,...,T)$
- Output:
	- Corresponding speaker label sequence
		- $Y = (y_t | t= 1,...,T)$ where $y_t = [y_{t,k} \in {0, 1} | k=1,..., K]$ 
		- $y_{t,k}=1$ represents the speech activity of the speaker $k$ at the time frame $t$
		- $K$ is the max number of speakers that the NN can output
- $y_{t,k}$ and $y_{t,k'}$ can both 1 for different speakers $k$ and $k'$, indicating overlapping speech scenaio
- NN is trained to maximize $log\ P(Y|X) \sim \sum_t \sum_k log\ P(y_{y,k}|X)$ over the training data by assuming the conditional independence of the output $y_{t,k}$.
	- Because there can be multiple candidates of the reference label $Y$ by swapping the speaker index $k$, the loss function is calculated for all possible reference labels and the ref label that has the min loss os used for the error back-propagation, which is inspired by the permutation free obj used in speech separation.

- Advantages of EEND:
	- can handle overlapping speech
	- network is directly optimized toward the maximization of diarization accuracy, by which we can expect a high accuracy
	- can be retrained by a real data (i.e. not synthetic data) just by feeding a ref diarization label while it is often not straighforward for prior works.
- Limitations:
	- model architecture limits the max no of speakers that the model can cope with
	- consist of either Bi-LSTM or self-attention based NNs, making it difficult to do online processing
	- empirically suggested that EEND tend to overfit to the distribution of the training data.


- ==Improvements to deal with multiple speakers:==
	- [Horiguchi et al.](https://arxiv.org/abs/2005.09921) (2020) proposed technique with encoder-decoder-based attractor (EDA)
		- applies an LSTM-based encoder-decoder on the output of EEND to generate multiple attractors.
		- attractors are generated until the attractor existing probability becomes less than the threshold.
		- Then, each attractor is multiplied by the embeddings generated from EEND to calculate the speech activity for each speaker.
		- EEND-EDA was evaluated on `CALLHOME` (2-6 speakers) and `DIHARD 2` (2-9 speakers) dataset and showed better performance than the clustering-based baseline system. #Datasets
	- [Fujita et al.](https://arxiv.org/abs/2006.01796) (2020) proposed approach to output the speech activity one after another by using a conditional speaker chain rule.
		- NN is trained to produce a posterior probability $P(y_k|y_1, ...,y_{k-1}, X)$, where $y_k = (y_{t,k} \in {0,1}| t=1,..., T)$ is the speech activity of the $k^{th}$ speaker.
		- Then, the joint speech activity prob of all speakers can be estimated from the following speaker-wise conditional chain rule as: $P(y_1, ...,y_K|X) =  \prod_{k=1}^{K} P(y_k|y1,...,y_{k-1}, X)$
		- During inference, the NN is repeatedly applied until the speech activity $y_k$ for the last estimated speaker approaches zero.
	- [Kinoshita et al.](https://ieeexplore.ieee.org/document/9414333) (2021) proposed a different approach that combines EEND and speaker clustering. 
		- NN is trained to generate speaker embeddings and the speech activity probability. 
		- Speaker clustering constrained by the estimated speech activity by EEND is applied to align the estimated speakers among the different processing blocks.




Legends:
| Short hand | Full form                    |
| ---------- | ---------------------------- |
| SD         | Speaker Diarization          |
| ASR        | Automatic Speech Recognition |
| JFA        | Joint Factor Analysis        |
| SR         | Speaker recognition          |
| NN         | Neural networks              |
| DNN        | Deep Neural network          |
| DL         | Deep learning                |
| RT         | Rich transcription           |
| SAD        | Speech Activity Detection    |
|            |                              |
