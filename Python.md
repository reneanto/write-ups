
## Python

### Venv

* To create a virtual environment via bash we can do
  
```bash
python3 -m venv .
```

* To activate the virtual env we just created

```bash
source ./bin/activate
```

* To install packages which throw set up errors we can do

```bash
pip install --no-build-isolation <package>
```