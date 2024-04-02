Parent article: [[Label Studio]]

This article explains the sequence of steps to be carried out for data import, LS configurations, and export.

## Pre-annotation
- Install LS as mentioned [[Label Studio|here]].
- Go to IDE where LS is installed. Open terminal and run `$ label-studio` to launch LS interface (port 8080; default).
- LS interface will be launched. Sign-in into the LS window.
- Window like this will appear.
	- `Create` a project not already created.
		- ![[Screenshot 2023-01-15 at 6.05.27 PM.png]]
- All the data is stored in `~/Library/Application Support/label-studio/media/upload` path.
	- `cd "Library/Application Support/label-studio/media/upload"` (run from system root)
- Every project that is created in LS is assigned a number e.g. `1`, `2`, `3` etc.
	- Projects are stored as numbers and not by there names.
- In order to change the annotation interface, click on the project tile and navigate as follows:
	- project name →Settings →Labelling Interface →Browse Templates →Audio/Speech Processing → _Speaker Segmentation_.

## Data import
- Click on the selected project.
- ![[Screenshot 2023-01-15 at 6.15.45 PM.png|600]]
- Click on `Import` (top right) or `Go to import` (bottom)
- Following window will open. ![[Screenshot 2023-01-15 at 6.17.55 PM.png | 600]]
- Until the data is not imported, the project directory is not created.
	- Perform `cd "Library/Application Support/label-studio/media/upload"` and check.
- Even if data is loaded to LS interface but not submitted, it will still create project directory and submit the data!! (Maybe a bug or some feature)
	- Here 5 is the abbreviation for current project. Notice it being created upon data import.  ![[Screenshot 2023-01-15 at 6.25.17 PM.png]]
	- Data is still not submitted. Here is how it looks. ![[Screenshot 2023-01-15 at 6.27.09 PM.png | 600]]
- [IMP] Create a new `.txt` file by the name `SOSsub_ls_filenames.txt` in `audio_data_labelling/data/file_list` directory. This will be used for data preparation for model fine-tuning operation.
- Continue and Press `Import` data. Once done the interface should look like this. 
	- ![[Screenshot 2023-01-15 at 6.35.37 PM.png | 600]]
	- Notice the filenames have been appended with a _unique identifier_. If same file is submitted multiple times, it will still get submitted because of different identifier.
	- The meta information is stored as JSON. Click on `Show task source` towards any submitted file. Without any annotation, it looks like this: 
		- ![[Screenshot 2023-01-15 at 6.42.16 PM.png | 600]]

## Environment setup
- Once the Annotation template is setup and data is uploaded, click on any file and the annotation window opens.
	- ![[Screenshot 2023-01-15 at 6.45.00 PM.png | 600]]
- This is the default template rendering. Our requirement may change from this. Maybe we have more speakers or we need more information present in the page. That's when we change the visuals.
- Go to Settings →Labelling Interface → Click on `Code`
	- Labelling Interface page (by default checked on `Visual`)   ![[Screenshot 2023-01-15 at 6.50.57 PM.png | 600]]
	- Click `Code` and this will appear. ![[Screenshot 2023-01-15 at 6.52.55 PM.png]]
- Paste the following code-block and press `Save` to change interface properties.
```html
<View>
  <Labels name="label" toName="audio" zoom="true" hotkey="ctrl+enter">
    
    
  <Label value="agent" background="#0eb4f1"/>
  <Label value="customer" background="#D4380D"/>
  <Label value="dealer" background="#efcb00"/>
  <Label value="spk_0" background="#aef12b"/>
  <Label value="spk_1" background="#6eacbb"/>
  </Labels>
  
  <Audio name="audio" value="$audio"/>
  
 
  <Header value="Confirmation"/>
  <Text name="text" value="Are the annotations reviewed?"/>
  <Choices name="choices" toName="text" choice="single" showInLine="true">
    <Choice value="Yes"/>
    <Choice value="No"/>
    <Choice value="Pending"/>
  </Choices>
</View>
```
- This customises the interface and adds new feature to it.
- ![[Screenshot 2023-01-15 at 6.57.49 PM.png | 600]]

## Annotate the data
- Now the data and the UI is ready for annotation. Simply go to any file. 
- `Play` and Pause audio clip to select regions for each speaker.
- ![[Screenshot 2023-01-15 at 7.11.44 PM.png | 700]]
- Once conveniced of annotation, press `Submit`. This alters the basic JSON and fills the `annotations` field.
```json
{
  "id": 155,
  "data": {
    "audio": "/data/upload/3/177ee07a-RE0f6b4bd57298cd7524d2ae098f6ded03.wav"
  },
  "annotations": [
    {
      "id": 60,
      "created_username": " ayush.nema@toyotaconnected.co.in, 1",
      "created_ago": "0 minutes",
      "completed_by": {
        "id": 1,
        "first_name": "",
        "last_name": "",
        "avatar": null,
        "email": "ayush.nema@toyotaconnected.co.in",
        "initials": "ay"
      },
      "result": [
        {
          "original_length": 64.4455,
          "value": {
            "start": 3.139853836784409,
            "end": 5.416247868453105,
            "channel": 0,
            "labels": [
              "agent"
            ]
          },
          "id": "NTt0t",
          "from_name": "label",
          "to_name": "audio",
          "type": "labels",
          "origin": "manual"
        },
        {
          "original_length": 64.4455,
          "value": {
            "start": 6.201211327649208,
            "end": 11.067984774665042,
            "channel": 0,
            "labels": [
              "customer"
            ]
          },
          "id": "cKvg7",
          "from_name": "label",
          "to_name": "audio",
          "type": "labels",
          "origin": "manual"
        },
        {
          "original_length": 64.4455,
          "value": {
            "start": 12.088437271619975,
            "end": 15.463780146163215,
            "channel": 0,
            "labels": [
              "agent"
            ]
          },
          "id": "dXRVF",
          "from_name": "label",
          "to_name": "audio",
          "type": "labels",
          "origin": "manual"
        },
        {
          "original_length": 64.4455,
          "value": {
            "start": 16.641225334957365,
            "end": 18.05415956151035,
            "channel": 0,
            "labels": [
              "customer"
            ]
          },
          "id": "GNwVu",
          "from_name": "label",
          "to_name": "audio",
          "type": "labels",
          "origin": "manual"
        },
        {
          "original_length": 64.4455,
          "value": {
            "start": 18.28964859926918,
            "end": 20.252057247259437,
            "channel": 0,
            "labels": [
              "customer"
            ]
          },
          "id": "knRRG",
          "from_name": "label",
          "to_name": "audio",
          "type": "labels",
          "origin": "manual"
        },
        {
          "original_length": 64.4455,
          "value": {
            "start": 20.56604263093788,
            "end": 22.057473203410474,
            "channel": 0,
            "labels": [
              "customer"
            ]
          },
          "id": "gFlue",
          "from_name": "label",
          "to_name": "audio",
          "type": "labels",
          "origin": "manual"
        },
        {
          "original_length": 64.4455,
          "value": {
            "start": 22.842436662606573,
            "end": 24.490859926918393,
            "channel": 0,
            "labels": [
              "agent"
            ]
          },
          "id": "e0x7x",
          "from_name": "label",
          "to_name": "audio",
          "type": "labels",
          "origin": "manual"
        },
        {
          "original_length": 64.4455,
          "value": {
            "start": 25.511312423873324,
            "end": 26.767253958587087,
            "channel": 0,
            "labels": [
              "customer"
            ]
          },
          "id": "HURjg",
          "from_name": "label",
          "to_name": "audio",
          "type": "labels",
          "origin": "manual"
        },
        {
          "original_length": 64.4455,
          "value": {
            "start": 35.08786662606577,
            "end": 46.23434774665042,
            "channel": 0,
            "labels": [
              "agent"
            ]
          },
          "id": "HJcdc",
          "from_name": "label",
          "to_name": "audio",
          "type": "labels",
          "origin": "manual"
        },
        {
          "original_length": 64.4455,
          "value": {
            "start": 47.25480024360535,
            "end": 48.903223507917176,
            "channel": 0,
            "labels": [
              "customer"
            ]
          },
          "id": "Cr82U",
          "from_name": "label",
          "to_name": "audio",
          "type": "labels",
          "origin": "manual"
        },
        {
          "original_length": 64.4455,
          "value": {
            "start": 49.29570523751522,
            "end": 50.15916504263094,
            "channel": 0,
            "labels": [
              "customer"
            ]
          },
          "id": "5h_lo",
          "from_name": "label",
          "to_name": "audio",
          "type": "labels",
          "origin": "manual"
        },
        {
          "original_length": 64.4455,
          "value": {
            "start": 51.17961753958587,
            "end": 54.554960414129106,
            "channel": 0,
            "labels": [
              "agent"
            ]
          },
          "id": "06YUE",
          "from_name": "label",
          "to_name": "audio",
          "type": "labels",
          "origin": "manual"
        },
        {
          "original_length": 64.4455,
          "value": {
            "start": 55.575412911084044,
            "end": 56.674361753958586,
            "channel": 0,
            "labels": [
              "customer"
            ]
          },
          "id": "Isb74",
          "from_name": "label",
          "to_name": "audio",
          "type": "labels",
          "origin": "manual"
        },
        {
          "original_length": 64.4455,
          "value": {
            "start": 57.14533982947624,
            "end": 60.363690012180264,
            "channel": 0,
            "labels": [
              "agent"
            ]
          },
          "id": "qE-Uo",
          "from_name": "label",
          "to_name": "audio",
          "type": "labels",
          "origin": "manual"
        },
        {
          "original_length": 64.4455,
          "value": {
            "start": 61.14865347137636,
            "end": 62.87557308160779,
            "channel": 0,
            "labels": [
              "customer"
            ]
          },
          "id": "K58qW",
          "from_name": "label",
          "to_name": "audio",
          "type": "labels",
          "origin": "manual"
        },
        {
          "original_length": 64.4455,
          "value": {
            "start": 63.346551157125454,
            "end": 64.4455,
            "channel": 0,
            "labels": [
              "agent"
            ]
          },
          "id": "LYWtQ",
          "from_name": "label",
          "to_name": "audio",
          "type": "labels",
          "origin": "manual"
        }
      ],
      "was_cancelled": false,
      "ground_truth": false,
      "created_at": "2023-01-15T13:39:29.240341Z",
      "updated_at": "2023-01-15T13:39:29.240369Z",
      "lead_time": 118.577,
      "task": 155,
      "project": 3,
      "parent_prediction": null,
      "parent_annotation": null
    }
  ],
  "predictions": []
}
```
- After the required volume of data is annotated. Press `Export` on project root and download the suitable format.
	- ![[Screenshot 2023-01-15 at 7.13.52 PM.png | 600]]

## Loading the pre-annotated data
### Generating the JSONs required by LS
- Currently output returned by AWS Transcribe is treated as benchmark annotations. 
- The output of AWS-T is `.csv` but we need `.json` for LS and that too in particular format.
- Go to `audio_data_labelling/upstream.py` script.
	- Inputs: 
		- Path of AWS-T data (`.csv`)
			- Ex: `data/data/annotations/AWS_Transcribe/sos_subscription/*.csv`
		- Path of `.txt` file containing the names (+ file system info) of files submitted to LS
			- Ex: `data/data/file_list/SOSem_ls_filenames.txt`
		- Export path to `.json`'s generated (compatible to LS)
			- Ex: `data/data/annotations/LS_input/sos_subscription`
	- Output:
		- Generates all `.json` and exports to given path

### Loading pre-annotated labels to LS for review
- By this time, you should already have JSONs supported by LS.
- Next, again go to `Import` in LS UI & pick the `.json` from `/data/annotations/LS_input/sos_subscription`.
- The annotated information is stored in the same location as audio data
	- Notice the tasks from 156 onwards have `Total predictions per task` as 1.  ![[Screenshot 2023-01-16 at 10.13.13 AM.png | 600]]
	- Open Task ID 156 and notice the regions are pre-annotated.
		- ![[Screenshot 2023-01-16 at 10.17.47 AM.png | 600]]




#### Legends
LS → Label Studio
AWS-T → AWS Transcribe
