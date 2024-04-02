In this article:
	- [[#Definition]]
	- [[#Creating signal and sample rate]]
	- [[#Fast-Fourier & Discrete-Fourier Transforms]]



## Definition
- Immensely useful tool to help dissect information from audio files and process it into images.
- Plot info:
	- x-axis: time
	- y-axis: frequency
	- Colour intensity: amplitude of a frequency at a certain point in time
- Ex [[#Legend|Sgm]]
![450](https://miro.medium.com/max/1400/1*2MSPAVZVrijFWdUuMr4jyg.png)
- can be used as diagnostic tool for DL and CV to train CNN for various tasks.
	- music style classification → diagnose if a person has an infectious disease based on their coughing audio



## Creating signal and sample rate
- `librosa` library
```python
import librosa, librosa.display  
import numpy as np  
import matplotlib.pyplot as plt  
%matplotlib inline

signal, sr = librosa.load(librosa.util.example(‘brahms’))
```
- `librosa.load()` →loads audio file
	- outputs:
		- sample rate (`sr`)
		- signal of the audio (`signal`) →
			- 1D `NumPy` array. Length: (`sr` × duration of audio)
			- each value in `signal` is amplitude of audio at a certain point in time
		- If `sr`=22050, it means 22050 samples for each second

- To view our loaded audio, we use `librosa.display.waveplot()`
```python
plt.figure(figsize=(20, 5))  
librosa.display.waveplot(signal, sr=sr)  
plt.title(‘Waveplot’, fontdict=dict(size=18))  
plt.xlabel(‘Time’, fontdict=dict(size=15))  
plt.ylabel(‘Amplitude’, fontdict=dict(size=15))  
plt.show()
```
![470](https://miro.medium.com/max/1400/1*nXcScGcjm63ko30suhqvQA.png)
- In order to listen to the audio clip, use `IPython.display`
```python
import IPython.display as ipd
ipd.Audio(signal, rate=sr)
```
- To see the magnitude of each frequency contributing to the overall sound of the audio file, we need _Fast-fourier transformation_ (FFT) algorithm.



## Fast-Fourier & Discrete-Fourier Transforms
- FFT is an algorithm to compute DFT.
- 



## References
- [How to Create & Understand Mel-Spectrograms](https://importchris.medium.com/how-to-create-understand-mel-spectrograms-ff7634991056)
- 

## Legend
| Short form | Full form                  |
| ---------- | -------------------------- |
| Sgm        | Spectrogram                |
| FFT        | Fast fourier transform     |
| DFT        | Discrete fourier transform |
|            |                            |
