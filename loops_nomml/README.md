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
from process_file_fast import detect_loops_from_path

midi_file = './your_midi_file.mid'

result = detect_loops_from_path({'file_path': [midi_file]})
```

## Enjoy! :)
