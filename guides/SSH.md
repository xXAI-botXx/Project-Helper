# SSH

[<img align="right" width=150px src='../res/rackete_2.png'></img>](../README.md)

This file tries to help out for remote control a compute device (server).



Table of Contents:

- [Helpful Bash-Commands](#helpful-bash-commands)
- [Create SSH Connection](#create-ssh-connection)
- [Start SSH Connection](#start-ssh-connection)
- [Data Transfer](#data-transfer)
- [Wake on Lan](#wake-on-lan)
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





---
### Start SSH Connection 



Ensure you have the remote system's **IP address** or **domain name** (example: `192.168.1.100` or `myserver.com`) and your user-name.



``` terminal
ssh local-admin@10.24.3.16
```

Or

VS Code Remote SSH Extension. Paste: local-admin@10.24.3.16 and type your password.





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
    nohup python temp_train.py > output.log 2>&1 &
   ```





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







