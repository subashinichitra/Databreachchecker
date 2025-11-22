
---

# DATABREACH CHECKER

Databreach Checker is a simple Python tool that helps you check if an **email** or **password** has appeared in known data breaches.
It’s mainly for personal security checks and learning, not for attacking or abusing other people’s data.

---

## FEATURES

* Check if an **email address** has been leaked in a known data breach
* Check if a **password** appears in leaked password lists
* Support for **single** and **mass** (file-based) password checks
* Optional **proxy** support for network routing
* Simple **CLI interface** (run from terminal)
* Lightweight and easy to run locally

---

## PREREQUISITES

* Python **3.11** or later
* `pip` installed
* Git (if you clone using git)

Optional:

* A text editor or IDE (VS Code, PyCharm, etc.)

---

## INSTALLATION

1. **Clone the repository**

   ```bash
   git clone https://github.com/subashinichitra/Databreachchecker.git
   cd Databreachchecker
   ```

2. **Create and activate virtual environment**

   ```bash
   python -m venv venv
   # Windows
   venv\Scripts\activate
   # Linux / macOS
   source venv/bin/activate
   ```

3. **Install dependencies**

   ```bash
   pip install -r requirements.txt
   ```

That’s it. After this, you can run the tool.

---

## DEVELOPMENT

Basic workflow if you want to modify or extend the project:

* Edit Python files inside the main package (for example, `bchecker/`).

* Run the tool after each change to test behaviour:

  ```bash
  python -m bchecker
  ```

* Keep JSON files like `mailleaks.json` and `passleaks.json` as data sources or caches; avoid editing them manually unless you know what you’re doing.

* Use a separate branch if you are experimenting with new features.

---

## BUILDING

This project is a **Python CLI tool**, so there is no heavy “build” step.

Possible “build-style” actions:

* Create a **standalone executable** (optional) using tools like `pyinstaller` if you want to share it with non-Python users.
* Package it as a Python package (`setup.py` / `pyproject.toml`) if you plan to publish it.

For normal use, you just install dependencies and run it with Python.

---

## PROJECT STRUCTURE

Example structure (may vary slightly):

```text
Databreachchecker/
├─ bchecker/              # Main Python package (core logic & CLI)
│  ├─ __init__.py
│  ├─ main.py             # Entry point for python -m bchecker
│  ├─ cli.py              # Argument parsing and commands (if present)
│  └─ utils.py            # Helper functions (if present)
├─ mailleaks.json         # Email breach data / cache
├─ passleaks.json         # Password breach data / cache
├─ savedone.json          # Internal state / cache
├─ masspass.txt           # Example file for batch password checks
├─ requirements.txt       # Dependencies
└─ README.md              # Project documentation
```

Adjust the filenames if your repo uses slightly different names.

---

## USAGE

### 1. Interactive mode

Run the tool and follow the prompts:

```bash
python -m bchecker
```

You’ll be asked what you want to check (email or password), and then to enter the value.

---

### 2. Check a single email

```bash
python -m bchecker -m 1 -e "user@example.com"
```

### 3. Check a single password

```bash
python -m bchecker -m 2 -t 1 -p "your_password_here"
```

### 4. Check multiple passwords from a file

Create a text file (for example, `masspass.txt`) with **one password per line**, then:

```bash
python -m bchecker -m 2 -t 2 -f masspass.txt
```

If supported in your version, you can also use `-pu` to pass a proxy URL.

---

## API ENDPOINTS

This project is a **local CLI tool**, not a web service, so it does **not expose HTTP API endpoints** out of the box.

If you later wrap it in a web API, you might have endpoints like:

* `POST /check/email` → check if an email is breached
* `POST /check/password` → check if a password is breached

For now, all interaction happens through the **command line**, not HTTP.

---

## PERFORMANCE OPTIMIZATION

* Use **mass check** mode for large lists of passwords instead of checking one by one manually.
* Avoid unnecessarily large input files if you only need to test a few values.
* Keep breach data files (`mailleaks.json`, `passleaks.json`) on a **fast local drive**.
* If you add network-based lookups in the future, use:

  * Timeouts on HTTP requests
  * Caching of repeated results
  * Batched queries where possible

---

## TROUBLESHOOTING

**1. `ModuleNotFoundError` or import errors**

* Make sure you are **inside the project folder** and the virtual environment is activated:

  ```bash
  cd Databreachchecker
  venv\Scripts\activate    # or source venv/bin/activate
  ```

---

**2. `python -m bchecker` doesn’t work**

* Check that `bchecker` is a **Python package** (it should have an `__init__.py`).
* Make sure you run the command from the folder that contains the `bchecker` directory.

---

**3. No output or tool exits immediately**

* Run with the CLI options instead of pure interactive mode, for example:

  ```bash
  python -m bchecker -m 1 -e "test@example.com"
  ```

---

**4. Strange results or empty breach data**

* Confirm that the JSON data files (`mailleaks.json`, `passleaks.json`) are present and not corrupted.
* If you changed them manually, restore them from a fresh clone of the repository.

---

**5. Proxy issues**

* Check your proxy URL format:

  * `http://user:pass@host:port`
  * `socks5://user:pass@host:port`
* If checks fail only with proxy enabled, try running without `-pu` to see if the problem is the proxy itself.

---


