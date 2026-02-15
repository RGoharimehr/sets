# PyCharm Setup Guide with Anaconda

This guide will help you set up and run the SETS project in PyCharm using Anaconda.

## Prerequisites

- [Anaconda](https://www.anaconda.com/products/distribution) or [Miniconda](https://docs.conda.io/en/latest/miniconda.html) installed
- [PyCharm](https://www.jetbrains.com/pycharm/) (Community or Professional edition)
- Linux system (Ubuntu recommended) or WSL on Windows

## Step 1: Install System Dependencies

Before setting up the Python environment, install the required system libraries:

```bash
sudo apt install build-essential
sudo apt install libeigen3-dev
sudo apt install libyaml-dev
```

## Step 2: Create Conda Environment

Open a terminal and navigate to the project directory:

```bash
cd /path/to/sets
conda env create --file environment.yml
```

This will create a conda environment named `sets` with Python 3.7 and all required dependencies.

Activate the environment:

```bash
conda activate sets
```

## Step 3: Add Source Directory to Python Path

Add the `src` directory to your conda environment's path:

```bash
conda develop src/
```

Alternatively, you can set the `PYTHONPATH` environment variable in PyCharm (see Step 5).

## Step 4: Build C++ Extensions

The project includes C++ code with Python bindings that must be compiled:

```bash
cd src/
mkdir -p build
cd build
cmake -DPYTHON_EXECUTABLE=$(which python) -DCMAKE_BUILD_TYPE=Release ..
make
```

This creates the Python bindings in `src/build/bindings.so`.

## Step 5: Configure PyCharm

### A. Open Project in PyCharm

1. Launch PyCharm
2. Click **File → Open**
3. Navigate to and select the `sets` directory
4. Click **OK**

### B. Configure Python Interpreter

1. Go to **File → Settings** (or **PyCharm → Preferences** on macOS)
2. Navigate to **Project: sets → Python Interpreter**
3. Click the gear icon ⚙️ → **Add...**
4. Select **Conda Environment** → **Existing environment**
5. Browse to your conda environment's Python executable:
   - Typically located at: `~/anaconda3/envs/sets/bin/python`
   - Or find it by running `conda activate sets && which python` in terminal
6. Click **OK**

### C. Configure Project Structure

1. Go to **File → Settings → Project: sets → Project Structure**
2. Right-click on the `src` folder and mark it as **Sources Root** (this makes imports work correctly)
3. Right-click on the `scripts` folder and mark it as **Sources Root**
4. Click **OK**

### D. Set Environment Variables (Optional)

If you didn't use `conda develop`, you need to add the `src` directory to `PYTHONPATH`:

1. Go to **Run → Edit Configurations...**
2. Click **Edit configuration templates...** → **Python**
3. Add environment variable:
   - Name: `PYTHONPATH`
   - Value: `$PROJECT_DIR$/src`
4. Click **OK**

## Step 6: Running Scripts in PyCharm

### Option A: Run from Terminal (Recommended for First Time)

1. Open the PyCharm terminal (bottom panel)
2. The conda environment should activate automatically
3. Run scripts:

```bash
cd scripts/
python value_convergence.py
```

or

```bash
cd scripts/
python policy_convergence.py
```

### Option B: Use Pre-configured Run Configurations

This project includes pre-configured run configurations for the main scripts. After opening the project in PyCharm:

1. Look for the run configuration dropdown in the top toolbar (near the green play button ▶️)
2. You should see configurations for:
   - **Value Convergence** - Runs `value_convergence.py`
   - **Policy Convergence** - Runs `policy_convergence.py`
   - **Rollout** - Runs `rollout.py`
3. Select a configuration and click the green play button ▶️ to run

These configurations are stored in the `.run/` directory and are automatically recognized by PyCharm.

### Option C: Create Custom Run Configurations

If you need to create additional run configurations:

1. Right-click on a script file (e.g., `scripts/value_convergence.py`)
2. Select **Run 'value_convergence'**
3. Or click **Run → Edit Configurations...**
4. Click **+** → **Python**
5. Configure:
   - **Name**: Value Convergence
   - **Script path**: Browse to `scripts/value_convergence.py`
   - **Working directory**: `$PROJECT_DIR$/scripts`
   - **Python interpreter**: Select the `sets` conda environment
6. Click **OK**

Now you can run the script by clicking the green play button ▶️ in PyCharm.

## Troubleshooting

### Import Errors

If you get import errors like `ModuleNotFoundError: No module named 'build.bindings'`:

1. Make sure you've built the C++ extensions (Step 4)
2. Verify `src` is marked as Sources Root in PyCharm
3. Check that `PYTHONPATH` includes the `src` directory
4. Try running from the `scripts` directory

### Build Errors

If CMake or make fails:

1. Ensure all system dependencies are installed (Step 1)
2. Make sure the conda environment is activated
3. Check that cmake finds the correct Python: `cmake -DPYTHON_EXECUTABLE=$(which python) ..`

### PyCharm Can't Find Conda Environment

If PyCharm doesn't detect your conda installation:

1. Find conda's path: `which conda`
2. In PyCharm settings, manually specify the path to the Python interpreter in your environment

### Module 'bindings' Not Found

This means the C++ extensions haven't been built. Go back to Step 4 and compile them.

## Additional Tips

### Running with Different Configurations

You can modify the parameters in the `configs/` directory to change the behavior of the scripts.

### Debugging

PyCharm's debugger works great with this project:

1. Set breakpoints by clicking in the left margin of the code editor
2. Right-click your script and select **Debug 'script_name'**
3. Use the debugger panel to step through code, inspect variables, etc.

### Performance

For better performance when running computationally intensive simulations:

- Build in Release mode (already done in Step 4)
- Use multiple CPU cores (the scripts already use multiprocessing)
- Consider running on a machine with more CPU cores

## Verifying Your Setup

To verify everything is set up correctly, try running:

```bash
cd scripts/
python -c "from build.bindings import get_mdp; print('Success! Bindings imported correctly.')"
```

If this prints "Success!", you're ready to run the full scripts.

## Next Steps

Once set up, you can run the main scripts that reproduce the results from the paper:

- `value_convergence.py` - Generates Figure 5c from the paper
- `policy_convergence.py` - Generates Figure 5d from the paper

Results will be saved in the `plots/` directory, and data in the `data/` directory.

## Need Help?

If you encounter issues not covered here, please check:

1. The main [README.md](README.md) for general project information
2. The GitHub repository's Issues section
3. Make sure your Anaconda and PyCharm versions are up to date
