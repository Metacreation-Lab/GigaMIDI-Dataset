# GigaMIDI Dataset 

\
![GigaMIDI Logo](./Giga_MIDI_Logo_Final.png)

## Summary 
Research in artificial intelligence applications in music computing has gained significant traction with the progress of deep learning. Musical instrument digital interface (MIDI) data and its associated metadata are fundamental for the advancement of models that execute tasks such as music generation and transcription with optimal efficiency and high-performance quality. The majority of the public music datasets contain audio data, and symbolic music datasets are comparatively small. However, MIDI data presents advantages over audio, such as providing an editable version of musical content independent of its sonic rendering. MIDI data can be quantized or interpreted with variations in micro-timing and velocity, but there is only a limited amount of metadata and algorithms to differentiate expressive symbolic music data performed by a musician from non-expressive data that can be assimilated into music scores. To address these challenges, we present the GigaMIDI dataset, a comprehensive corpus comprising over 1.43M MIDI files, 5.3M tracks, and 1.8B notes, along with annotations for loops and metadata for expressive performance detection. To detect expressiveness, which tracks reflect human interpretation, we introduced a new heuristic called note onset median metric level (NOMML), which allowed us to identify with 100\% accuracy that 31\% of GigaMIDI tracks are expressive. Detecting loops, or repetitions of musical patterns, presents a challenge when tracks exhibit expressive timing variations, as repeated patterns may not be strictly identical. To address this issue, we mark MIDI loops for non-expressive music tracks, which allows us to identify 7M loops. The GigaMIDI dataset is accessible for research purposes on the Hugging Face hub [https://huggingface.co/datasets/Metacreation/GigaMIDI] in a user-friendly way for convenience and reproducibility.

## GigaMIDI Dataset Version Update 
Currently, we provide two versions of the GigaMIDI dataset: the first, in parquet format, is optimized for efficiency and integrates 
seamlessly with Python MIDI parsing libraries such as Symusic and the MIDI tokenization library MIDITok. 
The second version consists of raw MIDI files along with a CSV file. 
We are excited to announce the release of version 1.0 of the GigaMIDI dataset (released on February 7th, 2025). 
We are actively refining the dataset to improve its usability, adding new musical features, and expanding it with additional subsets. 
Stay tuned for more exciting updates coming throughout 2025!

### Citation/Reference

If you use the GigaMIDI dataset or any part of this project, please cite the following paper:
https://transactions.ismir.net/articles/10.5334/tismir.203 
```bibtex
@article{lee2025gigamidi,
  title={The GigaMIDI Dataset with Features for Expressive Music Performance Detection},
  author={Lee, Keon Ju Maverick and Ens, Jeff and Adkins, Sara and Sarmento, Pedro and Barthet, Mathieu and Pasquier, Philippe},
  journal={Transactions of the International Society for Music Information Retrieval (TISMIR)},
  volume={8},
  number={1},
  pages={1--19},
  year={2025}
}
```

## Repository Layout

[**/GigaMIDI**](./GigaMIDI): Code for creating the full GigaMIDI dataset from
source files, and README with example code for loading and processing the 
data set using the `datasets` library

[**/loops_nomml**](./loops_nomml): Source files for loop detection algorithm 
and expressive performance detection algorithm

[**/scripts**](./scripts): Scripts and code notebooks for analyzing the 
GigaMIDI dataset and the loop dataset

[**/tests**](./tests): E2E tests for expressive performance detection and 
loop extractions

[**Analysis of Evaluation Set and Optimal Threshold Selection including Machine Learning Models**](https://github.com/GigaMidiDataset/The-GigaMIDI-dataset-with-loops-and-expressive-music-performance-detection/tree/82d424ae7ff48a2fb3ce5bb07de13d5cca4fc8c5/Analysis%20of%20Evaluation%20Set%20and%20Optimal%20Threshold%20Selection%20including%20Machine%20Learning%20Models): This archive includes CSV files corresponding to our curated evaluation set, which comprises both a training set and a testing set. These files contain percentile calculations used to determine the optimal thresholds for each heuristic in expressive music performance detection. The use of percentiles from the data distribution is intended to establish clear boundaries between non-expressive and expressive tracks, based on the values of our heuristic features. Additionally, we provide pre-trained models in .pkl format, developed using features derived from our novel heuristics. The hyperparameter setup is detailed in the following section titled *Pipeline Configuration*.

[**Data Source Links for the GigaMIDI Dataset**](https://github.com/GigaMidiDataset/The-GigaMIDI-dataset-with-loops-and-expressive-music-performance-detection/blob/8acb0e5ca8ac5eb21c072ed381fa737689748c81/Data%20Source%20Links%20for%20the%20GigaMIDI%20Dataset%20-%20Sheet1.pdf): Data source links for each collected subset of the GigaMIDI dataset are all organized and uploaded in PDF. 

## Running MIDI-based Loop Detection

Included with GigaMIDI dataset is a collection of all loops identified in the 
dataset between 4 and 32 bars in length, with a minimum density of 0.5 notes 
per beat. For our purposes, we consider a segment of a track to be loopable if 
it is bookended by a repeated phrase of a minimum length (at least 2 beats 
and 4 note events)

![Loop example](./loops_nomml/loop_ex_labeled.png)

### Starter Code

To run loop detection on a single MIDI file, use the `detect_loops` function
```python
from loops_nomml import detect_loops
from symusic import Score

score = Score("tests\midi_files\Mr. Blue Sky.mid")
loops = detect_loops(score)
print(loops)
```

The output will contain all the metadata needed to locate the loop within the 
file. Start and end times are represented as MIDI ticks, and density is 
given in units of notes per beat:
```
{'track_idx': [0, 0, 0, 0, 1, 1, 2, 2, 2, 3, 3, 3, 3, 3, 4, 5], 'instrument_type': ['Piano', 'Piano', 'Piano', 'Piano', 'Piano', 'Piano', 'Piano', 'Piano', 'Piano', 'Drums', 'Drums', 'Drums', 'Drums', 'Drums', 'Piano', 'Piano'], 'start': [238080, 67200, 165120, 172800, 1920, 97920, 15360, 216960, 276480, 7680, 195840, 122880, 284160, 117120, 49920, 65280], 'end': [241920, 82560, 180480, 188160, 3840, 99840, 17280, 220800, 291840, 9600, 211200, 138240, 291840, 130560, 51840, 80640], 'duration_beats': [8.0, 32.0, 32.0, 32.0, 4.0, 4.0, 4.0, 8.0, 32.0, 4.0, 32.0, 32.0, 16.0, 28.0, 4.0, 32.0], 'note_density': [0.75, 1.84375, 0.8125, 0.8125, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 2.8125, 2.46875, 2.4375, 2.5, 0.5, 0.6875]}  
```

### Batch Processing Loops

We also provide a script, `main.py` that batch extracts all loops in a 
dataset. This requires that you have downloaded GigaMIDI, see the [dataset README](./GigaMIDI/README.md) for instructions on doing this. Once you have the dataset downloaded, update the `DATA_PATH` and `METADATA_NAME` globals to reflect the location of GigaMIDI on your machine and run the script:

```python
python main.py
```


## Instruction for using the code for note onset median metric level (NOMML) heuristic
### Install and import Python libraries for the NOMML code: <br /> 
Imported libraries: 
```
pip install numpy tqdm symusic
```
<br />
Note: symusic library is used for MIDI parsing.

### Using with the command line  <br />
usage: 
```python
python nomml.py [-h] --folder FOLDER [--force] [--nthreads NTHREADS]
```
<br />
Note: If you run the code succesfully, it will generate .JSON file with appropriate metadata.

The metadata median metric depth corresponds to the Note Onset Median Metric Level (NOMML). 
Please refer to the latest information below, as this repository was intended to be temporary during the anonymous peer-review process 
of the academic paper. Based on our experiments, tracks at levels 0-11 can be classified as non-expressive, while level 12 indicates 
expressive MIDI tracks. Note that this classification applies at the track level, not the file level.

## Pipeline Configuration

The following pipeline configuration was determined through hyperparameter tuning using leave-one-out cross-validation and GridSearchCV for the logistic regression model:

```python
# Hyperparameters
{'C': 0.046415888336127774}

# Logistic Regression Instance
LogisticRegression(random_state=0, C=0.046415888336127774, max_iter=10000, tol=0.1)

# Pipeline
Pipeline(steps=[('scaler', StandardScaler(with_std=False)),
                ('logistic',
                 LogisticRegression(C=0.046415888336127774, max_iter=10000,
                                    tol=0.1))])
```

## Acknowledgement
We gratefully acknowledge the support and contributions that have directly or indirectly aided this research. This work was supported in part by funding from the Natural Sciences and Engineering Research Council of Canada (NSERC) and the Social Sciences and Humanities Research Council of Canada (SSHRC). We also extend our gratitude to the School of Interactive Arts and Technology (SIAT) at Simon Fraser University (SFU) for providing resources and an enriching research environment. Additionally, we thank the Centre for Digital Music (C4DM) at Queen Mary University of London (QMUL) for fostering collaborative opportunities and supporting our engagement with interdisciplinary research initiatives.  

Special thanks are extended to Dr. Cale Plut for his meticulous manual curation of musical styles and to Dr. Nathan Fradet for his invaluable assistance in developing the HuggingFace Hub website for the GigaMIDI dataset, ensuring it is accessible and user-friendly for music computing and MIR researchers. We also sincerely thank our research interns, Paul Triana and Davide Rizotti, for their thorough proofreading of the manuscript.  

Finally, we express our heartfelt appreciation to the individuals and communities who generously shared their MIDI files for research purposes. Their contributions have been instrumental in advancing this work and fostering collaborative knowledge in the field.
