# Contribute to **IDMPToolkit**

Please follow our **branching** and **naming conventions** to keep the repository clean and maintainable.

---

## Poetry Installation

We use [**Poetry**](https://python-poetry.org/) to manage dependencies and environments.  

Installing Poetry is highly recommended for a seamless management of dependencies for our library. Please refer to the link above for installation instructions.

---

## Dependency Management

When developing functions that rely on dependencies that are **not** yet in `idmp-toolkit`, instead of performing an installation via `pip install <package-name>` or `conda install <package-name>`, please perform the installation as follows:

```bash
poetry add <package-name>
```

!!! note

    This command must be executed from the project root (the folder containing `pyproject.toml`). It will install the dependency into your environment and automatically list it as a dependency for `idmp-toolkit`, preventing dependency errors in future installations.

---

## Branching Convention

All work should be done in a **feature branch**, not directly on `main`. This ensures that `main` always remains stable and ready for release.

### Branch naming convention

```bash
# Branch naming convention:
feature/<module>-<task>-<function>-<contributor_name>
```
Examples:

- `feature/preprocess-mask-eric`
- `feature/preprocess-patch-yijing`
- `feature/models-segmentation-chengxiang`
- `feature/data-split-haoda`

This format makes it easy to see what is being developed in each branch at a glance.

---

## Creating a Branch
Always start from the latest `main` branch:

```bash
# Update local main
git checkout main
git pull origin main

# Create a feature branch
git checkout -b feature/<task>-<function>-<contributor-name>
git push -u origin feature/<task>-<function>-<contributor-name>
```

---

## Pushing to a Branch
```bash
git add .
git commit -m "Add rule-based tissue masking"
git push -u origin feature/preprocess-mask-<contributor-name>
```

---

## Code Convention

### Typing
Always use Python type hints for function signatures and class methods.

```py
def threshold_segmenter(
    slide: np.ndarray,
    apply_morphological_closing: bool = True,
    ksize: int = 3
    ) -> np.ndarray:

    ...
```

Specifying the expected input and return types makes functions easier to use and maintain, as future modifications only need to preserve these types to prevent errors.

### Docstrings
Document every function, and class using NumPy-style docstrings.
Clear docstrings describe what a function does, explain its parameters and return values, and make the codebase easier to use and extend.

```py
def threshold_segmenter(
    slide: np.ndarray,
    apply_morphological_closing: bool = True,
    ksize: int = 3
    ) -> np.ndarray:

    """
    Segments tissue regions by thresholding the saturation channel 
    with otsu.

    Parameters
    ----------
    slide: np.ndarray
        The image as a numpy array.
        The image is assumed to be in RGB.

    apply_morphological_closing: bool
        Whether to close small gaps in the tissue

    ksize: 
        The kernel size for blurring and morphological closing.
        Must be odd.

    Returns
    -------
    tissue_mask: np.ndarray
        The segmented tissue mask.
    """

    ...

    return tissue_mask
```

Please refer to this [**guide**](https://sphinxcontrib-napoleon.readthedocs.io/en/latest/example_numpy.html#example-numpy) for more details.

---

## Avoiding Branch Conflicts

Our library is developed by multiple contributors, often working on similar tasks in different ways.  

For example:  

- **Preprocess**
  - `mask`: rule-based and model-based tissue masking (LJ, XM, EM, HD)  
  - `iam`: image augmentation methods (XM, HD)  
  - `patch`: sliding window and random patching (XM, HD, ER, ZR)  
  - `wsi_constructor`: reconstruct WSIs from patches (XM, ER, HD, ZR)  

- **Visualization**
  - `annotation_stats`: number of slides per class, annotations, and area stats (KH, JA, SY)  

- **Data**
  - `split`: data splitting functions (EM, HD)  
  - `qc`: quality control checks (LJ)  

- **Models**
  - `foundation`, `segmentation`, `classification`  

If everyone edited a single `preprocess.py` or `models.py` file, merge conflicts would happen frequently. The codebase would also become harder to maintain and refactor over time.

### The Role of `__init__.py`

Instead of forcing everything into one large file, each group of functions lives in its own submodule (`mask.py`, `patch.py`, `split.py`, etc.).  

`__init__.py` then **fuses these submodules into a clean public API**.  

### Benefits
- **Conflict reduction** – contributors work in separate files, avoiding merge conflicts.  
- **Stable API surface** – users import from `idmp.preprocess` or `idmp.data` without worrying about internal file layout.  
- **Future flexibility** – we can reorganize files as the library grows, while keeping the same import paths for users.  
- **Cleaner organization** – related functionality is grouped into smaller, focused files.  

### Example Folder Layout

```text
idmp/
├── preprocess/
│   ├── __init__.py
│   ├── mask.py
│   ├── iam.py
│   ├── patch.py
│   └── wsi_constructor.py
├── models/
│   ├── __init__.py
│   ├── foundation.py
│   └── segmentation.py
```

### Example `__init__.py`

```python
# idmp/preprocess/__init__.py

from .iam import match_histogram
from .wsi_constructor import reconstruct_wsi
from .patch import sliding_window, random_patch
from .mask import threshold_segmenter, model_segmenter

__all__ = [
    "threshold_segmenter",
    "model_segmenter",
    "match_histogram",
    "sliding_window",
    "random_patch",
    "reconstruct_wsi",
]
```

### Simplified Imports

Without `__init__.py`:

```python
from idmp.preprocess.mask import rule_based_mask
from idmp.preprocess.patch import sliding_window
```

With `__init__.py`:

```python
from idmp.preprocess import rule_based_mask, sliding_window
```

Users only interact with a **flat, stable API**, while contributors can keep adding new files without breaking imports.  

---

This way, contributors can focus on their feature, while the library maintains a clean, conflict-resistant structure.
