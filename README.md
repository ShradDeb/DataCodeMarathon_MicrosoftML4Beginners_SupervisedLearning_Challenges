# 🧑‍💻 From “ModuleNotFoundError” to Model Fit: My Week of ML Debugging ⚙️

I hit a bunch of classic pitfalls while rebuilding Microsoft’s **“ML for Beginners”** labs locally.  
Here’s every issue I faced — and how I fixed it — so you don’t lose hours like I did.

---

## 1️⃣ Missing Module  
**Error:**  
`ModuleNotFoundError: No module named 'm0b_optimizer'`

**Cause:**  
The module wasn’t on PyPI; it was a local file in my repo.

**Fix:**
- Verified the file existed (`m0b_optimizer.py`) in my project.  
- Ensured my notebook ran from that folder (or added the folder to `sys.path`).  
- When needed, I moved/copied the file into the working project directory.

💡 **Pro tip:** If `pip install <name>` fails with *“No matching distribution”*, it’s probably not a published package — use the source file directly.

---

## 2️⃣ Importing a Helper from GitHub but Getting 404 / SyntaxError  

**Errors:**
- Saved a GitHub HTML page as `.py` →  
  `SyntaxError: illegal target for annotation` with *404: Not Found* at the top.  
- `ModuleNotFoundError` for `microsoft_custom_linear_regressor`.

**Fix:**
- Always download the **Raw** file (not the HTML page).  
- Replace the bad file and verify the first lines are Python, not HTML.  
- When links were flaky, I created a local drop-in version of the class (`MicrosoftCustomLinearRegressor`) so I could keep moving.

💡 **Pro tip:**  
Use:
```bash
curl -L -o file.py https://raw.githubusercontent.com/...
head -n 10 file.py
```
## 3️⃣ Wrong Import Name for 3D Plotting
Error:
ImportError: cannot import name 'mplots3d'
Cause: Typo.
Fix:
from mpl_toolkits import mplot3d
# or
from mpl_toolkits.mplot3d import Axes3D

## 4️⃣ NumPy Function Typo
Error:
AttributeError: module 'numpy' has no attribute 'linespace'
Cause:
Used np.linespace instead of np.linspace.
Fix:
intercepts = np.linspace(-100, -70, 10)

## 5️⃣ Variable Name and Column Access Mismatches
Symptoms: Name errors or wrong plotting.
Fixes:
X vs x: use a single, consistent variable (e.g., x = data["year"]).
Typo: temperatire_estimate → temperature_estimate.
Pandas column access: dataset["TMIN"] (use strings, not bare names).

## 6️⃣ Method Signature Mismatch on .fit()
Error:
TypeError: fit() got an unexpected keyword argument 'cost_function'
Cause:
My local MicrosoftCustomLinearRegressor.fit() accepted only (X, y),
but the notebook passed cost_function= (from a different tutorial version).
Fix (two options):
Simple: remove cost_function=
model = MicrosoftCustomLinearRegressor().fit(X=dataset["year"], y=dataset["TMIN"])
Flexible: update the class to accept cost_function=None and handle it internally.
💡 Pro tip:
Use:
import inspect
print(inspect.signature(Class.method))
to see which parameters your local version supports.

## 7️⃣ Plotting After Fit
Gotchas fixed:
x = dataset["year"].values
plt.plot(x, model.predict(x))
plt.xlabel("Year")
plt.ylabel("Min Temperature")
plt.legend()
plt.show()

## 8️⃣ Concept Checks That Saved Me Time
Gradient Descent: Uses calculus (derivatives) to follow the slope of the cost function — not random trials.
Cost Functions: Choose based on your goal — MSE for regression, cross-entropy for classification.
Learning Rate: Controls both speed and convergence quality. Too large → diverge; too small → crawl.

## 🧭 Debugging Habits That Helped

Read the exact error text — it usually reveals the cause.
Verify working directory (os.getcwd()) and list files (os.listdir()).
Check function signatures with inspect.signature(...).
Use Raw GitHub downloads, not HTML pages.
Make small, testable changes — import first, fit next, then plot.
If this helped, save it for your next ML project!
If you’ve fought similar “tiny typo, huge detour” bugs, share yours — I’d love to add them here. ✨

