
#Paper
# `pyAnnote.audio`: NEURAL BUILDING BLOCKS FOR SPEAKER DIARIZATION
- Source: https://arxiv.org/pdf/1911.01255.pdf
- Authors: _Herv ́e Bredin, Ruiqing Yin, Juan Manuel Coria, Gregory Gelly, Pavel Korshunov,  
Marvin Lavechin, Diego Fustes, Hadrien Titeux, Wassim Bouaziz, Marie-Philippe Gill_
- Link: [github.com/pyannote/pyannote-audio](https://github.com/pyannote/pyannote-audio)

## Abstract
_We introduce `pyannote.audio`, an open-source toolkit written in Python for speaker diarization. Based on `PyTorch` machine learning framework, it provides a set of trainable end-to-end neural building blocks that can be combined and jointly optimized to build speaker diarization pipelines. `pyannote.audio` also comes with pre-trained models covering a wide range of domains for voice activity detection, speaker change detection, overlapped speech detection, and speaker embedding – reaching state-of-the-art performance for most of them._

#### Index terms
speaker diarization, voice activity detection, speaker change detection, overlapped speech detection, speaker embedding

## 1. Introduction
- SD is the task of partitioning an audio stream into homogeneous temporal segments a/c to the identity of the speaker.
- this is usually addressed by putting together a collection of building blocks, each tackling a specific task (e.g. voice activity detection, clustering, or re-segmentation)
	- ![[SD system.png]]
	- `pyannote.audio` provides a collection of modules that can be jointly optimized to build a SD pipeline.
- `pyannote.audio` provides end-to-end neural implementations for each of them.

==Other open-source alternatives==
- `S4D` (SIDEKIT for diarization) [^1]
	- provides an educational and efficient toolkit for speaker diarization including the whole chain of treatment
	- written in Python
	- provides most of the aforementioned blocks, and performs the actual evaluation of the system
	- Demerit: 
		- focus on traditional approaches
		- lack of joint optimization of the pipeline
- `Kaldi` [^2]
	- provides few SD recipes but not written in Python
	- mostly dedicated to building speech and speaker recognition system
- `ALIZE` and its `LIA_SpkSeg` extension [^3]
	- written in C++
	- do not provide recent DL approaches for SD
- `pyAudioAnalysis` [^4]
	- written in Python
	- addresses more general audio signal analysis, though can be used for SD


## 2. Feature extraction with built-in data augmentation
- `pyannote.audio` supports training models from the waveform directly (e.g. using SincNet learnable features)
- `pyannote.audio.features` module provides a collection of standard feature extraction techniques such as MFCCs or spectrograms using the implementation available in the librosa library
- They all inherit from the same `FeatureExtraction` base class that supports on-the-fly data augmentation which is very convenient for training NNs.
- Contrary to other tools that generate in advance a fixed number of augmented versions of each original audio file, `pyannote.audio` generates a virtually infinite number of versions as the augmentation is done on-the-fly every time an audio chunk is processed.


## 3. Sequence labeling
- `pyannote.audio.labeling` provides a unified framework to train (usually recurrent) NNs for several SD sub-modules, including voice activity detection[^5], speaker change detection[^6], overlapped speech detection[^7], and even re-segmentation[^8].

### 3.1 Principle
- Each of them can be addressed as a sequence labeling task where the input is the sequence of feature vectors $X = {x_1, x_2, . . . , x_T }$ and the expected output is the corresponding sequence of labels $y = {y_1, y_2, . . . , y_T }$ with $y_t \in [1;K]$; where the number of classes $K$ depends on the task. 
- `pyannote.audio` provides generic code to train a NN $f : X \rightarrow y$ that matches a feature sequence $X$ to the corresponding label sequence $y$. The choice of the actual NN architecture is left to the user, though `pyannote.audio` does provide pre-trained `PyTorch` models sharing the same generic PyanNet base.
- ![[pyannote_Arc.png]]
==Gist==
- Because processing long audio files of variable lengths is neither practical nor efficient, `pyannote.audio` relies on shorter fixed-length sub-sequences. 
	- At training time, fixed-length sub-sequences are drawn randomly from the training set to form mini-batches, increasing training samples variability (data augmentation) and training time (shorter sequences).  
	- At test time, audio files are processed using overlapping sliding windows of the same length as used in training. For each time step $t$, this results in several overlapping sequences of K-dimensional prediction scores, which are averaged to obtain the final score of each class.

### 3.2 Voice activity detection
- Voice activity detection is the task of detecting speech regions in a given audio stream or recording.
- It can be addressed in `pyannote.audio` using the above principle with $K = 2: y_t = 0$ if there is no speech at time step $t$ and $y_t = 1$ if there is.
- At test time, time steps with prediction scores > tunable threshold $\theta_{VAD}$ are marked as speech.
- Overall, this implements a simplified version of the voice activity detector originally described in this paper[^5].

### 3.3 Speaker change detection
- Speaker change detection is the task of detecting speaker change points in a given audio stream or recording. 
- `pyannote.audio` → same sequence labeling principle with $K = 2: y_t = 0$ if there is no speaker change at time step $t$ and $y_t = 1$ if there is.
- To address the class imbalance problem and account for human annotation imprecision, time steps {${t \| t − t^∗| < \delta}$} in the close temporal neighborhood of a speaker change point $t^∗$ are artificially labeled as positive for training.
- In practice, the order of magnitude of $\delta$ is 200ms.
- At test time, time steps corresponding to prediction scores local maxima and greater than a tunable threshold $\theta_{SCD}$ are marked as speaker change points. 
- Overall, this essentially implements a version of the speaker change detector originally described in [^6].

### 3.4 Overlapped speech detection
- Overlapped speech detection is the task of detecting regions where at least two speakers are speaking at the same time.
- `pyannote.audio` → same sequence labeling principle with $K = 2: y_t = 0$ if there is no or one speaker at time step $t$ and $y_t = 1$ if there are two speakers or more.
- To address the class imbalance problem, half of the training sub-sequences are artificially made of the weighted sum of two random sub-sequences, as shown here.  
- ![[pyannote_overlapping speech.png|400]]
- At test time, time steps with prediction scores > tunable threshold $\theta_{OSD}$ are marked as overlapped speech.

### 3.5 Re-segmentation
- Re-segmentation is the task of refining speech turns boundaries and labels coming out of a diarization pipeline.
- Unsupervised task
- `pyannote.audio` → same sequence labeling principle with $K = \kappa +1$ where $\kappa$ is the number of speaker hypothesized by the diarization pipeline: $y_t =0$ if no speaker is active at time step $t$ and $y_t=k$ if speaker $k \in [1;\kappa]$ is active.
- Because the re-segmentation step is unsupervised by design, one cannot pre-train re-segmentation models.
- For each audio file, a new re-segmentation model is trained from scratch using the (automatic, hence imperfect) output of a diarization pipeline as training labels.
	- Once trained for a number of epochs $\epsilon$ (one epoch being one complete pass on the file), the model is applied on the very same file it was trained from – making this approach completely unsupervised.
- Each time step is assigned to the class (non-speech or one of the $\kappa$ speakers) with highest prediction scores. 
- Method is originally described in [^8] where it was found that $\epsilon=20$ is a reasonable number of epochs.
	- This re-segmentation step may be extended to also assign the class with the second highest prediction score to overlapped speech regions[^7].


## 4. Sequence embedding and clustering
- Like voice activity detection, clustering is one of the most important part of any SD pipeline. It consists in grouping speech segments according to the actual identity of the speaker.
- Until Oct 2019:
	- Most best performing SD systems relied on →x-vectors as i/p to clustering step.
	- usually extracted from a fixed-length sliding window, & the pairwise similarity matrix is obtained with PLDA.
- `pyannote.audio` does not uses this approach.
- What's used?
	- the clustering step uses metric learning approaches to train speaker embedding that are directly optimized for a predefined distance (usually cosine)
	- growing collection of such techniques are present in `pyannote.audio.embedding`.
- Like other blocks, speaker embeddings can be trained either from handcrafted features, or from the waveform directly in an end-to-end manner.


## 5. Tunable pipelines
- While each building block has to be trained separately, `pyannote.audio.pipeline` combines them into a SD pipeline whose hyper-parameters are optimized jointly to minimize the diarization error rate (or any other metric available in `pyannote.metrics` [^9]).
- this joint optimization process usually leads to better results than the late combination of multiple building blocks that were tuned independently from each other.


## 6. Reproducible results
`Skipped`


## 7. References
[^1]: Pierre-Alexandre Broux, Florent Desnous, Anthony Larcher, Simon Petitrenaud, Jean Carrive, and Sylvain Meignier, “S4D: Speaker Diarization Toolkit in Python,” in Proc. Interspeech 2018, 2018, pp. 1368–1372
[^2]: Daniel Povey, Arnab Ghoshal, Gilles Boulianne, Lukas Burget, Ondrej Glembek, Nagendra Goel, Mirko Hannemann, Petr Motlicek, Yanmin Qian, Petr Schwarz, Jan Silovsky, Georg Stemmer, and Karel Vesely, “The kaldi speech recognition toolkit,” in IEEE 2011 Workshop on Automatic Speech Recognition and Understanding. Dec. 2011, IEEE Signal Processing Society, IEEE Catalog No.: CFP11SRW-USB
[^3]: Jean-Franc ̧ois Bonaster, F Wils, and Sylvain Meignier,  “Aliz ́e, a free Toolkit for Speaker Recognition,” in Proc. Interspeech 2005, 2005
[^4]: Theodoros Giannakopoulos, “pyaudioanalysis: An open-source python library for audio signal analysis,” PloS one, vol. 10, no. 12, 2015
[^5]: Gregory Gelly and Jean-Luc Gauvain, “Optimization of RNN-Based Speech Activity Detection,” IEEE/ACM Transactions on Audio, Speech, and Language Processing, vol. 26, no. 3, pp. 646–656, March 2018.  
[^6]:  Ruiqing Yin, Herv ́e Bredin, and Claude Barras, “Speaker Change Detection in Broadcast TV Using Bidirectional Long Short-Term Memory Networks,” in Proc. Interspeech 2017, 2017.  
[^7]: Latan ́e Bullock, Herv ́e Bredin, and Leibny Paola Garcia-Perera, “Overlap-Aware Resegmentation for Speaker Diarization,” Submitted to ICASSP 2020.  
[^8]: Ruiqing Yin, Herv ́e Bredin, and Claude Barras, “Neural Speech Turn Segmentation and Affinity Propagation for Speaker Diarization,” in Proc. Interspeech 2018, 2018, pp. 1393–1397.
[^9]: Herv ́e Bredin, “pyannote.metrics: a toolkit for re-producible evaluation, diagnostic, and error analysis of speaker diarization systems,” in Proc. Interspeech 2017, Stockholm, Sweden, August 2017








### Legends
| Short hand | Full form                                  |
| ---------- | ------------------------------------------ |
| SD         | Speaker Diarization                        |
| DL         | Deep Learning                              |
| PLDA       | Probabilistic Linear Discriminant Analysis |
|            |                                            |


