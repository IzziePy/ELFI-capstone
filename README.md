# ELFI – Emotional & Latent Feedback Interpretation

![ELFI – Visula](./images/elfi_visual_wide.png)

> **Abstract:** Within the scope of this project, a solution for the sentiment and cluster analysis of **German-language** free-text responses from employee surveys is being developed. The goal is to systematically evaluate qualitative feedback and generate actionable insights.
>
> The solution is based on modern Natural Language Processing (NLP) methods and is visualized in Power BI as a dashboard. Free texts are read from a defined data source, analyzed, and the results are written back into a separate target table. The analysis covers both the emotional tone (sentiment) and the thematic grouping (clustering) of the texts.
>
> The technical implementation is designed to be scalable for a data volume of approximately 500 free-text responses per survey wave. The project contributes to data-driven organizational development and supports managers in interpreting employee feedback. In the long term, the solution is to be automated and serve as a reusable analysis module for other survey formats.

---

## 📑 Table of Contents
* [Project Description](project_setup/project_description.md)
* [Project Goals](project_setup/goals.md)
* [Decision Log](project_setup/decision_log.md)

* [Overview German Sentiment Models](project_setup/german_sentiment_models.md)

*Theoretical Foundation*

* [Psychological Emotional Models](project_setup/emotional_models.md)

---

## 🛠️ Set up your Environment

Please make sure you have forked the repo and set up a new virtual environment.
The added [requirements file](requirements.txt) contains all libraries and dependencies we need to execute the NLP notebooks.

**`Note:`**

- If there are errors during environment setup, try removing the versions from the failing packages in the requirements file. silicon shizzle.
- In some cases it is necessary to install the **Rust** compiler for the transformers library.
- make sure to install **hdf5** if you haven't done it before.

 - Check the **rustup version**  by run the following commands:
    ```sh
    rustup --version
    ```
    If you haven't installed it yet, begin at `step_1`. Otherwise, proceed to `step_2`.


### **`macOS`** type the following commands : 

- `Step_1:` Update Homebrew and install **rustup** and **hdf5** by following commands:

    ```BASH
    brew install rustup
    rustup-init -y
    ```
    Then press ```1``` for the standard installation.
    
    Then we can go on to install hdf5:
    
    ```BASH
     brew install hdf5
    ```

  Restart Your Terminal and then check the **rustup version**  by running the following commands:
     ```sh
    rustup --version
    ```
 
- `Step_2:` Install the virtual environment and the required packages by following commands:

  > NOTE: for macOS with **silicon** chips (other than intel)
    ```BASH
    pyenv local 3.11.3
    python -m venv .venv
    source .venv/bin/activate
    pip install --upgrade pip
    pip install -r requirements_silicon.txt
    ```
  > NOTE: for macOS with **intel** chips
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

    Next, we’ll install **HDF5** (if needed).

    HDF5 may already be installed from earlier exercises, so **first check whether it’s available** by running this in your terminal:

    ```sh
    h5dump -V    # Expected output: h5dump 1.14.6 (or similar)
    ```

    - If you see a version output: **HDF5 is installed → you can skip the installation.**
    - If you get an error like **"command not found"**: install it via the HDF Group website:

      - Visit: [install hdf5](https://www.hdfgroup.org/download-hdf5/)
      - Create an account
      - Download and install the **Pre-built Binary Distribution for Windows**

    After installation, open a **new terminal** and run the command above again to confirm HDF5 is installed.



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
