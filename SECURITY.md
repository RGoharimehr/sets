# Security Advisory

## PyTorch Vulnerabilities in Original Environment

The original `environment.yml` file specifies PyTorch 1.12.1 with Python 3.7.16. This combination has known security vulnerabilities:

### Identified Vulnerabilities

1. **Heap Buffer Overflow** (CVE)
   - Affected: PyTorch < 2.2.0
   - Patched: 2.2.0+

2. **Use-After-Free Vulnerability** (CVE)
   - Affected: PyTorch < 2.2.0
   - Patched: 2.2.0+

3. **Remote Code Execution via torch.load**
   - Affected: PyTorch < 2.6.0
   - Patched: 2.6.0+
   - Note: Using `weights_only=True` in vulnerable versions still allows RCE

4. **Arbitrary Code Execution**
   - Affected: PyTorch <= 1.13.0
   - Patched: 1.13.1+

5. **Deserialization Vulnerability**
   - Affected: PyTorch <= 2.3.1
   - Status: Withdrawn advisory (may be false positive)

## Recommended Actions

### Option 1: Use Secure Environment (Recommended)

Use the new `environment-secure.yml` with Python 3.9 and PyTorch 2.6.0+:

```bash
conda env create --file environment-secure.yml
```

**Benefits:**
- ✅ All known vulnerabilities patched
- ✅ Uses supported Python version (3.9)
- ✅ Modern PyTorch with latest features
- ✅ Better performance

**Potential Issues:**
- May require code adjustments if incompatibilities exist
- Newer dependencies might behave differently

### Option 2: Minimal Security Update (Python 3.7 Only)

If you must use Python 3.7, upgrade to PyTorch 1.13.1 (maximum version for Python 3.7):

Edit `environment.yml`:
```yaml
- python=3.7.16
- pytorch=1.13.1  # Changed from 1.12.1
```

**Benefits:**
- ✅ Fixes arbitrary code execution vulnerability
- ✅ Maintains Python 3.7 compatibility

**Limitations:**
- ❌ Does NOT fix heap buffer overflow (requires 2.2.0+)
- ❌ Does NOT fix use-after-free (requires 2.2.0+)
- ❌ Does NOT fix torch.load RCE (requires 2.6.0+)
- ⚠️ Python 3.7 reached end-of-life on June 27, 2023

### Option 3: Continue with Original (Not Recommended)

Keep using `environment.yml` with Python 3.7.16 and PyTorch 1.12.1.

**Only consider this if:**
- You are working in an isolated/air-gapped environment
- You do not load any untrusted .pt/.pth model files
- You do not process untrusted input data
- You understand and accept the security risks

## Security Best Practices

Regardless of which option you choose:

1. **Never load untrusted model files**
   - Avoid loading .pt, .pth, or pickle files from untrusted sources
   - Use `torch.load(..., weights_only=True)` only on PyTorch 2.6.0+ where it's properly patched

2. **Validate input data**
   - Sanitize all external inputs
   - Implement proper input validation

3. **Keep dependencies updated**
   - Regularly check for security updates
   - Subscribe to PyTorch security advisories

4. **Use virtual environments**
   - Isolate project dependencies
   - Prevent system-wide compromise

5. **Monitor for vulnerabilities**
   - Use tools like `pip-audit` or `safety` to check dependencies
   - Review security advisories regularly

## Testing Compatibility

After updating to a secure environment, test the code:

```bash
# Activate the secure environment
conda activate sets

# Build C++ extensions
cd src/build
cmake -DPYTHON_EXECUTABLE=$(which python) -DCMAKE_BUILD_TYPE=Release ..
make

# Run test scripts
cd ../../scripts
python value_convergence.py
```

If you encounter issues, please report them so we can address compatibility problems.

## Additional Resources

- [PyTorch Security Advisories](https://github.com/pytorch/pytorch/security/advisories)
- [Python 3.7 EOL Announcement](https://www.python.org/downloads/release/python-3718/)
- [Conda Security Best Practices](https://docs.conda.io/projects/conda/en/latest/user-guide/tasks/manage-environments.html)

## Questions?

If you have questions about these security recommendations, please open an issue in the repository.
