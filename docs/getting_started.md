# Getting started with **IDMPToolkit**

## 1. Setup SSH Keys with Gitea
To install the package, make sure your SSH keys are configured with the lab's Git server. For a step-by-step guide, refer to the [**GitHub documentation**](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/adding-a-new-ssh-key-to-your-github-account?platform=mac).

Once setup, you can test your connection:

```bash
ssh -T git@git.cdplab.org
```

---

## 2. Package Installation
Create a virtual environment:

```bash
conda create -n idmp python=3.11
```

Enter virtual environment:

```bash
conda activate idmp
```

You can install the package directly from the lab’s private Git server:

```bash
pip install git+ssh://git@git.cdplab.org/ericmonzon/idmp-toolkit.git
```