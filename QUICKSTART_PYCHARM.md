# Quick Start: Running SETS in PyCharm with Anaconda

**Short Answer: YES, you can run this code on Anaconda with PyCharm!**

This guide provides a quick overview. For detailed instructions, see [PYCHARM_SETUP.md](PYCHARM_SETUP.md).

## Quick Setup (5 minutes)

### 1. Install System Dependencies
```bash
sudo apt install build-essential libeigen3-dev libyaml-dev
```

### 2. Create Anaconda Environment
```bash
conda env create --file environment.yml
conda activate sets
```

### 3. Build C++ Extensions
```bash
cd src/
mkdir -p build
cd build
cmake -DPYTHON_EXECUTABLE=$(which python) -DCMAKE_BUILD_TYPE=Release ..
make
cd ../..
```

### 4. Open in PyCharm
1. Open PyCharm
2. **File → Open** → Select the `sets` directory
3. **File → Settings → Project: sets → Python Interpreter**
4. Click ⚙️ → **Add** → **Conda Environment** → **Existing environment**
5. Browse to `~/anaconda3/envs/sets/bin/python`
6. Click **OK**

### 5. Configure Project Structure
1. **File → Settings → Project: sets → Project Structure**
2. Right-click `src` folder → **Mark as Sources Root**
3. Click **OK**

### 6. Run Scripts
Use the pre-configured run configurations in the toolbar:
- Select **Value Convergence** or **Policy Convergence**
- Click the green play button ▶️

Or use the terminal:
```bash
cd scripts/
python value_convergence.py
```

## That's it! 🎉

For troubleshooting and advanced configuration, see [PYCHARM_SETUP.md](PYCHARM_SETUP.md).
