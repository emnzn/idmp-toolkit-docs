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

## Collaboration Principles

To keep development smooth and conflict-free, we follow these practices:

- **Work only on feature branches**  
  Nobody commits directly to `main`. Every change starts from a branch (see [Branching Convention](#branching-convention)).

- **Respect ownership of functions**  
  Contributors may work in the same file, but do not edit or overwrite someone else’s function without discussion.

- **Conflicts are expected, but manageable**  
  Merge conflicts will happen when multiple people branch from the same point and edit the same file. As long as contributors work on separate functions, any conflicts will be line-based and trivial to resolve.

- **Small, modular changes**  
  Keep pull requests focused. Small changes are easier to review and reduce the chance of large, messy conflicts.

---

## Module Organization

```text
idmp/
├── preprocess/
│   ├── __init__.py
│   ├── mask.py                 # Rule-based + model-based tissue masking
│   ├── iam.py                  # Image assessment methods (variation, quality, etc.)
│   ├── patch.py                # Sliding window and random patch sampling
│   └── wsi_constructor.py      # Reconstruct WSIs from patches + coordinates
│
├── visualization/
│   └── annotation_stats.py     # Number of slides/class, annotation counts, area stats
│
├── data/
│   ├── __init__.py
│   ├── split.py                # Dataset splitting (train/val/test)
│   └── qc.py                   # Quality control functions
│
└── models/                     # Model initialization scripts
    ├── foundation.py           # Foundation models
    ├── segmentation/           # Segmentation models
    │   ├── glands.py           # Gland segmentation models
    │   └── tissue.py           # Tissue segmentation models
    └── classification/         # Classification models

```

To keep the function library clean and maintainable, instead of placing everything into a single large file like `preprocess.py`, we break down functionality by **sub-tasks**. Each sub-task lives in its own module (`mask.py`, `patch.py`, etc.), while the package `__init__.py` collects these into a stable public API.  

This structure makes it easier to:

- **Maintain**  
  Smaller files are easier to read, test, and refactor.  

- **Collaborate**  
  Less contributors working on the same file, minimizing conflicts.

- **Extend**  
  New features can be added in a more organized manner, and it will be easier to find existing functions.

- **Expose a clean API**  
  With `__init__.py` users can import functions seamlessly without worrying about internal layout. 

### Example `__init__.py`

```python
# idmp/preprocess/__init__.py

from .iam import match_histogram
from .wsi_constructor import WSIConstructor
from .patch import sliding_window, random_patch
from .mask import threshold_segmenter, model_segmenter

__all__ = [
    "threshold_segmenter",
    "model_segmenter",
    "match_histogram",
    "sliding_window",
    "random_patch",
    "WSIConstructor",
]
```

### Simplified Imports

Without `__init__.py`:

```python
from idmp.iam import match_histogram
from idmp.preprocess.patch import sliding_window
from idmp.preprocess.mask import threshold_segmenter
from idmp.preprocess.wsi_constructor import WSIConstructor
```

With `__init__.py`:

```python
from idmp.preprocess import (
    match_histogram,
    sliding_window,
    threshold_segmenter, 
    WSIConstructor
)
```

Users only interact with a **flat, stable API**, while contributors can keep adding new files without bloating imports.

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
