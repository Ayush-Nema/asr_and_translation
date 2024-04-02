
# Improving Speaker Segmentation via Speaker Identification and Text Segmentation #Paper 

Source: https://csl.uni-bremen.de/cms/images/documents/publications/Interspeech09_Li.pdf
Authors: Runxin Li, Tanja Schultz, Qin Jin

## Abstract
_Speaker segmentation (SS) is an essential part of a speaker diarization (SD) system. Common segmentation systems usually miss speaker change points when speakers switch fast. These errors seriously confuse the following speaker clustering step and result in high overall speaker diarization error rates. 
In this paper two methods are proposed to deal with this problem: The first approach uses speaker identification techniques to boost SS. And the second approach applies text segmentation methods to improve the performance of SS. 
Experiments on Quaero speaker diarization evaluation data shows that our methods achieve up to 45% relative reduction in the SD error and 64% relative increase in the speaker change detection recall rate over the baseline system. 
Moreover, both these two approaches can be considered as post-processing steps over the baseline segmentation, therefore, they can be applied in any SD systems._  

_**Index Terms**: speaker diarization, speaker segmentation, speaker identification, text segmentation_


## 1. Introduction
- Given an audio stream with multiple speakers involved, the goal of the SD system is to split the audio into homogeneous segments and to answer the question of ”Who spoke when?” 
- A typical SD (a.k.a. SS and clustering) system generally contains 2 components: 
	- 1st component: "==speaker segmentation==" whose goal is to split the audio into homogeneous segments and the key challenge is to detect the locations of the speaker changes or turns
	- 2nd component: "==speaker clustering==" which aims at grouping all the segments that belong to the same speaker together.

- Good SS should provide the correct speaker changes as the result; each segment should contain _exactly one speaker_.

- Segmentation errors (SEr) and their effect
	- Two types of error related to speaker change detection:
		- Insertion error→when a speaker change is detected but it does not exist in reference
		- Deletion error→an existing speaker change is not detected
	- Segmentation stage is followed by a clustering stage. ∴, insertion errors (resulting in an over-segmentation) are less critical than deletion errors, ∵ the clustering procedure has the opportunity to correct the insertion errors by grouping the segments related to the same speaker. While deletion errors cannot be recovered in the clustering stage

- Some efforts to reduce the SEr
	- Joint segmentation and clustering schemes, including iterative Viberbi decoding during agglomerative clustering [^1]
	- ergodic-HMM [^2] with a top-down strategy, in which each speaker is represented by a state and the changes between speakers are represented by transitions in the HMM.
	- iterative GMM segmentation/ clustering and re-segmentation after the initial segmentations [^3]

- Two key approaches in the paper
	- First approach
		- Uses speaker identification techniques to  refine the former segmentation criterions and label each speech segment with its speaker ID. 
		- Speaker identification techniques are proved to be helpful to the speaker clustering [^4]. 
		- In this paper the speaker identification models are trained by MAP algorithm over the background model, more discriminative powers are preserved to help boost the SS and then the following clustering task.
	- Second approach
		- Idea derived from text segmentation, which aims at partitioning a document into a set of segments, each of which is coherent about a specific topic.
		- To use this method, it is necessary to find a way to transform the feature vector of each frame of the speech signal into textual tokens.
		- In Gaussian Mixture Models (GMM) it is revealed that only the most probable mixture components have significant impacts on the tasks of speaker recognition. 
		- In this paper a GMM is first trained on the whole speech, then these components are considered as the tokens for speech frames, at last text segmentation methods are proposed after the tokenization to find the best segment boundaries during the speech.

```ad-note
- Paper organization
	- Section-2: baseline SD system and the improved systems with the two proposed approaches
	- Section-3: experimental results
	- Section-4: conclusion
```


## 2. System description
### 2.1. Baseline system
![[SD_segmentation.png | 340]]
- Baseline system consists of 3 main components:
	- Audio sementation is realized by an HMM segmenter with 4 classes:
		- speech
		- noise
		- silence
		- music
	- Speaker change detection (SCD)
	- BIC clustering
- speech features →13-dim MFCCs + their 1st and 2nd derivatives
- each class  →represented by a GMM with 64 Gaussians
- trained on 3 hours of manually annotated HUB4 English shows
- SCD applied on any segment longer than 5secs to check whether there exist speaker turn changes that have not been detected
- For speaker clustering, hierarchical, agglomerative clustering technique based on 128 tied GMM and BIC stopping criterion are used.

### 2.2. Speaker identification for SS (SID-Seg)
1. Train speaker models
2. Windowing
3. SID indexing
4. Segments merging
5. BIC clustering
`Details Skipped`

### 2.3. Text segmentation for SS (TS-Seg)
`Skipped`

## 3. Experiments
### 3.1. Corpora and experimental design
### 3.2. Experimental results
`Details skipped`

## 4. Conclusions
`Skipped`

## 5. References
[^1]: Gauvain, J. -L., Lamel, L. and Adda, G. “Partitioning and transcription of broadcast news data,”, in Proc. Int. Conf. Spoken Lang. Process., vol. 4, Sydney, Australia, Dec. 1998, pp. 1335- 1338.
[^2]: Meignier,S., Bonastre, J.-F., Fredouille, C., and Merlin, T., “Evolutive HMM for multispeaker tracking system,” in Proc. IEEE Int. Conf. Acoust., Speech, Signal Process., vol. II, Istanbul, Turkey, Jun. 2000, pp. 1201-1204.  
[^3]: Barras,C., Zhu, X. , Meignier, S. , and Gauvain, J.-L.,“Improving speaker diarization, ”, in Proc. Fall Rich Transcription Workshop (RT-04), Palisades, NY, Nov. 2004.
[^4]: Zhu, X. , Barras, C., Meignier, S. , and Gauvain, J.-L., “Combining speaker identification and BIC for speaker diarization,” in Proc. Eur. Conf. Speech Commun. Technol., Lisbon, Portugal, Sep. 2005, pp. 2441-2444


## Legends
| Short form | Full form                      |
| ---------- | ------------------------------ |
| SD         | Speaker diarization            |
| SS         | Speaker segmentation           |
| DER        | Diarization error rate         |
| SEr        | Segmentation error             |
| HMM        | Hidden markov model            |
| GMM        | Gaussian mixture model         |
| BIC        | Bayesian information criterion |
| SCD        | Speaker change detection       |
|            |                                |
