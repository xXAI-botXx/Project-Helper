<h1 align="center"> Project-Helper</h1>

by Tobia Ippolito
<br>
<br>

<p align="center">🟡 Welcome here, i'm there to help you out! 🟡</p>
<br><br>

<p align="center">In this repository you found helpful guides to specific topics of data science projects. Moreover I plan some projects as Issues.</p>

<br><br>

<div align="center">>>> Notice that this repository grow over the time <<<</div>



---

<h2 align="center">Navigation</h2>

<div align=center>

### [Git Guide](guides/Git_Helper.md)
### [Sphinx Guide](guides/Sphinx_Helper.md)
### [Ipynb to Markdown](README.md#Ipynb-to-Markdown)

</div>

---
  
  <a name="Ipynb-to_Markdown"></a>
### Ipynb to Markdown  
  
1. Installing the tools
  ```
pip install jupyter
pip install nbconvert
  ```
  
2. Start the cmd and go to the location of the notebook and type follow:
  ```
jupyter nbconvert --ClearMetadataPreprocessor.enabled=True --ClearOutput.enabled=True --to markdown README.ipynb
  ```
  
---
  
