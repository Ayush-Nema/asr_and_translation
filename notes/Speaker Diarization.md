 **In this article:**
- [[#Definition]]
- [[#Areas of application]]
- [[#Anatomy of a Speaker Diarization System]]
	- [[#1. Traditional SD system]]
	- [[#2. End-to-end neural diarization (EEND) system]]
- [[#SD frameworks]]
- [[#Implementation]]
	- [[#Brief Explanation of paper]]
	- [[#**Code**|Code]]
	- [[#Using Google's speech-to-text API]]

 
 ![400](https://miro.medium.com/max/1400/1*vlK0QyQU4MizY6FMQZvWLQ.png)


 ## Definition
 - **Who spoke when!** 🗣️
 - Speaker diarization is the process of partitioning an audio stream with multiple people into homogeneous segments associated with each individual. (It is an important part of speech recognition systems and a well-known open problem.)
 - Take the audio file ⇨ segment it speaker-wise ⇨ create the transcript of conversation
 - **Speaker Diarization** is the task of segmenting and co-indexing audio recordings by speaker. The way the task is commonly defined, the goal is not to identify known speakers, but to co-index segments that are attributed to the same speaker; in other words, diarization implies finding speaker boundaries and grouping segments that belong to the same speaker, and, as a by-product, determining the number of distinct speakers. In combination with speech recognition, diarization enables speaker-attributed speech-to-text transcription.
 - Details of speech segmentation and its relation with SD is presented here: [[Speaker segmentation]]
 - ![600](https://miro.medium.com/max/1400/1*LhJIQly3bH3pAS0OnK04DA.png)
 - By generating a *speaker-aware transcript*, we can more easily interpret the generated conversation compared to a generated transcript without diarization. ![500](https://miro.medium.com/max/1400/1*918Vvgx5K27eAM2ymD9cbg.png)


## Areas of application
- understanding medical conversations
- video captioning
- customer care call analytics


## Anatomy of a Speaker Diarization System
### 1. Traditional SD system
A Speaker Diarization system consists of the following subsystems :
![500](https://miro.medium.com/max/1400/1*SCLo2lPhAXJsxdX_Lw2CPA.png)

-   ==**Speech Detection==**
	- In this step, we use a Voice Activity Detector (VAD) module to _separate out speech from non-speech_. This is required to trim out silences and non-speech part from your audio recording.
-   **==Speech Segmentation==**
	- In this step, we _extract out small segments of audio_ call (typically about 1-2 secs long) in a continuous manner. Speech segments typically contain just one speaker.
-   ==**Embedding Extraction**== (speech embedder)
	- This system creates a neural-network based embedding of your speech segments extracted in the previous step. An embedding is a vector representation of data which could be used by the deep learning framework and it summarizes the voice characteristics (a.k.a voice print). We can create embeddings for audio, text, images, documents etc.
-   ==**Clustering**== 
	- After creating embeddings of the segments, we next need to cluster these embeddings. After clustering, the embeddings of the segments belonging to same speakers are part of one cluster, and assigned the label of the speaker. This step is crucial as it assigns the labels to our embeddings, as well as the number of clusters, which indicates us of the number of speakers in the audio file.
-   ==**Transcription**==
	- After clustering, we now have the labels for all the embeddings. Using this, we can now segment out the audio into clips belonging to each speaker, and pass it to a Speech-to-Text model (or ASR) which produces the transcript of the speech segment.

### 2. End-to-end neural diarization (EEND) system
Traditional SD system can be replaced by one neural network that is trained end-to-end on SD.
- Advantages
	- Direct optimization of the network towards maximizing the accuracy for the diarization task. This is in contrast with traditional systems where submodules are optimized individually but not as a whole.
	- Less need to come up with useful pre-processing and post-processing transformation on the input data.
- Disadvantages
	- More effort needed for data collection and labelling. This is because this type of approach requires speaker-aware transcripts for training. This differs from traditional systems where only labels consisting of the speaker tag along with the audio timestamp are needed (without transcription efforts).
	- These systems habe the tendency to overfit on the training data.


## SD frameworks
- Building a speaker diarization system also involves aggregating quite a few building blocks and the implementation can seem daunting at first glance.
- Luckily, there exists a plethora of libraries and packages that have all those steps implemented and are ready for you to use out of the box .
- All but the last framework ([UIS-RNN](https://github.com/google/uis-rnn)) are based on the traditional diarization approach.
- More exhaustive list of different diarization libraries: [Awesome Speaker Diarization](https://wq2012.github.io/awesome-diarization/).

1. [`pyAnnote`](https://github.com/pyannote/pyannote-audio)
	1. pre-trained models are based on [VoxCeleb datasets](https://www.robots.ox.ac.uk/~vgg/data/voxceleb/) which consists of recordings of celebrities extracted from YouTube. The audio quality of those recording is crisp and clear → might need to retrain the model if you want to use the model on other use case.
	2. (+) comes with a set of _pre-trained models_ for Voice Activity Detection, embedder, and segmentation.
	3. (+) can _identify multiple speakers_ at the same time (multi-label diarization on overlapping speech)
	4. (-) not possible to define the no of speakers before running the clustering algorithm. This _could lead to an over- or under- estimation_ of speakers.
2. [`NVIDIA NeMo`](https://developer.nvidia.com/nvidia-nemo)
	1. has separate collections for Automatic Speech Recognition (ASR), Natural Language Processing (NLP), and Text-to-Speech (TTS) models.
	2. The models that the pre-trained networks were trained on [VoxCeleb datasets](https://www.robots.ox.ac.uk/~vgg/data/voxceleb/) as well as the [Fisher](https://catalog.ldc.upenn.edu/LDC2004T19) and [SwitchBoard](https://catalog.ldc.upenn.edu/LDC97S62) dataset, which consists of telephone conversations in English. This makes it more suitable as a starting point for fine-tuning a model for call-center use cases compared to the pre-trained models used in pyannote. More information about the pre-trained models can be found [here](https://docs.nvidia.com/deeplearning/nemo/user-guide/docs/en/stable/asr/speaker_diarization/results.html).
	3. (+) Diarization results can be combined easily with ASR outputs to _generate speaker-aware transcripts_.
	4. (+) Possibility to _define the no of speakers beforehand_ if they are known, resulting in a more accurate diarization output.
	5. (+) The fact that the NeMo toolkit also includes NLP related frameworks makes it _easy to integrate_ the diarization outcome with downstream NLP tasks.
3. [`Simple Diarizer`](https://github.com/cvqluu/simple_diarizer)
	1. Simple diarization pipeline for quick testing
	2. Uses same pre-trained models as `pyAnnote`
	3. (+) Similar to `NVIDIA NeMo`, the _no of speakers are defined_ a priori
	4. (-) Unlike `pyAnnote`, we _can't fine tune_ pre-trained model, making it less suitable for specialized use cases.
4. [`SpeechBrain`](https://github.com/speechbrain/speechbrain)
	1. All-in-one conversational AI toolkit based on `pyTorch`
	2. (+) `SpeechBrain` ecosystem make it easy to develop integrated speech solutions with systems such ASR, speaker identification, speech enhancement, speech separation and language identification.
	3. (+) _Large amount of pre-trained models_ for various tasks. Check out their [HuggingFace page](https://huggingface.co/speechbrain) for more information.
	4. (+) Contains [_comprehensible tutorials_](https://speechbrain.github.io/tutorial_basics.html) for various speech building blocks to easily get started.
	5. (-) Diarization pipeline is still _not fully implemented_ yet but this [might change in the future](https://github.com/speechbrain/speechbrain/issues/1208).
5. [`Kaldi`](https://github.com/kaldi-asr/kaldi)
	1. Speech recognition toolkit that is mainly targeted towards researchers. It is built with C++ and used to train speech recognition models and decode audio from audio files.
	2. pre-trained models are based on `CALLHOME` dataset. [[Datasets|Link here]] for more info.  
	3. Benefits from large community support. However, mainly targeted towards researchers and less suitable for production ready-solutions.
	4. (-) Relatively _steep learning curve_ for beginners who don’t have a lot of experience with speech recognition systems.
	5. (-) _Not suitable for a quick implementation_ of ASR/ diarization systems.
6. [`UIS-RNN`](https://github.com/google/uis-rnn)
	1. A fully supervised end-to-end diarization model developed by Google.
	2. Both training and prediction require the usage of a GPU.
	3. (+) Relatively _easy to train_ if you have a large set of pre-labelled data.
	4. (-) _No pre-trained model available_, so you need to train it from scratch on your custom transcribed data.
7. [ESPNet](https://github.com/espnet/espnet)
	1. `description skipped`
	2. Highest number of Github stars (✯5.8k) among all other SD libraries.
8. [pyAudioAnalysis](https://github.com/tyiannak/pyAudioAnalysis)
	1. `description skipped`
	2. Has 5.1k ✯'s  on Github

![500](https://miro.medium.com/max/1400/1*6uXGAXbAc0JdDL-wJakHFw.png)


---

## Implementation
Implemention of a research paper published by Google Brain in 2017.
Paper: [_Speaker Diarization with LSTM_](https://arxiv.org/abs/1710.10468)

#### Brief Explanation of paper
In this paper the authors have implemented all the sub-parts of a speaker diarization system as discussed [[#Anatomy of a Speaker Diarization System |before]] -
-  *Speech Detection*
	- The authors have used the VAD module from [pyannote.metrics](https://github.com/pyannote/pyannote-audio) library. A **VAD** is basically a neural network trained to distinguish speech signals from non-speech signals. It is used to trim out silences from the audio file.
-  *Speech Segmentation*
	- This is achieved by segmenting the audio into windows with overlap. The size of the window determines the size of your segment. Think of it as a magnifier which works on a specific segment of your audio file. So, if your window size is **2 seconds**, and you set an overlap or stride length of **0.5 seconds**, your first window would be : (**start** = 0.0s , **stop** = 2.0s), next window will be: (**start** = 0.5s, **stop** = 2.5s) … and so on until your full audio is covered.
-  *Embedding Extraction*
	- The authors extracted embeddings of each of the audio segments which we found before. First, we find the **MFCC (Mel Frequency Cepstral Coefficient)** of the audio segment. These are basically feature coefficients which capture the variations in the speech like pitch, quality, intone etc of the voice in a much better way. They are obtained by doing a specialized Fourier Transform of the speech signal. Don’t worry, the SciPy library of python has a separate module for finding MFCCs for us. In the next step, the authors use an **LSTM based network** which takes in the MFCCs and outputs a vector representation (embedding) which they call a **d-vector**.
-  *Clustering*
	- Clustering is an **Unsupervised machine learning** method which tries to create clusters (or groups) of your data in an n-dimensional space. There are many clustering algorithms being used by machine learning researchers, most famous among them being **K-means**.

In their work, the authors found that **[[Spectral Clustering]]** works best for their data.

> Spectral clustering works by first creating an *Affinity matrix* of the data (a matrix whose $(i,j)^{th}$ element is obtained by performing some mathematical operation on $i^{th}$ and $j^{th}$ embedding), and then after performing some matrix operations on this affinity matrix, we use K-means to get the final labels.

And that’s it. This paper has been a big step in the field of Speaker Diarization, and set new benchmarks by achieving a lower **Diarization Error Rate** than the previous state-of-the-art methods (Diarization Error Rate or **DER** is the metric used to evaluate Speaker Diarization models.)

![[d-vec based diarization.png | 500]]


#### **Code**
In our implementation, we will be using this awesome open-source repository for our work — [**`Resemblyzer`**](https://github.com/resemble-ai/Resemblyzer) . 
`Resemblyzer` will be doing three of our major tasks, namely, Speech detection, Speech Segmentation and Embedding Extraction.

1. Clone the repo
```shell
git clone https://github.com/resemble-ai/Resemblyzer.git
cd Resemblyzer
```

2. Get the small audio file (2-3mins), and save it as `my.recording.wav`  
``` python
from resemblyzer import preprocess_wav, VoiceEncoder
from pathlib import Path

#give the file path to your audio file
audio_file_path = 'my_recording.wav'
wav_fpath = Path(audio_file_path)
wav = preprocess_wav(wav_fpath)
encoder = VoiceEncoder("cpu")

_, cont_embeds, wav_splits = encoder.embed_utterance(wav, return_partials=True, rate=16)
print(cont_embeds.shape)
```

**Step-by-step explaination**
-   You provide the pathname to your file by passing it into the Path module, in this case its ‘my_recording.wav’. Then you pass the file path to the **preprocess_wav** function, which internally uses a **VAD** to trim out the silences in the audio file and also normalizes the decibel level of audio.
-   Then you create an instance of the **VoiceEncoder** class name encoder, passing cpu as your default device. The **embed_utterance** function of this instance takes in the processed wav file, segments it out into windows , makes MFCCs (Mel-frequency cepstral coefficients) of these segments and finally creates **d-vectors** of these audio segments.
-   The **cont_embeds** is a **N by D** matrix, where **N is the number of segments** created (which is equal to the number of d-vectors) and **D is the dimension of each d-vector**, which by default is 256. **wav_splits** is a list with the start and end time of each window for which a d-vector has been created.

```ad-warning
Sometimes the above code throws error if audio file is not with `.wav` extension. But it can converted from `.mp3` using `pydub` library.
```
```python
$ !pip install pydub

from pydub import AudioSegment

def mp3_to_wav(audio_file_path):
	sound = AudioSegment.from_mp3(audio_file_path)
	audio_file_path = audio_file_path.split('.')[0] + '.wav'
	sound.export(audio_file_path, format="wav")
return audio_file_path

audio_file_path = mp3_to_wav(audio_file_path)
print(audio_file_path)`
```

3. Next step is the clustering of our d-vectors. For this, we will use an open source implementation of Spectral Clustering by Quan Wang, one of the original authors of the paper that we are implementing.

Install `spectralcluster` library in virtual env.
`$ pip3 install spectralcluster`

```python
from spectralcluster import SpectralClusterer

clusterer = SpectralClusterer(min_clusters=2, max_clusters=100, p_percentile=0.90, gaussian_blur_sigma=1)

labels = clusterer.predict(cont_embeds)
```
That’s it. We now have got labels for our d-vectors. We are almost done!

**Creating continuous segments**
4. We have got labels for our d-vectors in the previous step. However, each d-vector corresponds to a small window frame, and using them directly for any further task is not feasible. We need to join continuous windows which have a common speaker together.

```python
from resemblyzer import sampling_rate

def create_labelling(labels,wav_splits):
	times = [((s.start + s.stop) / 2) / sampling_rate for s in wav_splits]
	labelling = []
	start_time = 0
	
	for i,time in enumerate(times):
		if i>0 and labels[i]!=labels[i-1]:
			temp = [str(labels[i-1]),start_time,time]
			labelling.append(tuple(temp))
			start_time = time
				
		if i==len(times)-1:
			temp = [str(labels[i]),start_time,time]
			labelling.append(tuple(temp))

return labelling

labelling = create_labelling(labels,wav_splits)
```
We pass the previously created **labels** and **wav_splits** to the **create_labelling** function, which finds a **continuous segment of windows having a common label** (or a common speaker) and merges them together. **labelling** is a list of tuples with values in order **(speaker_label, start_time, end_time)**

**Using our diarization labels**
5. After you have got the labelling file, which is basically a dictionary of **which person spoke when** your diarization task is in theory complete. You can now use these labels to create a text transcription of your audio call as we had seen earlier, or create a cool animation [plot](https://youtu.be/SY6rHaD-eEc). 


## Using Google's speech-to-text API
You can use Google’s [speech-to-text](https://cloud.google.com/speech-to-text/) API for creating a transcript of your audio file. You can find the step-by-step process for getting your own API account [here](https://cloud.google.com/speech-to-text/docs/quickstart-client-libraries). I am providing below the pseudocode which you can follow to create your transcript —

1.  For each tuple element ‘ele’ in your labelling file, extract ele[0] as the speaker label, ele[1] as the start time and ele[2] as the end time.
2.  Trim your original audio file from start time to end time. You can use **ffmpeg** for this task.
3.  Pass the trimmed audio file obtained in the previous step to Google’s API which will return you the text transcript of this audio segment.
4.  Write the transcript along with the speaker label to a text file and save it.

And that’s it!

After going through each element in your labelling file and following the above procedure you will get your transcript file created, something like this- 

```
Speaker 0 : well that was Jason and Yuki we asked you who's Yuki meeting on Saturday night   
Speaker 1 : probably going to meet   
Speaker 0 : but instead of saying going to Yuki said going to she's probably going to meet Lucy   
Speaker 1 : salt with looking up and mint program English because often say the words going to very far out of it. Like going to going to say usually do this if Informer situation when the choking   
Speaker 0 : does that mean that you shouldn't say going to when you need to speak carefully and politely like in a job interview   
Speaker 1 : really it's going to come out naturally probably okay but a job interview so it's probably a bit more polite and you don't usually use going to NY pic unless you actually writing that. In science   
Speaker 0 : some people never use it even informally more common in American English   
Speaker 1 : one other thing about is that we only use it to replace into when going to explode   
Speaker 0 : rude if you say I'm going to Parrot you can't use going to to say I'm going to Paris you have to add a verb base and say I'm going to go to Paris   
Speaker 1 : I go to Paris let's have another clip with the mold short spoken both during this weekend's UK   
Speaker 0 : so Jason said whatcha whatcha that's a short form of what are you but sometimes what do you
```


--- 
### References
- [Who spoke when! How to Build your own Speaker Diarization Module](https://medium.com/saarthi-ai/who-spoke-when-build-your-own-speaker-diarization-module-from-scratch-e7d725ee279)
- [Awesome diarization library](https://github.com/wq2012/awesome-diarization)
- [Speaker Diarization](https://paperswithcode.com/task/speaker-diarization)
- [Who spoke when: Choosing the right speaker diarization tool](https://blog.ml6.eu/who-spoke-when-choosing-the-right-speaker-diarization-tool-3d7a115c524b)
