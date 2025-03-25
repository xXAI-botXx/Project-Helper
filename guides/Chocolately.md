# Chocolately

[<img align="right" width=150px src='../res/rackete_2.png'></img>](../README.md)


Chocolately is a package manager for windows, similiar to apt in linux.



Table of Contents:
- [Installation](#installation)
- [Search for Software](#search-for-software)
- [Install Software with Chocolately](#install-software-with-chocolately)
- [Upgrade Software with Chocolately](#upgrade-software-with-chocolately)
- [Uninstall Software with Chocolately](#install-software-with-chocolately)
- [Choco GUI](#choco-gui)



> click the **rocket** for going **back**






---
### Installation

1. Open the PowerShell with Admin rights (Win+x -> "Terminal (Admin)")
2. Type followinf to enable scripts:
    ```powershell
    Set-ExecutionPolicy Bypass -Scope Process -Force
    ```
3. Install Chocolately:
    ```powershell
    iwr https://community.chocolatey.org/install.ps1 -UseBasicParsing | iex
    ```
4. Verify Installation -> Close and open your powershell and then run:
    ```powershell
    choco -v
    ```






---
### Search for Software

```powershell
choco search <software-name>
```

Example:
```powershell
choco search python
```






---
### Install Software with Chocolately

```powershell
choco install <package-name> -y
```

Example:
```powershell
choco install git -y
```

```powershell
choco install vscode cmake git python nodejs -y
```





---
### Upgrade Software with Chocolately

```powershell
choco upgrade <package-name> -y
```

Example:
```powershell
choco upgrade git -y
```

And for upgrading all packages:
```powershell
choco upgrade all -y
```





---
### Uninstall Software with Chocolately

```powershell
choco uninstall <package-name> -y
```

Example:
```powershell
choco uninstall git -y
```






---
### Choco GUI

Chocolately have alternativly also a GUI. You can install it with:

```powershell
choco install chocolateygui -y
```




