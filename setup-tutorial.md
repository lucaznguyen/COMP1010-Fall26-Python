# Python and PyCharm Setup

Use Python 3 with PyCharm to complete the lab exercises. PyCharm's core Python development features are free; its optional Pro trial is not required for this lab.

## 1. Install Python 3

1. Download Python from the [official Python downloads page](https://www.python.org/downloads/).
2. Choose the installer for your operating system and complete the installation.
3. On Windows, enable **Add Python to PATH** in the installer if that option is shown.
4. Open a terminal or command prompt and check the installation:

   ```text
   python --version
   ```

   On some macOS or Linux systems, use `python3 --version` instead. Confirm that the command reports Python 3.

## 2. Install PyCharm

1. Download PyCharm from the [official JetBrains download page](https://www.jetbrains.com/pycharm/download/).
2. Install the version for your operating system and launch PyCharm.
3. The unified PyCharm app includes the core features needed to write and run Python programs for free.

## 3. Create and run a Python project

1. From the welcome screen, select **New Project**. If a project is already open, select **File > New Project**.
2. Choose **Pure Python**, set a project location, and keep the default project virtual environment unless your instructor gives different instructions.
3. If PyCharm asks for a Python interpreter, select the Python 3 installation from Step 1.
4. Create a Python file named `main.py` and enter:

   ```python
   print("Hello World!")
   ```

5. Run the file. The Run window should display `Hello World!`.

For screenshots and current interface details, see JetBrains' [quick start guide](https://www.jetbrains.com/help/pycharm/quick-start-guide.html).
