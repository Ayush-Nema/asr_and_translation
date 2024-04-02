- _Open source tool_ for audio data annotation
- Part of Fine-tuning efforts for `pyAnnote` pre-trained model
- Refer [[pyannote.audio finetuning requirements |this]] article for more information on data requirements and their format.

_Useful resources_ for learning Label Studio →
1. Demonstration video from the creators of `Label Studio`: [Installing Label Studio Plus Overview of Basic Features](https://www.youtube.com/watch?v=A0cob_f5BmM)
2. Label Studio channel in YouTube: [Link](https://www.youtube.com/@labelstudio520)

Other tools for audio data annotation:
1. `Prodigy` →paid ([link](https://prodi.gy/docs/audio-video))
2. `LabelBox` →paid ([link](https://labelbox.com/))
3. `Label Studio` →Open source ([link](https://labelstud.io/))

### Important links
- [Data upload to studio](https://labelstud.io/guide/tasks.html#Basic-Label-Studio-JSON-format)
- [Import the pre-annotated data](https://labelstud.io/guide/predictions.html)
---

## Installation
- Install Label Studio:
```shell
$ pip install label-studio
```
- Start Label Studio
```shell
$ label-studio
```
- Open the Label Studio UI at [http://localhost:8080](http://localhost:8080/)
- Sign up with an email address and password that you create.
- Click **Create** to create a project and start labeling data. 
- Name the project, and if you want, type a description and select a color.
- Click **Data Import** and upload the data files that you want to use. If you want to use data from a local directory, cloud storage bucket, or database, skip this step for now.
- Click **Labeling Setup** and choose a template and customize the label names for your use case.
- Click **Save** to save your project.


## Directions
- store the files in `audio_data_labelling` repository under `/data` directory.
- run `upstream.py` for converting pre-annotations `.csv` files to `.json` consumed by Label Studio.
- the ouput `.json`'s will be stored in `op_jsons` directory under `/data`
- import these json's to Label Studio
- Problem: 
	- LS has its own file system unlike local system
	- Ex: `label/2/....json`
	- When these json's are uploaded with just the filename (abc.wav) or file address LS rejects the filepath and the task crashes.
- Potential solution:
	- web scrapping of dashboard to get the filename
		- it only gives filename, not complete address
	- LS →Settings → Cloud Storage → Local files OR AWS S3
		- For local files, LS does not allow. How to enable?
			- Change in environment variable ([instructions](https://labelstud.io/guide/start.html#Set-environment-variables))
			- Read instructions [here](https://labelstud.io/guide/storage.html#Local-storage) and check below for exact commands.
			- `LABEL_STUDIO_LOCAL_FILES_SERVING_ENABLED=true`
			- `LABEL_STUDIO_LOCAL_FILES_DOCUMENT_ROOT=/home/user`



Setting the environment variables:
```shell
$ export LABEL_STUDIO_LOCAL_FILES_SERVING_ENABLED=true
$ echo $LABEL_STUDIO_LOCAL_FILES_SERVING_ENABLED 

	$ export LABEL_STUDIO_LOCAL_FILES_DOCUMENT_ROOT=/Users/ayush.nema/Downloads/AIOps_data/01_DA/
$ echo $LABEL_STUDIO_LOCAL_FILES_DOCUMENT_ROOT   

$ export LOCAL_FILES_DOCUMENT_ROOT=/Users/ayush.nema/Downloads/AIOps_data/01_DA/


```