# Test project to demonstrate how the `MANIFEST.in` affects wheels

This project is a simple demo project to show, that the `MANIFEST.in` file
from the `setuptools` package does affect the output of the wheels created.

According to the documentation of `setuptools`, the `MANIFEST.in` should only
affect the source distribution:
> `MANIFEST.in`
>
> A `MANIFEST.in` is needed when you need to package additional files that are
> not automatically included in a source distribution. For details on writing 
> a `MANIFEST.in` file, including a list of what’s included by default, see 
> “Including files in source distributions with `MANIFEST.in`”.
>
> However, you may not have to use a `MANIFEST.in`. For an example, the PyPA
> sample project has removed its manifest file, since all the necessary files
> have been included by setuptools 43.0.0 and newer.
>
> **Note `MANIFEST.in` does not affect binary distributions such as wheels.**

https://packaging.python.org/en/latest/guides/distributing-packages-using-setuptools/#manifest-in

But this project shows, that the contents of the `MANIFEST.in` file do affect
which files will be included in the wheel.


## How to reproduce?

1. Create a virtual environment using a Python 3 interpreter. This was tested
   using Python 3.10 and 3.11 interpreter on a Ubuntu Linux 22.10:
   ```bash
   python -m venv venv
   ```

2. Install the requirements:
   ```bash
   ./venv/bin/pip install -r requirements.txt
   ```

3. Build a source distribution and a wheel:
   ```bash
   ./venv/bin/pyproject-build .
   ```

Now a `dist` folder has been created which contains a `.tar.gz` source
distribution and a `.whl` wheel. If you open the wheel, you can see, that
there exists a `fuu/test` file, which is not a python source file and is
not included in the package data in the `setup.py`:
```python
from setuptools import setup, find_packages

setup(
    name = "fuu",
    include_package_data=True,
    packages=find_packages("."),
)
```

But it is included in the `MANIFEST.in` file:
```
graft fuu
```

To verify, that it really is the `MANIFEST.in` file including this file,
just delete it and rebuild the wheel:
```bash
rm MANIFEST.in && ./venv/bin/pyproject-build .
```

Now you can see, that the `fuu/test` file is not included in the wheel.
