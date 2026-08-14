# Cats & Dogs OpenShift AI Workshop Setup

This guide creates a dedicated Python virtual environment for the Cats vs Dogs notebook and registers it as a Jupyter kernel.

## 1. Create the workshop directory

```bash
mkdir workshop
```

## 2. Change into the workshop directory

```bash
cd workshop
```

## 3. Clone the workshop repository

```bash
https://github.com/mohtork/cats_vs_dogs_demo.git
```

## 4. Create a new virtual environment

```bash
VENV="$HOME/cats-dogs-env"

python3 -m venv "$VENV"
```

## 5. Upgrade pip

```bash
"$VENV/bin/pip" install --upgrade pip
```

## 6. Install PyTorch and Torchvision with CUDA 12.4 support

```bash
"$VENV/bin/pip" install \
  "torch==2.6.0+cu124" \
  "torchvision==0.21.0+cu124" \
  --index-url https://pypi.org/simple \
  --extra-index-url https://download.pytorch.org/whl/cu124
```

## 7. Install the remaining notebook dependencies

```bash
"$VENV/bin/pip" install \
  kagglehub \
  matplotlib \
  pillow \
  numpy \
  ipykernel
```

## 8. Verify PyTorch and CUDA

```bash
"$VENV/bin/python" - <<'PY'
import torch

print("PyTorch:", torch.__version__)
print("CUDA available:", torch.cuda.is_available())
print("CUDA version:", torch.version.cuda)

if torch.cuda.is_available():
    print("GPU:", torch.cuda.get_device_name(0))
PY
```

Expected output should look similar to:

```text
PyTorch: 2.6.0+cu124
CUDA available: True
CUDA version: 12.4
GPU: NVIDIA H100 80GB HBM3 MIG 1g.10gb
```

## 9. Register the virtual environment as a Jupyter kernel

```bash
"$VENV/bin/python" -m ipykernel install \
  --user \
  --name cats-dogs-env \
  --display-name "Cats & Dogs (OpenShift AI)"
```

## 10. Verify the Jupyter kernel registration

```bash
jupyter kernelspec list
```

You should see an entry similar to:

```text
cats-dogs-env    /opt/app-root/src/.local/share/jupyter/kernels/cats-dogs-env
```

## 11. Select the kernel in JupyterLab

Open the Cats vs Dogs notebook and select:

```text
Kernel → Change Kernel → Cats & Dogs (OpenShift AI)
```

## 12. Verify the active environment inside the notebook

Run this cell:

```python
import sys
import torch
import torchvision

print("Python:", sys.executable)
print("PyTorch:", torch.__version__)
print("Torchvision:", torchvision.__version__)
print("CUDA:", torch.cuda.is_available())

if torch.cuda.is_available():
    print("GPU:", torch.cuda.get_device_name(0))
```

The Python path should be:

```text
/opt/app-root/src/cats-dogs-env/bin/python
```

This confirms that the notebook is running inside the dedicated Cats & Dogs virtual environment.

## 13. Cleanup after the workshop

Before removing the environment, make sure no notebook is currently using the **Cats & Dogs (OpenShift AI)** kernel.

### Unregister the Jupyter kernel

```bash
jupyter kernelspec remove cats-dogs-env
```

When prompted, confirm the removal.

You can verify that the kernel has been removed with:

```bash
jupyter kernelspec list
```

### Delete the virtual environment

```bash
rm -rf "$HOME/cats-dogs-env"
```

Verify that the virtual environment no longer exists:

```bash
ls -ld "$HOME/cats-dogs-env"
```

You should see a message similar to:

```text
ls: cannot access '/opt/app-root/src/cats-dogs-env': No such file or directory
```

At this point, the dedicated Cats & Dogs virtual environment and its Jupyter kernel registration have both been removed.

