"""
weather_quadratic_versions.py

All-in-one Python file demonstrating a simple "weather modelling" example
using a quadratic model (y = a*x^2 + b*x + c). The file contains
four staged versions:

1) hard-coded variables
2) keyboard input (interactive)
3) read from a file (single set)
4) read from a file (multiple sets)

Also includes:
- a stable quadratic solver (handles real/complex roots)
- a small "temperature model" wrapper showing how quadratic fits might be used
- sample input formats and example runs
- guidance for saving versions with git and pushing to GitHub
- debugging tips and common fixes

You can copy this file into a local folder, create the sample input files
listed below, and follow the git/GitHub instructions at the bottom.
"""
import math
import cmath
from typing import Tuple, List


def solve_quadratic(a: float, b: float, c: float) -> Tuple[complex, complex]:
    """Return the two roots of ax^2 + bx + c = 0 as complex numbers.

    Handles degenerate cases (a == 0) by returning a linear solution or
    raising a ValueError when both a and b are zero.
    """
    if abs(a) < 1e-12:  # treat as linear bx + c = 0
        if abs(b) < 1e-12:
            raise ValueError("Both a and b are zero; no solution (or infinite solutions if c==0)")
        root = -c / b
        return (root, root)
    disc = b * b - 4 * a * c
    if disc >= 0:
        sqrt_disc = math.sqrt(disc)
    else:
        sqrt_disc = cmath.sqrt(disc)
    r1 = (-b + sqrt_disc) / (2 * a)
    r2 = (-b - sqrt_disc) / (2 * a)
    return (r1, r2)


def temperature_model(a: float, b: float, c: float, x: float) -> float:
    """A simple quadratic-based "temperature" model.

    Interprets x as time (e.g., hour of day or day index) and returns
    the modeled temperature y = a*x^2 + b*x + c.
    """
    return a * x * x + b * x + c


# -------------------- STAGE 1: Hard-coded variables --------------------

def stage_hardcoded_demo():
    print("STAGE 1: Hard-coded variables demo")
    # Example coefficients: choose a small negative 'a' to represent a parabolic
    # diurnal temperature curve (peaks in middle of day)
    a, b, c = -0.05, 1.2, 15.0  # example
    print(f"Using coefficients: a={a}, b={b}, c={c}")
    for hour in [0, 6, 12, 18, 24]:
        temp = temperature_model(a, b, c, hour)
        print(f" hour={hour:2d} -> temp={temp:.2f} °C")
    roots = solve_quadratic(a, b, c)
    print(f"Quadratic roots: {roots[0]}, {roots[1]}\n")


# -------------------- STAGE 2: Keyboard input --------------------

def stage_keyboard_input():
    print("STAGE 2: Keyboard input demo")
    try:
        a = float(input("Enter coefficient a: "))
        b = float(input("Enter coefficient b: "))
        c = float(input("Enter coefficient c: "))
        x = float(input("Enter x to evaluate model at (e.g., hour): "))
    except ValueError:
        print("Invalid numeric input. Please enter numbers only.")
        return
    y = temperature_model(a, b, c, x)
    r1, r2 = solve_quadratic(a, b, c)
    print(f"Model at x={x} -> y={y:.4f}")
    print(f"Roots: {r1}, {r2}\n")


# -------------------- STAGE 3: Read from file (single set) --------------------

def read_single_from_file(filename: str) -> Tuple[float, float, float, float]:
    """Reads a single a b c x from file. Accepts whitespace separated values.

    Expected file content, example:
      -0.05 1.2 15.0 12
    which means a=-0.05, b=1.2, c=15, x=12
    """
    with open(filename, 'r') as f:
        contents = f.read().strip()
    if not contents:
        raise ValueError("Input file is empty")
    parts = contents.split()
    if len(parts) < 4:
        raise ValueError("File must contain at least 4 values: a b c x")
    a, b, c, x = map(float, parts[:4])
    return a, b, c, x


def stage_file_single(filename: str = 'input_single.txt'):
    print("STAGE 3: Read single set from file")
    try:
        a, b, c, x = read_single_from_file(filename)
    except Exception as e:
        print(f"Error reading file: {e}")
        return
    y = temperature_model(a, b, c, x)
    r1, r2 = solve_quadratic(a, b, c)
    print(f"File: {filename} -> coefficients: a={a}, b={b}, c={c}, x={x}")
    print(f"Model at x={x} -> y={y:.4f}")
    print(f"Roots: {r1}, {r2}\n")


# -------------------- STAGE 4: Read from file (multiple sets) --------------------

def read_multiple_from_file(filename: str) -> List[Tuple[float, float, float, float]]:
    """Reads multiple lines, each with a b c x (whitespace separated).

    Blank lines and lines starting with # are skipped. Returns list of tuples.
    """
    sets = []
    with open(filename, 'r') as f:
        for line in f:
            line = line.strip()
            if not line or line.startswith('#'):
                continue
            parts = line.split()
            if len(parts) < 4:
                raise ValueError(f"Line must contain at least 4 values: '{line}'")
            a, b, c, x = map(float, parts[:4])
            sets.append((a, b, c, x))
    return sets


def stage_file_multiple(filename: str = 'input_multiple.txt'):
    print("STAGE 4: Read multiple sets from file")
    try:
        sets = read_multiple_from_file(filename)
    except Exception as e:
        print(f"Error reading file: {e}")
        return
    for idx, (a, b, c, x) in enumerate(sets, start=1):
        y = temperature_model(a, b, c, x)
        r1, r2 = solve_quadratic(a, b, c)
        print(f"Set {idx}: a={a}, b={b}, c={c}, x={x} -> y={y:.4f}, roots=({r1}, {r2})")
    print()


# -------------------- Utilities: sample input file content --------------------

SAMPLE_INPUT_SINGLE = """
# input_single.txt
-0.05 1.2 15.0 12
"""

SAMPLE_INPUT_MULTIPLE = """
# input_multiple.txt
# a    b    c     x
-0.05 1.2 15.0 0
-0.05 1.2 15.0 6
-0.05 1.2 15.0 12
-0.05 1.2 15.0 18
-0.05 1.2 15.0 24
"""


# -------------------- Simple CLI to run stages --------------------

def main():
    print("weather_quadratic_versions: run different stages to try out the model")
    print("Choose stage to run: 1=hardcoded, 2=keyboard, 3=file single, 4=file multiple, 0=quit")
    while True:
        choice = input("Stage (0-4): ")
        if choice == '0':
            print("Bye")
            break
        elif choice == '1':
            stage_hardcoded_demo()
        elif choice == '2':
            stage_keyboard_input()
        elif choice == '3':
            filename = input("Filename for single-set input [input_single.txt]: ") or 'input_single.txt'
            stage_file_single(filename)
        elif choice == '4':
            filename = input("Filename for multiple-set input [input_multiple.txt]: ") or 'input_multiple.txt'
            stage_file_multiple(filename)
        else:
            print("Invalid choice; try 0-4.")


if __name__ == '__main__':
    main()


# -------------------- Git / GitHub workflow notes (copy into README) --------------------
#
# 1) Initialize a local git repo and save versions:
#    mkdir weather-model && cd weather-model
#    git init
#    (copy this file to weather_quadratic_versions.py)
#    git add weather_quadratic_versions.py
#    git commit -m "stage1: add hard-coded and solver"
#
# 2) After each stage change, save a new commit:
#    git add weather_quadratic_versions.py
#    git commit -m "stage2: interactive input"
#    git commit -m "stage3: file input (single)"
#    git commit -m "stage4: multiple file input"
#
# 3) Create a GitHub repository:
#    - Go to https://github.com and sign up / verify email (choose a username)
#    - Click "New" -> repository name e.g. "weather-quadratic-model"
#    - On the GitHub repo page it shows commands to push an existing repo:
#        git remote add origin git@github.com:YOURUSERNAME/weather-quadratic-model.git
#        git branch -M main
#        git push -u origin main
#
# 4) Useful extras:
#    - Create a .gitignore (e.g., __pycache__/, *.pyc)
#    - Add LICENSE (MIT recommended for student projects)
#    - Add README.md describing the stages and sample input files
#
# 5) Debugging tips and common fixes
#    - ValueError: file empty or missing values -> check file formatting and blank lines
#    - Non-numeric input in file -> strip BOM or hidden characters; print(repr(line)) to debug
#    - a close to zero -> solver switches to linear branch; ensure you're not expecting quadratic
#    - For unexpected complex results -> ensure coefficients produce positive discriminant if you
#      expect real roots (disc = b*b - 4*a*c)
#
# 6) Extending the model
#    - Fit coefficients a,b,c to real temperature data using least squares (numpy.polyfit)
#    - Use hourly data and evaluate fit error (MSE)
#    - Add plotting with matplotlib to visualize model vs data
#
# End of file

