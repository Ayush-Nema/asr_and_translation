List of curated articles for reference reading.
1. [Sequence Modeling With CTC](https://distill.pub/2017/ctc/)
2. [ESPnet-ST: All-in-One Speech Translation Toolkit](https://aclanthology.org/2020.acl-demos.34.pdf)
3. [pyAudioAnalysis](https://github.com/tyiannak/pyAudioAnalysis)
4. [Intro to Audio Analysis: Recognizing Sounds Using Machine Learning](https://medium.com/behavioral-signals-ai/intro-to-audio-analysis-recognizing-sounds-using-machine-learning-20fd646a0ec5) 
5. [Accurate Online Speaker Diarization with Supervised Learning](https://ai.googleblog.com/2018/11/accurate-online-speaker-diarization.html)
6. [Audio Handling Basics: Process Audio Files In Command-Line or Python](https://hackernoon.com/audio-handling-basics-how-to-process-audio-files-using-python-cli-jo283u3y) 
7. [12 Speech Recognition Models in 2022; One of These Has 20k Stars on Github](https://pub.towardsai.net/12-speech-recognition-models-in-2022-one-of-these-has-20k-stars-on-github-6ed989b24cb5) 
8. [Whisper: Transcribe & Translate Audio Files With Human-Level Performance](https://towardsdatascience.com/whisper-transcribe-translate-audio-files-with-human-level-performance-df044499877)

## Description
- `pyAudioAnalysis` (pt **3.** ⇪)
	- About →
		- Python Audio Analysis Library: Feature Extraction, Classification, Segmentation and Applications
		- [Wiki page](https://github.com/tyiannak/pyAudioAnalysis/wiki)
		- Related useful video → https://www.youtube.com/watch?v=xkGN-A9BqD4
- _Intro to Audio analysis: recognising sounds using ML_ (pt **4.** ⇪)
	- Speaker diarization is also performed here using `pyAudioAnalysis`
- _Audio handling basics: process audio files in command-line or python_ (pt **6.** ⇪)
	- Explains ffmpeg, sox, audacity →command line audio processing
	- librosa, pydub, scipy → audio processing with programming
		- the sub-topics mentioned under this section are useful
	- Article written by [Theodoros Giannakopoulos](https://hackernoon.com/u/tyiannak), father of `pyAudioAnalysis`
	- Linking [[Audio signal processing | this]] for better connectivity



## Audio features
Source of table: https://dolby.io/blog/creating-audio-features-with-pyaudio-analysis/

| Feature               | Description                                                                  |
| --------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Zero-crossing rate    | The rate of sign changes of the signal during the duration of a particular frame.                                                                         |
| Energy                | The sum of squares of the signal values, normalized by the respective frame length.                                                                       |
| Entropy of energy     | The entropy of sub-frames normalized energies. It can be interpreted as a measure of abrupt changes.                                                      |
| Spectral centroid     | The center of gravity of the spectrum.                                                                                                                    |
| Spectral spread       | The second central moment of the spectrum.                                                                                                                |
| Spectral entropy      | The entropy of the normalized spectral energies for a set of sub-frames.                                                                                  |
| Spectral flux         | The squared difference between the normalized magnitudes of the spectra of the two successive frames.                                                     |
| Spectral rolloff      | The frequency below which 90% of the magnitude distribution of the spectrum is concentrated.                                                              |
| MFCCs           | Mel Frequency Cepstral Coefficients form a cepstral representation where the frequency bands are not linear but distributed according to the mel-scale.   |
| Chroma vector  | A 12-element representation of the spectral energy where the bins represent the 12 equal-tempered pitch classes of western-type music (semitone spacing). |
| Chroma deviation      | The standard deviation of the 12 chroma coefficients.                                                                                                     |


## Legends
| Short form | Full form                             |
| ---------- | ------------------------------------- |
| CTC        | Connectionist Temporal Classification |
|            |                                       |
