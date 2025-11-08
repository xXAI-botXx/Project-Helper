# Remote ML Workflow & GPU Management

[<img align="right" width=150px src='../res/rackete_2.png'></img>](../README.md)

This file tries to help out for remote control a compute device (server).



Table of Contents:

- [Helpful Bash-Commands](#helpful-bash-commands)
- [Create SSH Connection](#create-ssh-connection)
- [Start SSH Connection](#start-ssh-connection)
- [Data Transfer](#data-transfer)
- [Wake on Lan](#wake-on-lan)
- [GPU Healthcare (Nvidia)](#gpu-healthcare)
- [PyTorch CUDA Version Support Check](#pytorch-cuda-version-support-check)
- [PyTorch](#pytorch)
- [TensorFlow](#tensorflow)
- [Run Python-Script in Background](#run-python-script-in-background)
- [Run ipynb in Background](#run-ipynb-in-background)
- [Check if Script is Running](#check-if-script-is-running)
- [Start Tensorboard](#start-tensorboard)
- [Start MLFlow UI](#start-mlflow-ui)
- [GUI Sharing](#gui-sharing)

  


> click the **rocket** for going **back**





---

### Helpful Bash-Commands



Linux:
- `mkdir` – Create a new directory
- `rm` – Remove a file
- `rm -rf` – Forcefully remove a directory and its contents
- `nano` – Open a simple text editor
- `cat` – Display the contents of a file
- `ls` – List files in a directory
- `cd` – Change directory
- `pwd` – Print the current working directory
- `cp` – Copy files and directories
- `mv` – Move or rename files and directories
- `chmod` – Change file permissions
- `chown` – Change file ownership
- `grep` – Search for text within files
- `find` – Search for files and directories
- `tar` – Compress and extract archive files
-  `zip` / `unzip` – Compress and extract ZIP files
- `wget` – Download files from the web
- `curl` – Transfer data from or to a server
- `ps` – Display currently running processes
- `top` – Show real-time system processes
- `kill` – Terminate a process
- `htop` – Interactive process viewer (if installed)
- `df -h` – Show disk space usage
- `du -sh` – Show size of a directory
- `free -h` – Display memory usage
- `ifconfig` / `ip a` – Show network interfaces
- `ping` – Test network connectivity
- `sudo` – Execute a command as another user (usually root)

more helpful commands:
- Find most large files: 
   ```bash
   du -h ~ --max-depth=1 | sort -hr | head -n 20
   ```
- Kill all docker container:
   ```bash
   docker kill $(docker ps -q)
   ```
- Kill old/stopped docker container:
   ```bash
   docker container prune
   ```
- Show all running python runs from a specific file
   ```bash
   ps aux | grep multiprocessing.py
   ```
- Kill all running python runs from a specific file
   ```bash
   ps aux | grep multiprocessing.py | grep -v grep | awk '{print $2}' | xargs kill -9
   ```

> If your process (training or data generation) failed without a good reason, then you might want to check you free memory space with 'df -h'. I often run out of memory and wondered why the processed stopped and why I can't connect to my SSH server anymore.<br>Also check your old docker container, they still saved their memory: docker ps -a & docker container prune



Windows (cmd):

- `mkdir` – Create a new directory  
- `del` – Delete a file  
- `rmdir /s /q` – Remove a directory and its contents  
- `notepad` – Open Notepad as a text editor  
- `type` – Display the contents of a file (like `cat`)  
- `dir` – List files in a directory (like `ls`)  
- `cd` – Change directory  
- `echo %cd%` – Print the current working directory (like `pwd`)  
- `copy` – Copy files  
- `move` – Move or rename files  
- `attrib` – Change file attributes (like `chmod`)  
- `findstr` – Search for text within files (like `grep`)  
- `where` – Locate a file (like `which` in Bash)  
- `tar` – Compress and extract archive files (built-in since Windows 10)  
- `compact` – Compress files in NTFS  
- `certutil -decode` / `certutil -encode` – Encode and decode Base64 files  
- `curl` – Transfer data from or to a server  
- `ping` – Test network connectivity  
- `tasklist` – List running processes (like `ps`)  
- `taskkill /F /PID <PID>` – Terminate a process (like `kill`)  
- `netstat -ano` – Display network connections  
- `ipconfig` – Show network interface details (like `ifconfig`)  
- `systeminfo` – Show system information  
- `chkdsk` – Check the file system and disk health  


**Bonus: Starting a process in background**

* **Linux:**
   ```bash
   nohup *your_command(s)_to_run > my_process.log 2>&1 &
   ```
   Explanation:
   - `nohup` the process will not stop after logging out of the console.
   - `>` my_process.log writes output in the log file.
   - `2>&1' leads the error messages also into the file.
   - `&` starts the process in background -> you can continuing tiping/executing something else.
* **Windows (cmd):**<br>
   ```powershell
   start /b your_command(s)_to_run > my_process.log 2>&1
   ```
   Explanation:
   - `start /b` — runs the command without opening a new window (`/b` means *background* in the same console session). The process will keep running even if you close the console if the program itself is detached or independent.
   - `>` redirects standard output (stdout) into `my_process.log`.
   - `2>&1` redirects standard error (stderr) into the same destination as stdout, so both go into the log file.
* **Windows (Powershell):**<br>
   ```powershell
   Start-Process powershell -ArgumentList '-NoProfile -Command "your_command(s)_to_run > my_process.log 2>&1"' -WindowStyle Hidden
   ```
   Explanation:
   - `Start-Process` — launches a completely new process, detached from the current PowerShell session (so it won’t die if you log out or close the window).
   - `powershell -NoProfile -Command` — runs your command inside a clean PowerShell environment.
   - `> my_process.log 2>&1` — redirects both stdout and stderr to the log file.
   - `-WindowStyle Hidden` — keeps it from popping up a new visible console window.

<br><br>

---
### Create SSH Connection

You can also connect via SSH without this step, but the connection via SSH key will be more simple and you don't have to type your password all the time.



#### **Step 1: Check if SSH is Installed**  
Most Linux/macOS systems have SSH installed by default. To check:  
```bash
ssh -V
```
For Windows, use **PowerShell** or install **OpenSSH** if necessary:  
```powershell
Get-WindowsCapability -Online | Where-Object Name -like 'OpenSSH*'
```
If not installed, enable OpenSSH via Windows settings or install **PuTTY**.



#### **Step 2: Get Remote Computer's IP or Hostname**  
Ensure you have the remote system's **IP address** or **domain name**.  
Example: `192.168.1.100` or `myserver.com`



#### **Step 3: Connect via SSH**  
Run:  
```bash
ssh username@remote_host
```
Example:  
```bash
ssh user@192.168.1.100
```
If prompted, type **yes** to accept the host key and enter the **password**.



#### **Step 4: Using an SSH Key (Optional, Recommended)**  
To avoid password prompts, create an SSH key and add it to the remote server.

1. **Generate SSH Key (on your local machine):**  
   ```bash
   ssh-keygen -t rsa -b 4096
   ```
   Press **Enter** to accept the default location.

2. **Copy Public Key to Remote Server:**  
   ```bash
   ssh-copy-id user@remote_host
   ```
   Or manually copy the key:  
   ```bash
   cat ~/.ssh/id_rsa.pub | ssh user@remote_host "mkdir -p ~/.ssh && cat >> ~/.ssh/authorized_keys"
   ```

3. **Test Connection Without Password:**  
   ```bash
   ssh user@remote_host
   ```



#### **Step 5: Additional SSH Options**  

- **Use a specific key:**  
  ```bash
  ssh -i ~/.ssh/my_key user@remote_host
  ```
- **Run a single command remotely:**  
  ```bash
  ssh user@remote_host "ls -l"
  ```
- **Forward a port (useful for remote access):**  
  ```bash
  ssh -L 8080:localhost:80 user@remote_host
  ```


<br><br>


---
### Start SSH Connection 



Ensure you have the remote system's **IP address** or **domain name** (example: `192.168.1.100` or `myserver.com`) and your user-name.



``` terminal
ssh local-admin@10.24.3.16
```

Or

VS Code Remote SSH Extension. Paste: local-admin@10.24.3.16 and type your password.


<br><br>


---
### Data Transfer 



A is a remote connection and B a local connection.

To copy a file from B to A while logged into B:

``` terminal
scp /path/to/file local-admin@10.24.3.16:/path/to/destination
```

To copy a file from B to A while logged into A:

``` terminal
scp local-admin@10.24.3.16:/path/to/file /path/to/destination
```


Examples:

``` terminal
scp /home/tobia/src/tf2/train.py local-admin@10.24.3.16:/home/local-admin/src/tf2
```

``` terminal
scp -r local-admin@10.24.3.16:/home/local-admin/src/tf2/mlruns /home/tobia/from_server/
```

```bash
scp -r local-admin@10.24.3.16:/home/local-admin/src/tf2/benchmark_results /home/tobia/backup/results
```


<br><br>


---
### Wake on Lan 



It is possible to turn on your computer remotly.

1. Get the connection name and device name (example: Wired connection 2, enp11s0)<br>
``` terminal
nmcli connection show
```
2. Get the MAC Adress (example: 30:85:A9:A4:49:7D)<br>
``` terminal
nmcli device show "<CONNECTION NAME>" | grep "GENERAL.HWADDR"
```
3. Check your current setting (use from step 1)
``` terminal
nmcli connection show "<CONNECTION NAME>" | grep 802-3-ethernet.wake-on-lan
```
4. Change to Wake on Lan
``` terminal
nmcli connection modify "<CONNECTION NAME>" 802-3-ethernet.wake-on-lan magic
```

Turning off with:
``` terminal
nmcli connection modify "<CONNECTION NAME>" 802-3-ethernet.wake-on-lan ignore
```


Now we can turn our computer on remotly with (use **ip a**, to get your device name + MAC-Address):

``` terminal
sudo etherwake 30:85:a9:a4:49:7d -i enp11s0
```

``` terminal
sudo etherwake 30:85:a9:a4:49:7d -i eth0
```

Hint: Make sure that your PC are on electricity and have a LAN-cable turned in.



You need to install etherwake on your local machine: sudo apt install etherwake



Shut-Down: 

```bash
sudo shutdown -h now
```





<br><br>

---
### GPU Healthcare

In order to train AI models we most likely need an accelerator (a GPU). And most likely this must be a Nvidia GPU.

For usage we need a Nvidia Driver and a CUDA installation.

<br>

**Linux installation** <br>
```bash
# Installation (Ubuntu/Debian example)
sudo apt update
sudo apt install -y nvidia-driver-535  # replace with latest recommended driver
# Optional: install CUDA toolkit
sudo apt install -y nvidia-cuda-toolkit

# Testing
nvidia-smi
nvcc --version   # if CUDA toolkit installed
```

<br>

**Linux reinstallation** <br>
```bash
# Remove existing drivers & CUDA
sudo apt remove --purge '^nvidia-.*'
sudo apt autoremove
sudo apt clean

# Reinstall latest NVIDIA driver (Ubuntu/Debian example)
sudo apt update
sudo apt install -y nvidia-driver-535  # Replace with latest recommended
# Optional: reinstall CUDA toolkit
sudo apt install -y nvidia-cuda-toolkit

# Reboot to load new driver
sudo reboot

# Testing after reboot
nvidia-smi
nvcc --version   # if CUDA toolkit installed
```

<br>

**Linux Debugging** <br>
```bash
# Check if GPU is detected by PCI bus
lspci | grep -i nvidia

# Check driver module
lsmod | grep nvidia

# Try manually loading the driver
sudo modprobe nvidia

# Check driver logs for errors
dmesg | grep -i nvidia
journalctl -xe | grep -i nvidia
```

<br>

**Windows installation** <br>
```cmd
# Installation
:: 1. Download and install the latest NVIDIA driver from:
::    https://www.nvidia.com/Download/index.aspx
:: 2. (Optional) Download and install the CUDA Toolkit from:
::    https://developer.nvidia.com/cuda-downloads

# Testing
nvidia-smi
nvcc --version   :: only works if CUDA Toolkit installed
```

<br>

**Windows reinstallation**<br>
```cmd
:: 1. Open "Add or Remove Programs" and uninstall:
::    - NVIDIA Graphics Driver
::    - NVIDIA GeForce Experience (optional)
::    - Any existing CUDA Toolkit installation (optional)
::
:: 2. Reboot the PC.
::
:: 3. Download and install the latest NVIDIA driver:
::    https://www.nvidia.com/Download/index.aspx
::
:: 4. (Optional) Download and install the latest CUDA Toolkit:
::    https://developer.nvidia.com/cuda-downloads
::
:: 5. Reboot again to apply changes.

:: Testing
nvidia-smi
nvcc --version
```

<br>

**Windows Debugging**<br>
- Open Device Manager → expand Display adapters → check if NVIDIA GPU is listed without a yellow warning icon.
- If missing or with a warning:
   1. Uninstall the driver (right-click → Uninstall device → Delete the driver software).
   2. Reinstall the latest driver from NVIDIA’s website.
- Check Windows Event Viewer → System logs for NVIDIA-related errors.
- Ensure Windows Update hasn’t replaced your NVIDIA driver with an older one (disable automatic driver updates if needed).


<br>

**Checking GPU**<br>
We can then check our GPU by typing following command and this is a command you will very often type:
```bash
nvidia-smi
```

And:
```bash
nvcc --version
```

For checking with PyTorch and TensorFlow:
```bash
python -c "import torch, tensorflow as tf; print('PyTorch GPU:', torch.cuda.is_available(), torch.cuda.get_device_name(0) if torch.cuda.is_available() else None); print('TensorFlow GPU:', tf.config.list_physical_devices('GPU'))"
```

For checking with only PyTorch:
```bash
python -c "import torch; print(torch.cuda.get_device_name(0) if torch.cuda.is_available() else 'No GPU found')"
```

For checking with only TensorFlow:
```bash
python -c "import tensorflow as tf; print(tf.config.list_physical_devices('GPU'))"
```

**Using Docker with GPU**<br>
Docker provides a reproducible environment for GPU workloads without installing Python packages or CUDA directly. *The Nvidia Driver has to be installed on the host system.*
```bash
# TensorFlow GPU container
docker run --gpus all -it --rm tensorflow/tensorflow:latest-gpu \
    python -c "import tensorflow as tf; print(tf.__version__); print(tf.config.list_physical_devices('GPU'))"

# PyTorch GPU container
docker run --gpus all -it --rm pytorch/pytorch:latest \
    python -c "import torch; print(torch.cuda.is_available(), torch.cuda.get_device_name(0) if torch.cuda.is_available() else None)"
```
- `--gpus` all ensures the container can access all GPUs.
- `--rm` removes the container after exit.
- `-it` gives an interactive terminal.
This is useful for testing if your GPU is working correctly in a reproducible environment, even if your host system doesn’t have Python, CUDA, or libraries installed.

<br>

Additional commands to check CUDA on docker (from a real example):

> Here you might want remove `--runtime=nvidia`, this is a specific runtime from the nvidia-toolkit which helps in this case to find the hardware and run the code. Also change the mountings `-v` as wished and change the docker image name `cosmos-predict2-local` to yours.

```
# Get Device Number
nvidia-smi -L

# Find the right (previously installed) image
docker image ls

# Check versions (multiline commands)
echo -e "\n> CUDA TEST start <\n\n---------\nHOST GPU Versions\n---------\n" && \
echo "NVIDIA Driver Version: $(nvidia-smi --query-gpu=driver_version --format=csv,noheader)" && echo "CUDA Version: $(nvcc --version | grep release | awk '{print $6}' | sed 's/,//')" && \
echo -e "\n---------\nDOCKER GPU Versions\n---------\n" && \
docker run --gpus all --runtime=nvidia --rm \
--shm-size=8g \
-v ~/src/cosmos-predict2:/workspace \
-v ~/src/cosmos-predict2/datasets:/workspace/datasets \
-v ~/src/cosmos-predict2/checkpoints:/workspace/checkpoints \
cosmos-predict2-local \
/bin/bash -c 'echo "CUDA Version:" $(nvcc --version | grep release | awk "{print \$6}" | sed "s/,//")' && \
echo -e "\n> CUDA TEST end <\n" 

# Start docker
docker run --gpus all --runtime=nvidia -it --rm \
--shm-size=8g \
-v ~/src/cosmos-predict2:/workspace \
-v ~/src/cosmos-predict2/datasets:/workspace/datasets \
-v ~/src/cosmos-predict2/checkpoints:/workspace/checkpoints \
cosmos-predict2-local

# Tests
nvidia-smi
python3 -c "import torch; print(torch.cuda.device_count()); print(torch.cuda.get_device_name(0))"

exit
```

Or similiar but a bit simpler:

```bash
docker run --gpus '"device=0"' --runtime=nvidia -it --rm \
  -v ~/src/cosmos-predict2:/workspace \
  -v ~/src/cosmos-predict2/datasets:/workspace/datasets \
  -v /ssd0/tippolit/checkpoints/checkpoints:/workspace/checkpoints \
  cosmos-predict2-local

then type:
env | grep CUDA
ls /dev/nvidia*
nvidia-smi
python -i
import torch; print(torch.cuda.is_available())
print(torch.cuda.get_device_name(0))
exit()
exit
```


You might want to check out [PyTorch CUDA Version Support Check](#pytorch-cuda-version-support-check).

<br>

**Other related commands**<br>

- GPU status live refresh every 1 second:
   ```bash
   nvidia-smi -l 1
   ```
- Get all GPU Devices:
  ```bash
  nvidia-smi -L
  ```
- Custom GPU info in CSV format:
   ```bash
   nvidia-smi --query-gpu=name,driver_version,memory.total,memory.used,memory.free --format=csv
   ```
- Process monitoring (shows which PIDs are using the GPU):
   ```bash
   nvidia-smi pmon -c 1
   ```
- Show GPU topology & NVLink connections:
   ```bash
   nvidia-smi topo --matrix
   ```
- Power usage & temp stats:
   ```bash
   nvidia-smi --query-gpu=power.draw,temperature.gpu --format=csv
   ```
- Reset a GPU (dangerous if in use):
   ```bash
   nvidia-smi --gpu-reset -i <id>
   ```
- Disable auto boost clocks (for stable benchmarking):
   ```bash
   nvidia-smi --auto-boost-default=0
   ```
- Show full options list:
   ```bash
   nvidia-smi --help
   ```

- Show detailed GPU hardware info (Linux)
   ```bash
   lspci | grep -i nvidia
   ```

<br><br>

---
### PyTorch CUDA Version Support Check


Add a `pytorch_cuda_check.Dockerfile`:
```bash
# ==================================
#  Minimal PyTorch + CUDA Test Image
# ==================================

# xX CHANGE ME Xx
# --- Choose your CUDA version here ---
# FROM nvidia/cuda:12.1.1-devel-ubuntu22.04
# FROM nvidia/cuda:12.0.1-devel-ubuntu20.04
# FROM nvidia/cuda:12.0.1-devel-ubuntu22.04
FROM nvidia/cuda:11.8.0-devel-ubuntu22.04
# FROM nvcr.io/nvidia/pytorch:23.09-py3
# FROM nvcr.io/nvidia/pytorch:25.04-py3
# --------------------------------------

# Prevent interactive prompts
ENV DEBIAN_FRONTEND=noninteractive

# Install Python + pip
RUN apt-get update && apt-get install -y --no-install-recommends \
    python3 python3-pip \
    && rm -rf /var/lib/apt/lists/*

# For Debugging -> show CUDA directories
RUN ls -l /usr/local/cuda*

# xX CHANGE ME Xx
# Install matching PyTorch version (adjust when CUDA base image changes)
# CUDA 12.1 → cu121
# CUDA 12.0 → cu120
# CUDA 11.8 → cu118 
RUN pip install torch==2.3.1+cu118 torchvision==0.18.1+cu118 torchaudio==2.3.1 \
    --index-url https://download.pytorch.org/whl/cu118

# You might want also change the torch version itself, checkout: 
#            - https://pytorch.org/get-started/locally/
#            - https://pytorch.org/get-started/previous-versions/




# ---------------------------
# Creating a test python file
# ---------------------------
RUN mkdir -p /workspace && \
    echo 'import torch\n' \
         'print("PyTorch Version:", torch.__version__)\n' \
         'print("CUDA verfügbar?:", torch.cuda.is_available())\n' \
         'if torch.cuda.is_available():\n' \
         '    print("CUDA Geräteanzahl:", torch.cuda.device_count())\n' \
         '    print("CUDA Gerät:", torch.cuda.get_device_name(0))\n' \
         'else:\n' \
         '    print("[ERROR] Keine CUDA-Unterstützung erkannt!")\n' \
         > /workspace/test_cuda.py

WORKDIR /workspace

CMD ["python3", "test_cuda.py"]
```

Build an Image:
```bash
docker build --no-cache -t cuda-check -f pytorch_cuda_check.Dockerfile .
```

Run as Container:
```bash
docker run --rm cuda-check

# or

docker run --rm --gpus all cuda-check

# or

docker run --runtime nvidia --rm --gpus all cuda-check
# docker run --runtime=nvidia --rm --gpus all cuda-check
```


<br><br>

---
### PyTorch

In order to use the GPU we set up in the previous step, we need to install PyTorch with GPU support.<br>
Both libraries can be installed via Conda, pip (vanilla), or run inside a Docker container.<br>
Conda is good for quick and reproducable python environments - you can change your python version very easily. Docker is even more reproducable but a bit more complicated (make/pull image + run via container).

> For [Anaconda installation see this](https://github.com/xXAI-botXx/Project-Helper#anaconda) + for [Docker installation see this](https://github.com/xXAI-botXx/Docker).

**Installation**
- Via pip
   ```bash
   pip install torch torchvision --index-url https://download.pytorch.org/whl/cu126
   ```
- Via Conda (in anaconda bash) -> [also see this](https://github.com/xXAI-botXx/Project-Helper#anaconda)
   ```bash
   # Replace "cu126" with the CUDA version installed on your system (check with nvcc --version)
   conda create -n torch python=3.12 -y
   conda activate torch
   pip install torch torchvision --index-url https://download.pytorch.org/whl/cu126
   ```
- Via Docker
   ```bash
   # Latest PyTorch image with CUDA support
   docker run --gpus all -it --rm pytorch/pytorch:latest
   ```

**Check**
```bash
python -c "import torch; print(torch.__version__); print(torch.cuda.is_available(), torch.cuda.get_device_name(0) if torch.cuda.is_available() else None)"
```

```bash
docker run --gpus all --rm pytorch/pytorch:latest \
    python -c "import torch; print(torch.__version__); print(torch.cuda.is_available(), torch.cuda.get_device_name(0) if torch.cuda.is_available() else None)"
```

**Cleanup**
```bash
pip uninstall torch torchvision -y
```

**Run with Docker**
```bash
# Start TF container in background
docker run --gpus all -d --name torch_container pytorch/pytorch:latest tail -f /dev/null

# Enter container
docker exec -it torch_container bash

# Stop container
docker stop torch_container
docker rm torch_container
```


<br><br>

---
### Tensorflow

In order to use the GPU we set up in the previous step, we need to install PyTorch with GPU support.<br>
Both libraries can be installed via Conda, pip (vanilla), or run inside a Docker container.<br>
Conda is good for quick and reproducable python enviroments - you can change your python version very easily. Docker is even more reproducable but a bit more complicated (make/pull image + run via container).

> For [Anaconda installation see this](https://github.com/xXAI-botXx/Project-Helper#anaconda) + for [Docker installation see this](https://github.com/xXAI-botXx/Docker).


**Installation**
- Via pip
   ```bash
   # Will install GPU-enabled TF if requirements are met
   pip install tensorflow==2.15.*
   ```
- Via Conda (in anaconda bash) -> [also see this](https://github.com/xXAI-botXx/Project-Helper#anaconda)
   ```bash
   conda create -n tf python=3.10 -y
   conda activate tf
   pip install tensorflow==2.15.*  # GPU-enabled by default if NVIDIA driver + CUDA/cuDNN present
   ```
- Via Docker
   ```bash
   # Latest TensorFlow image with GPU support
   docker run --gpus all -it --rm tensorflow/tensorflow:latest-gpu
   ```

**Check**
```bash
python -c "import tensorflow as tf; print(tf.__version__); print(tf.config.list_physical_devices('GPU'))"
```

```bash
docker run --gpus all --rm tensorflow/tensorflow:latest-gpu \
    python -c "import tensorflow as tf; print(tf.__version__); print(tf.config.list_physical_devices('GPU'))"
```

**Cleanup**
```bash
pip uninstall tensorflow tensorflow-gpu -y
```

**Run with Docker**
```bash
# Start TF container in background
docker run --gpus all -d --name tf_container tensorflow/tensorflow:latest-gpu tail -f /dev/null

# Enter container
docker exec -it tf_container bash

# Stop container
docker stop tf_container
docker rm tf_container
```


<br><br>

---

### Run Docker in Background

Sometimes you want to **run Docker containers in the background**, e.g., for training or other long-running jobs. Use the `-d` parameter (detached mode).

**Example: PyTorch GPU container in the background**

```bash
docker run --gpus all -d --name my_pytorch_container pytorch/pytorch:latest tail -f /dev/null
```

* `-d` → starts the container in detached mode
* `--name my_pytorch_container` → gives the container a friendly name
* `tail -f /dev/null` → keeps the container alive without exiting immediately

**Example: TensorFlow GPU container in the background**

```bash
docker run --gpus all -d --name my_tensorflow_container tensorflow/tensorflow:latest-gpu tail -f /dev/null
```


**Access the running container**

```bash
docker exec -it my_pytorch_container bash
```

or

```bash
docker exec -it my_tensorflow_container bash
```

This allows you to work interactively, run scripts, or test the frameworks.


**Stop / remove the container**

```bash
docker stop my_pytorch_container
docker rm my_pytorch_container
```

The same applies for TensorFlow containers.



<br><br>

---
### Run Python-Script in Background



1. ``````bash
   cd ./src/instance-segmentation
   ``````

2. ```bash
   chmod +x temp_train.py
   ```

3. ``` bash
   conda activate yolact
   ```

4. ``` bash
    # Linux
    nohup python temp_train.py > output.log 2>&1 &

    # Windows
    start /B python temp_train.py > output.log 2>&1
   ```


<br><br>


---
### Run ipynb in Background 



- start vs code remote session (with remote ssh extension)
- go to [remote-server](#start-ssh-connection) with a terminal
- ``` terminal
  conda activate yolact
  ```
- ``` terminal
  conda install jupyter
  ```
- Navigate to your workdirectory
- start a jupyter-server with: <br>
``` terminal
nohup jupyter notebook > jupyter_output.log 2>&1 &
```
- connect VS Code to the jupyter server:
	- select another kernel
	
	- select existing jupyter server
	
	- copy link from starting server to the prompt
	
	  - ```bash
	    cat jupyter_output.log
	    ```

Now you can run your code and disconnect and connect back and the code should still work.
Because the jupyter-server is not started from VS Code, so it still runs and the VS-Code detect it and connect to it again.



<br><br>

---
### Check if Script is Running



There are different ways to ensure if your script is running or not. If you used a VS-Code Remote connect with IPYNB in Background, you can also connect to it and look if it is still running or not.
- ``` terminal
  top
  ```
    This is the best method to check if your code is still running. if your code is running, it should appear often at the head of the table and you should see since when your python task is running. So look for a python task.
- ``` terminal
  ls -lt
  ```
    With this command you can check when your files are updated the last time. Look at most at the models folder and your log-files. This method can be helpful but is not precisious at all.
- ``` terminal
  nvidia-smi
  ```
    Shows which processes are on the GPU, but often a process takes memory even if the process is not running anymore. So this method is most likely not helpful.
- ``` terminal
  ps
  ```
    This shows all current processes but somehow it doesn't show a python process which runs for long time. So this method is also not so helpful.
- ``` terminal
  ps ax | grep train.py
  ```
    This is probably a bit more helpful.
- ``` terminal
	pgrep -f train.py
  ```
	-> ps -p 3567 (use a founded ID)
	
	

Sometimes it could be helpful to reboot or kill a process.



<br><br>


---
### Start Tensorboard



(auf [remote-server](#start-ssh-connection))
1. ``` terminal
   conda activate yolact
   ```
2. ``` terminal
   tensorboard --logdir=~/src/instance-segmentation/runs --host=0.0.0.0
   ```
    Change the logfile-dirname as you need.
3. Now open the browser and type:<br>
http://10.24.3.16:6006



<br><br>

---

### Start MLFlow UI



(auf [remote-server](#start-ssh-connection))
1. ``` terminal
   conda activate yolact
   ```
2. ``` terminal
   mlflow ui --host=0.0.0.0 --backend-store-uri file:///home/local-admin/src/instance-segmentation/mlruns
   ```
3. Now open the browser and type:<br>
http://10.24.3.16:5000



Hint: You can search for your mlflow directory with following command (navigate to your home folder before):

```bash
find . -type d -name mlruns 2>/dev/null
```




<br><br>


---
### GUI Sharing 



VNC:

1. On Server (there you will get asked for a password):

   ``````bash
   sudo apt-get install x11vnc
   x11vnc -storepasswd
   x11vnc -usepw -display :0
   ``````

2. On Client:

   - Install remmina software over sudo apt install remmina or over the App Center
   - Open the remmina software
   - Choose VNC
   - Type the IP of your PC: 10.24.3.16
   - Type the password and there you go!

   



THE FOLLOWING ARE ONLY ATTEMPTS AND DID NOT WORKED FOR ME!

BUT MAYBE YOU CAN FIND SOMETHING HELPFUL THERE.

GOOD LUCK.



Other stuff:

``````bash
#You can also start a desktop-session:

gnome-session

# list monitors
xrandr --listmonitors
echo $DISPLAY

# set DISPLAY var
export DISPLAY=:0

# test with:
xprop
``````





Teamviewer:

1. Create virtuel display on server

   `````bash
   sudo apt-get update
   sudo apt-get install x11-xserver-utils
   
   gtf 1920 1080 60
   
   xrandr --newmode "1920x1080_60.00" 172.80 1920 2048 2248 2560 1080 1083 1088 1120 -hsync +vsync
   xrandr --addmode DP-1 "1920x1080_60.00"
   xrandr --output DP-1 --mode 1920x1080_60.00
   
   # helpful: xrandr --verbose
   `````

   

2. Install Teamviewer on the server

   ``````bash
   cd ~/Downloads
   wget https://download.teamviewer.com/download/linux/teamviewer_amd64.deb
   sudo dpkg -i teamviewer_amd64.deb
   sudo apt-get install -f
   teamviewer
   ``````

   



RDP:

1. Install Remote Desktop Protocol on Server

   ``````bash
   sudo apt update
   sudo apt-get install twm
   sudo apt-get install xterm
   sudo apt-get install xorg xinit
   sudo apt-get install ubuntu-gnome-desktop gnome-core gnome-session
   sudo apt install xfce4 xfce4-goodies
   sudo apt-get install xserver-xorg-video-all xserver-xorg-core
   sudo apt-get install libdrm-dev
   sudo apt install nvidia-driver-560
   sudo apt install xrdp
   
   sudo systemctl enable xrdp
   sudo systemctl start xrdp
   
   # listen to logs
   sudo journalctl -u xrdp
   
   # check port
   sudo netstat -tulnp | grep xrdp
   
   # open port in firewall
   sudo ufw allow 3389/tcp
   
   # check status
   sudo systemctl status xrdp
   
   # restart
   sudo systemctl restart xrdp
   sudo systemctl restart xrdp-sesman

2. User Install:

   `````` bash
   sudo apt install rdesktop
   rdesktop 10.24.3.16 # <REMOTE_IP_ADDRESS> 
   # use the username and password from the remote server
   ``````

   



---







