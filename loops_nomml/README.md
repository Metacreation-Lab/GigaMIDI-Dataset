# Fast loops extractor (CPU/GPU accelerated)

## Requirements

```sh
pip install numba
pip install numpy==1.24.4
pip install pretty-midi
pip install symusic
pip install miditok
```

## Basic use example

```python
import os

# Set desired environment variable
os.environ["USE_NUMBA"] = "1"
os.environ["USE_CUDA"] = "1"

# Check the variable
print(os.environ["USE_NUMBA"])
print(os.environ["USE_CUDA"])

from process_file_fast import detect_loops_from_path

midi_file = './your_midi_file.mid'

result = detect_loops_from_path({'file_path': [midi_file]})
```

## Enjoy! :)
