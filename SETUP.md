# Setup

Three things to get right: where the data folder sits, the Python environment, and —
only if you want to recompute — a local Ollama installation.

---

## 1. Where the data goes

The notebooks expect their data in a folder called `synthetic_dataset`, **next to**
`notebooks/`:

```
your-clone/
├── notebooks/
│   ├── 01_the_data.ipynb … 06_the_pipeline.ipynb
│   └── PROZESS.md
└── synthetic_dataset/
    ├── synthetic/
    │   ├── synthetic_data_transformed.csv          the dataset
    │   ├── synthetic_comments_checkpoint.jsonl     ┐ cached language-model results,
    │   ├── synthetic_valence_checkpoint.jsonl      │ so the chain runs without Ollama
    │   └── synthetic_topic_labels.parquet          ┘
    └── config/
        ├── german_stopwords_full.txt
        └── german_stopwords_extended.txt
```

Every notebook sets `DATA = Path("../synthetic_dataset")` in its first cell. If you put
the folder elsewhere or rename it, change that one line.

### Two files are not in this repository

Notebook **03** needs two published affective word lists:

```
synthetic_dataset/config/ims_affective_norms.parquet
synthetic_dataset/config/bawl_r.parquet
```

They are research data with their own terms of use and are therefore not redistributed
here. Sources and citations are in [REFERENCES.md](REFERENCES.md) under *Affective word
norms*; download them there and place them as shown above.

Without them, notebook 03 stops at the cell *Build the criterion and the referee*.
Notebooks 01, 02 and 04 to 06 are unaffected — notebook 05 uses only the stopword list
from that folder, and that one is included.

---

## 2. Python environment

Fork the repo and set up a new virtual environment. The
[requirements file](requirements.txt) contains all libraries the notebooks need.

**`Note:`**

- If there are errors during environment setup, try removing the version pins from
  the failing packages in the requirements file.
- In some cases it is necessary to install the **Rust** compiler, because the
  `tokenizers` package used by `transformers` builds from source when no matching
  wheel is available.

- Check the **rustup version**  by run the following commands:
    ```sh
    rustup --version
    ```
    If you haven't installed it yet, begin at `step_1`. Otherwise, proceed to `step_2`.


### **`macOS`** type the following commands : 

- `Step_1:` Update Homebrew and install **rustup** by following commands:

    ```BASH
    brew install rustup
    rustup-init -y
    ```
    Then press ```1``` for the standard installation.

  Restart Your Terminal and then check the **rustup version**  by running the following commands:
     ```sh
    rustup --version
    ```
 
- `Step_2:` Install the virtual environment and the required packages by following commands:

    ```BASH
    pyenv local 3.11.3
    python -m venv .venv
    source .venv/bin/activate
    pip install --upgrade pip
    pip install -r requirements.txt
    ```

    
### **`WindowsOS`** type the following commands :

- `Step_1:` Install **rustup**  by following :
  
  1. Visit the official Rust website: https://www.rust-lang.org/tools/install.
  2. Download and run the `rustup-init.exe` installer.
  3. Follow the on-screen instructions and choose the default options for a standard installation.
  4. Then press ```1``` for the standard installation.
 
    Restart Your Terminal and then check the **rustup version**  by running the following commands:
  
     ```sh
    rustup --version
    ```     


- `Step_2:` Install the virtual environment and the required packages by following commands.

   For `PowerShell` CLI :

    ```PowerShell
    pyenv local 3.11.3
    python -m venv .venv
    .venv\Scripts\Activate.ps1
    python -m pip install --upgrade pip
    pip install -r requirements.txt
    ```

    For `Git-bash` CLI :
  
    ```BASH
    pyenv local 3.11.3
    python -m venv .venv
    source .venv/Scripts/activate
    python -m pip install --upgrade pip
    pip install -r requirements.txt
    ```


---

## 3. Ollama — only if you want to recompute

Notebooks 02 and 05 have their language-model results cached in the data folder, so
**the chain runs end to end without Ollama.** You need it only to recompute those
steps: valence in notebook 02, and the topic names in notebook 05.

Delete the corresponding cache file, or set `RECOMPUTE = True` in the notebook's first
cell, and the notebook will call the model again.

1. Install Ollama from [ollama.com](https://ollama.com).
2. Pull the model the notebooks use:

    ```BASH
    ollama pull gemma2:9b
    ```

3. Check that the service answers:

    ```BASH
    curl http://127.0.0.1:11434/api/tags
    ```

`gemma2:9b` needs roughly 6 GB of memory while loaded. Keep only one model resident at
a time — two large models at once push part of the computation onto the CPU and slow
generation down by about a factor of three.

The models run locally on purpose: no comment text leaves the machine.

---

## What runs how long

Notebook 05 fits its own topic model, which takes a minute or two. Everything else is
seconds to a minute. The Hugging Face models — the German sentiment classifier, the
emotion classifier and the sentence embeddings — download themselves on first use.
