Fine tuning code for `pyAnnote`: [Click here](https://github.com/pyannote/pyannote-audio/blob/develop/tutorials/adapting_pretrained_pipeline.ipynb) 
Related article: [[pyAnnote.audio]]

# KEY QUESTIONS
1.  Each file category, what are (how many) the speakers?
2.  Tool required and how to use the tool. 
3.  For each category One file annotated as example along with file format. 
	1. how much time it takes for one annotation?
4.  list of files.

---
**In this document →** 
1. [[#Data requirement]]
2. [[#Labelling criterion]]
3. [[#Directory structure]]
4. [[#Testing]]
5. [[#References]]

---

## Data requirement
Annotated data require following files.

- ==**RTTM**== (_Rich Transcription Time Marked_) →
	- Considered as label (primary) file
	- Systems should output their diarizations as RTTM files. A NIST defined file format, the RTTM files are text files containing one turn per line, each line containing nine space-delimited fields:  
		- Type segment type ⇨ should always by “SPEAKER”  
		- File ID file name ⇨ basename of the recording minus extension (e.g., “FS P01 eval 023”)  
		- Channel ID ⇨ channel (1-indexed) that turn is on; should always be “1”  
		- Turn Onset ⇨ onset of turn in seconds from beginning of recording  
		- Turn Duration ⇨ duration of turn in seconds  
		- Orthography ⇨ Field should always by \<NA>  
		- Speaker Type ⇨ should always be \<NA>
		- Speaker Name ⇨ name of speaker of turn; should be unique within scope of each file 
		- Confidence Score (Optional)  ⇨ system confidence (probability) that information is correct; should always be \<NA>.

- ==**UEM**== (_Unpartitioned evaluation map_) →
	- UEM files are used to specify the scoring regions within each recording. For each scoring region, the UEM file contains a line with the four space-delimited fields.
	- The scoring region for each audio file is provided separately through [NIST](https://web.archive.org/web/20170119114252/http://www.itl.nist.gov/iad/mig/tests/rt/2009/docs/rt09-meeting-eval-plan-v2.pdf) defined UEM format.
	- The scoring region for each recording will be the entirety of the recording; that is, for a recording of duration 405.37 seconds, the scoring region will be \[0, 405.37]. 
	- These regions will be provided to the scoring tool via un-partitioned evaluation map (UEM) files, which are plaintext files containing one scoring region per line, each line consisting of four space-delimited fields:  
		-  `File ID` – file name; basename of the recording minus extension (e.g., “rec1 a”)  
		-  `Channel ID` – channel (1-indexed) that scoring region is on; ignored by scoring tool  
		-  `Onset` – onset of scoring region in seconds from beginning of recording  
		-  `Offset` – offset of scoring region in seconds from beginning of recording
	- For the dev set, the UEM will be available from the scoring section of the DIHARD website. The eval set UEM will be released alongside RTTMs at the conclusion of the evaluation [^1].
	- Considered as supplementary file

- ==JSON== (Optional; required for Sentiment analysis over audio clip) →
	- The transcriptions are provided in JSON format for each file as \<file ID\>.json. The JSON file includes the following pieces of information for each utterance: 
		- Speaker ID Token: “speakerID”  
		- Transcription Token: “words”  
		- Sentiment Token: “sentiment”  
		- Start Time Token: “startTime”  
		- End Time Token: “endTime”


- Ex of UEM [^1] → 
	- Note: Each line is a separate file
```text
ES2011a 1 0.000 1113.845375
FS P01 dev 001 1 234.32 737.54
CMU_20020319_1400_d01_NONE 1 125.000000 727.090000  
ICSI_20010208_1430_d05_NONE 1 97.440000 697.290000
```

- Ex of JSON [^3] →
```json
{
"speakerID": "NEIL",  
"words": "It’s one small step for man,",  
"sentiment": "POSITIVE"  
"startTIme": "1325.203"  
"endTime": "1327.501"  
},  
{  
"speakerID": "NEIL",  
"words": "One, Giant leap for mankind.",  
"sentiment": "POSITIVE"  
"startTIme": "1330.162"  
"endTime": "1332.89"  
}, ...
```

- Ex of RTTM [^3] →
```text
SPEAKER FS P01 dev 001 1 256.04 2.35 <NA> <NA> EECOM1 <NA>  
SPEAKER FS P01 dev 001 1 358.08 3.06 <NA> <NA> FD1 <NA>  
SPEAKER FS P01 dev 001 1 368.97 2.22 <NA> <NA> GNC1 <NA>
```

- More UEM and RTTM files are present in `AMI-diarization-setup`[^2]
- The original document stating the description of formats is present in [The 2009 (RT-09) Rich Transcription Meeting Recognition Evaluation Plan](https://web.archive.org/web/20170119114252/http://www.itl.nist.gov/iad/mig/tests/rt/2009/docs/rt09-meeting-eval-plan-v2.pdf) document.


## Labelling criterion
- For _Speaker Diarization_ [^3] →
	- Speech segments for which human annotators were unable to identify the the Speaker have been labeled “UNK” and will not be scored.
	- Overlap instances will not be considered for Diarization.
	- All the scoring regions will be provided in the UEM files. For the dev set, the UEM will be available for download.  
	- Consecutive segments of the same speaker with a non-speech segment of ≤ 1 second come together and are considered as a single segment.  
	- A forgiveness collar of 0.25 seconds, before and after each reference boundary, will be considered in order to take into account both inconsistent human annotations and the uncertainty about when a speaker begins or ends.

- For _Speaker identification_ [^3]→
	- Speech segments for which human annotators were unable to identify the Speaker have been labeled “UNK” and will not be provided as instances in Dev the Eval sets.
	- Speech segments which have an associated speaker sabel may contain background noise and presence of speech from background conversations.  
	- Consecutive segments of the same speaker with a non-speech segment of ≤ 2 seconds come together and are considered as a single segment.

- For _ASR_ [^3]→
	- Segments or parts of speech unintelligible to the annotators are marked as [unk] and will not be scored during evaluation.
	- Overlap instances with SNR ≤ 0 dB are marked as [unk] and will not be scored during evaluation.
	- Consecutive segments of the same speaker with a silent of less that 2 seconds come together and are considered as a single segment.

- For _Sentiment_ [^3]→
	- Every 10ms ground-truth speech segment will be scored and a cumulative accuracy score over the entire scoring region will be provided for each audio file.
	- Non-Speech segments will not be scored. 
	- Speech segments for which human annotators were unable to identify the the speech content have been labeled as [unk] and will not be scored.
	- Overlap instances will not be scored.
	- The development set is provided as a JSON file per audio file, and the same format will be expected for system submissions on the evaluation set.
	- Consecutive segments of the same speaker with a non-speech segment of ≤ 1 second come together and are considered as a single segment.  
	- A forgiveness collar of 2 seconds, before and after each reference boundary, will be considered in order to take into account both inconsistent human annotations and the uncertainty about when a speaker begins or ends.



## Directory structure
- Similar to [^4] (subset of [^2]). 
- `$ tree -d AMI-diarization-setup`
- Refer `AMI-diarization-setup/pyannote/database.yml` for more information.
- Data info:
	- `dev` ⇨ validation set
	- `test` ⇨ test set
	- `train` ⇨ training set
```shell
AMI-diarization-setup
├── lists
├── only_words
│   ├── labs
│   │   ├── dev
│   │   ├── test
│   │   └── train
│   └── rttms
│       ├── dev
│       ├── test
│       └── train
├── pyannote
├── uems
│   ├── dev
│   ├── test
│   └── train
└── word_and_vocalsounds
    ├── labs
    │   ├── dev
    │   ├── test
    │   └── train
    └── rttms
        ├── dev
        ├── test
        └── train
```

## Testing
- Task →
	- Score a set of system output RTTMs `sys1.rttm`, `sys2.rttm`, ... against corresponding reference RTTMs `ref1.rttm`, `ref2.rttm`, ... using the un-partitioned evaluation map (UEM) `dev.uem`.

## References
[^1]: : [First DIHARD Challenge Evaluation Plan](https://catalog.ldc.upenn.edu/docs/LDC2019S09/first_dihard_eval_plan_v1.3.pdf)
[^2]: : [BUTSpeechFIT/AMI-diarization-setup](https://github.com/BUTSpeechFIT/AMI-diarization-setup) 
[^3]: : [Fearless Steps Challenge (FS-I)](https://arxiv.org/pdf/2211.02051.pdf)
[^4]: : [pyannote/AMI-diarization-setup](https://github.com/pyannote/AMI-diarization-setup)

---
PS: 
[^3] → imp resource for conceptual information.