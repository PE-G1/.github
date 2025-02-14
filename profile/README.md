# PE Group 1

## Working on your project

To work on your project, clone your repository and start working locally. **DO NOT push data or model weights to GitHub** as these are protected by privacy laws. You can add files that you do not want to consider for commits to your repository's `.gitignore` to ensure they are not included in your commits automatically. Each repository should include a `README` file that includes how to run your repository as well as how to run the tests.

## General rules

### Write tests for functions

It is important to write tests, mainly for our benefit but also to inspire confidence in the product. This means writing unit tests for classes, functions, integration tests. You have to make sure your code does what is expected. For a part of the frontend that displays a plot, make sure it works with random data fed to it. With ML algorithms and models, make sure it can overfit to random data points, etc. We will use `pytest` as a Python library to write tests. An appropriate `/tests` library should exist for each repository, with subfolders according to the behavior tested.

### Use Virtual environments

To ensure portability, we will use virtual environments to encapsulate behavior to a set of predetermined versions of libraries and Python version. We will have, *for each repository*, a `requirements.txt` file that stores the dependencies *with their respective versions*. To start working on your project, here is an example of a workflow:

0. If you haven't already, create a virtual environment (here we use venv) called my_virtual_environment.

```
python3 -m venv my_virtual_environment
```

1. Activate your virtual environment.

```
source ./my_virtual_environment/bin/activate
```

2. Work on your project [...] This can include installing libraries via pip:

```
pip3 install numpy
```

3. If you installed new libraries, make sure to update the `requirements.txt` file using `pip freeze`:

```
pip freeze > requirements.txt
```

4. When you're done working, deactivate the virtual environment.

```
deactivate
```

5. If you need to recreate the environment on another machine, set up a new virtual environment and install dependencies from requirements.txt:
   
```
python3 -m venv my_virtual_environment  
source ./my_virtual_environment/bin/activate  
pip install -r requirements.txt
```

### Write robust code

Nothing should be hardcoded. If we repeat the experiment with slight changes in the way we use, collect or receive data, or even changes in the data itself, the code should still perform as expected. 

Use `try-except` clauses to perform error control. For example, the following code throws an error and stops the execution if `do_X()` throws an error:

```
do_X()
```

However, the following alternative controls the error, notifies appropriately and does not interrupt execution flow:

```
try:
  do_X()
  logger.info("Done X successfully!")
except Exception as e:
  logger.error(f"There was an error doing X: {e}")
```

Consider also using retries!

Finally, opt for parametrized executions for a better scalability of the code base: changing the parameters of the execution should **NOT** require accessing the source code (Python, etc). 

This can be done via `.yaml` files or through CLI such as `argparse`. We will prefer `.yaml` files, which you can use in this manner:

If your `.yaml` file looks like this:

```
arguments:
  first_arg: 1
  second_arg: 2
```

Then to access this, you should parse it using the `PyYAML` library. First, install the library if you haven't already:  

```bash
pip install pyyaml
```

Then, you can load and access the parameters in your Python script as follows:  

```python
import yaml

# Load YAML configuration
with open("config.yaml", "r") as file:
    config = yaml.safe_load(file)

# Access parameters
first_arg = config["arguments"]["first_arg"]
second_arg = config["arguments"]["second_arg"]

print(f"First argument: {first_arg}, Second argument: {second_arg}")
```

If you need to override parameters dynamically, you can combine `.yaml` files with command-line arguments using `argparse`:  

```python
import yaml
import argparse

# Load YAML configuration
with open("config.yaml", "r") as file:
    config = yaml.safe_load(file)

# Set up argument parsing
parser = argparse.ArgumentParser()
parser.add_argument("--first_arg", type=int, default=config["arguments"]["first_arg"])
parser.add_argument("--second_arg", type=int, default=config["arguments"]["second_arg"])
args = parser.parse_args()

print(f"First argument: {args.first_arg}, Second argument: {args.second_arg}")
```

This approach ensures that:
- **Default values come from `config.yaml`**, but they can be **overridden via CLI**.
- **No source code modification** is required to adjust execution parameters.
- **Experiments and configurations remain reproducible** while allowing flexibility.

### Log, Log, Log!

Use logging to debug, inform and keep a trace of application behavior. We will use the `loguru` library for Python, and design a `logs` directory to store the logs. Make sure to use a configuration that is common across the project: TBD. As an example of the library use:

```
from loguru import logger
logger.add("logs/debug.log", rotation="10 MB", level="DEBUG")
```

### Write modular code

Design classes to encapsulate objects and behavior. Ensure these are precise and useful building blocks for the project.

### Programming conventions

We will use the Google Style Guide for programming conventions: https://google.github.io/styleguide/ (Python: https://google.github.io/styleguide/pyguide.html).
Please ensure you are using this throughout to have a coherent and homogenous codebase.

The most important things:
- Install Black formatter (this is a VSCode extension, enabling format on save is recommended).
- We will use snake_case for variables, functions, methods and CamelCase for class names (snake case means we_write_like_this and camel case means weWriteLikeThis).
- Use typing whenever possible: this is a rich way of easing the process of reading/understanding code and avoiding errors. Here's an example:

```
def sum(a:int, b: int) -> int:
   return a+b
```
- Use documentation on each method and function. We will follow the conventions showed in the example below:

 ```
def sum(a:int, b:int) -> int:
"""
Adds a and b.

Args:
   - a (int): number to add
   - b (int): number to add

Returns:
   - Sum of both numbers (int)
"""
   return a+b
```
- Add comments if you think the code isn't self-explanatory. Comments should be concise and useful to another programmer that did not code the function but has to use it.

## Using VCS

We (obviously) use git for version control. Because this is a large project, we can't just all work in the main branch and solve conflicts as we go. Some basic rules:

### Working on your branch

By default, do not touch a branch that is not yours without the branch owner's permission/consent.

Beginner level:
- Have a branch with your name as name and perform commits to it. When you want to merge to main, create a pull request (PR) to main and wait for it to be approved by at least one of your fellow repository contributors to merge to main. Please do not break anything when merging to main, rewriting commit histories is not easy!

Advanced level:
- You can have branches for each feature you are trying to implement. Follow the abovementioned procedure to merge changes into main via PRs.

### Commits

Write useful, concise and informative commit messages. Have a commit for each action you try to do. For example, if you fix bug X and add feature Y, instead of writing a commit with message: *Fixed bug X and added feature Y*, write two commits encapsulating each change thematically: *Fixed bug X* and *Added feature Y*. This allows for finer granularity, for example if I only need bug X fixed and do not want feature Y, I can cherry-pick the first commit in the latter scenario, but not in the first.
