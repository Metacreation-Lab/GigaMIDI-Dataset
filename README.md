# GigaMIDI Dataset 
## The Extended GigaMIDI Dataset with Music Loop Detection and Expressive Multitrack Loop Generation
\
![GigaMIDI Logo](./Giga_MIDI_Logo_Final.png)

## The extended GigaMIDI Dataset Summary 
We present the extended GigaMIDI dataset, a large-scale symbolic music collection comprising over 2.1 million unique MIDI files with detailed annotations for music loop detection. Expanding on its predecessor, this release introduces a novel expressive loop detection method that captures performance nuances such as microtiming and dynamic variation, essential for advanced generative music modelling. Our method extends previous approaches, which were limited to strictly quantized, non-expressive tracks, by employing the Note Onset Median Metric Level (NOMML) heuristic to distinguish expressive from non-expressive material. This enables robust loop detection across a broader spectrum of MIDI data. Our loop detection method reveals more than 9.2 million non-expressive loops spanning all General MIDI instruments, alongside 2.3 million expressive loops identified through our new method. As the largest resource of its kind, the extended GigaMIDI dataset provides a strong foundation for developing models that synthesize structurally coherent and expressively rich musical loops. As a use case, we leverage this dataset to train an expressive multitrack symbolic music loop generation model using the MIDI-GPT system, resulting in the creation of a synthetic loop dataset.

## GigaMIDI Dataset Version Update 

We present the extended GigaMIDI dataset (select v2.0.0), a large-scale symbolic music collection comprising over 2.1 million unique MIDI files with detailed annotations for music loop detection. Expanding on its predecessor, this release introduces a novel expressive loop detection method that captures performance nuances such as microtiming and dynamic variation, essential for advanced generative music modelling. Our method extends previous approaches, which were limited to strictly quantized, non-expressive tracks, by employing the Note Onset Median Metric Level (NOMML) heuristic to distinguish expressive from non-expressive material. This enables robust loop detection across a broader spectrum of MIDI data. Our loop detection method reveals more than 9.2 million non-expressive loops spanning all General MIDI instruments, alongside 2.3 million expressive loops identified through our new method. As the largest resource of its kind, the extended GigaMIDI dataset provides a strong foundation for developing models that synthesize structurally coherent and expressively rich musical loops. As a use case, we leverage this dataset to train an expressive multitrack symbolic music loop generation model using the MIDI-GPT system, resulting in the creation of a synthetic loop dataset. The GigaMIDI dataset is accessible for research purposes on the Hugging Face hub [https://huggingface.co/datasets/Metacreation/GigaMIDI] in a user-friendly way for convenience and reproducibility. 


For the Hugging Face Hub dataset release, we disclaim any responsibility for the misuse of this dataset.
The subset version `v2.0.0` refers specifically to the extended GigaMIDI dataset, while version `v2.0.0` denotes the original GigaMIDI dataset (see [Lee et al., 2025](https://doi.org/10.5334/tismir.203)).
New users must request access via our Hugging Face Hub page before retrieving the dataset from the following link:
[https://huggingface.co/datasets/Metacreation/GigaMIDI/viewer/v2.0.0](https://huggingface.co/datasets/Metacreation/GigaMIDI/viewer/v2.0.0)

### Dataset Curators

Main curator: Keon Ju Maverick Lee 

Assistance: Jeff Ens, Sara Adkins, Nathan Fradet, Pedro Sarmento, Mathieu Barthet, Phillip Long, Paul Triana

Research Director: Philippe Pasquier

GigaMIDI Graphic Logo Design: Arshia Sobhan

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

[**/loops_nomml**](./loops_nomml): Source files for non-expressive loop detection algorithm 
and expressive performance detection algorithm

[**Expressive Loop Detector**](Expressive%20music%20loop%20detector-NOMML12.ipynb): code for the expressive loop detection method (in the .ipynb file) and instructions are available in the later section of this readme file. 

[**Expressive Loop Generation**](https://github.com/Metacreation-Lab/GigaMIDI-Dataset/tree/main/MIDI-GPT-Loop): code for the expressive loop generation and instructions are available in the hyperlink which connects to the readme file. 

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

# Expressive Loop Detection Pipeline

This repository contains a highly parallelized Python pipeline for detecting both **non-expressive** (hard-match) and **expressive** (soft-count) loops in large MIDI datasets. Built on the GigaMIDI `loops_nomml` library and Symusic for MIDI parsing, it uses `joblib` to distribute work across all available CPU cores. The workflow periodically writes out **checkpoint** CSVs (every 500 000 files) and produces a final, aggregated result. The code is available in the file named Expressive music loop detector-NOMML12.ipynb.

---

## 🚀 Key Features

- **Hard-match detection**  
  Finds exact, bar-aligned pitch-set repeats.  
- **Soft-count detection**  
  Captures expressive loops by combining pitch overlap, velocity similarity, and micro-timing tolerance.  
- **Loopability scoring**  
  A single metric blending the length of the longest repeat with overall repetition density.  
- **Scalable batch processing**  
  Checkpoints output every 500 000 files (`loops_checkpoint_1.csv`, `loops_checkpoint_2.csv`, …).  
- **Full parallelization**  
  Leverages `joblib.Parallel` to utilize all CPU cores for maximum throughput.

---

## 🔧 Prerequisites

- **Python 3.8+**  
- **Symusic** for MIDI I/O  
- **GigaMIDI `loops_nomml`** module (place alongside this repo)  
- Install required packages:
  ```bash
  pip install numpy pandas joblib tqdm


---

## 📦 Installation

1. Clone this repo alongside your local `loops_nomml` checkout:

   ```bash
   git clone https://github.com/YourUser/expressive-loop-detect.git
   cd expressive-loop-detect
   ```
2. (Optional) Create & activate a virtual environment:

   ```bash
   python3 -m venv venv
   source venv/bin/activate
   pip install -r requirements.txt
   ```

---

## ⚙️ Usage

1. **Prepare your CSV**
   Place your input CSV (default name:
   `Final_GigaMIDI_Loop_V2_path-instrument-NOMML-type.csv`) in the working directory. It must have:

   | Column      | Description                                                       |
   | ----------- | ----------------------------------------------------------------- |
   | `file_path` | Path to each `.mid` or `.midi` file                               |
   | `NOMML`     | Python list of per-track expressiveness flags (e.g. `[12,2,4,…]`) |

2. **Configure parameters**
   At the top of `detect_loops.py`, you can adjust:

   * `melodic_tau`  (default `0.3`): similarity threshold for melodic tracks
   * `drum_tau`     (default `0.1`): threshold for drum tracks
   * `chunk_size`   (default `500_000`): number of files per checkpoint
   * Bars are quantized every 4 beats; min/max loop lengths and density filters live in the `get_valid_loops` call.

3. **Run the detector**

   ```bash
   python detect_loops.py
   ```

   Or open the Jupyter notebook:

   ```bash
   jupyter notebook detect_loops.ipynb
   ```

   The script will:

   * Read the CSV of file paths
   * Process MIDI files in parallel, chunk by chunk
   * Save checkpoint CSVs named `loops_checkpoint_1.csv`, `loops_checkpoint_2.csv`, …
   * After all chunks, combine results into one DataFrame and save `loops_full_output.csv`

---

## 📊 Output

* **Checkpoint CSVs** (`loops_checkpoint_<n>.csv`): one row per MIDI file in that chunk, with columns:

  * `file_path`
  * `track_idx` (list of track indices with detected loops)
  * `MIDI program number` (list)
  * `instrument_group` (list of GM groups or “Drums”)
  * `loopability` (list of floats)
  * `start_tick`, `end_tick` (lists of integers)
  * `duration_beats` (list of floats)
  * `note_density` (list of floats)

* **Full output** (`loops_full_output.csv`): concatenation of all checkpoint rows.

**Example to load with correct list parsing**:

```python
import pandas as pd

converters = { 
  'track_idx': eval,
  'MIDI program number': eval,
  'instrument_group': eval,
  'loopability': eval,
  'start_tick': eval,
  'end_tick': eval,
  'duration_beats': eval,
  'note_density': eval
}

df = pd.read_csv("loops_full_output.csv", converters=converters)
```
| Column                   | Type           | Description                                 |
| ------------------------ | -------------- | ------------------------------------------- |
| `file_path`              | string         | Original MIDI filepath                      |
| `track_idx`              | list of ints   | Indices of tracks where loops were detected |
| `MIDI program number`    | list of ints   | Corresponding MIDI program codes            |
| `instrument_group`       | list of strs   | GM group (or “Drums”) for each loop         |
| `loopability`            | list of floats | Loopability score per detected loop         |
| `start_tick`, `end_tick` | list of ints   | Loop boundaries (MIDI ticks)                |
| `duration_beats`         | list of floats | Loop lengths in beats                       |
| `note_density`           | list of floats | Active-notes-per-beat density per loop      |

---

## 📝 Troubleshooting

* **No loops found**: try lowering `melodic_tau` or relaxing `min_rep_beats`/`min_beats`.
* **`IndexError` in beat-duration**: the script patches `get_duration_beats` for safety.
* **Performance issues**: set `n_jobs` in `Parallel(...)` to fewer cores or reduce `chunk_size`.

---

# Expressive Loop Generation

Information about loop generation is found at `MIDI-GPT-Loop/README.md`

---

## 🤝 Contributing

1. Fork this repo
2. Create a feature branch
3. Submit a pull request

---




## Acknowledgement
We gratefully acknowledge the support and contributions that have directly or indirectly aided this research. This work was supported in part by funding from the Natural Sciences and Engineering Research Council of Canada (NSERC) and the Social Sciences and Humanities Research Council of Canada (SSHRC). We also extend our gratitude to the School of Interactive Arts and Technology (SIAT) at Simon Fraser University (SFU) for providing resources and an enriching research environment. Additionally, we thank the Centre for Digital Music (C4DM) at Queen Mary University of London (QMUL) for fostering collaborative opportunities and supporting our engagement with interdisciplinary research initiatives.  

Special thanks are extended to Dr. Cale Plut for his meticulous manual curation of musical styles and to Dr. Nathan Fradet for his invaluable assistance in developing the HuggingFace Hub website for the GigaMIDI dataset, ensuring it is accessible and user-friendly for music computing and MIR researchers. We also sincerely thank our research interns, Paul Triana and Davide Rizotti, for their thorough proofreading of the manuscript.  

Finally, we express our heartfelt appreciation to the individuals and communities who generously shared their MIDI files for research purposes. Their contributions have been instrumental in advancing this work and fostering collaborative knowledge in the field.
