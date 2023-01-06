# Table of Contents

- [Table of Contents](#table-of-contents)
- [Best Practice](#best-practice)
  - [Organize Samba Share](#organize-samba-share)
  - [Utilize `git` for version control](#utilize-git-for-version-control)
  - [Use various tools installed on the server](#use-various-tools-installed-on-the-server)
  - [Working with Python Projects](#working-with-python-projects)
  - [Working with Mathematica Projects](#working-with-mathematica-projects)
  - [Working with Comsol Projects](#working-with-comsol-projects)


# Best Practice

## Organize Samba Share

Samba share is a public folder that can be accessed by all users. It is a good place to store your data that you want to share with other users. However, it is not a good place to store your personal data. For example, you should not store your personal files (i.e. credentials) in the samba share folder. Instead, you should create a folder in your home directory and store your personal files there.

Your samba share can be accessed locally from `~/Samba/` or `/home/Samba/profiles`. Alternatively, you can access it remotely using Windows Explorer from `\\phz8213.phys.ust.hk\profiles\`.

Here is a recommended folder structure for your samba share:

```
username
├── README.md           # A README file to describe the folder structure
├── Presentation        # Group meeting presentations
│   └── 230106 - Title  # Presentation for group meeting on 06 Jan 2023
├── Data                # Experimental data/logs/large files
│   ├── Designs          
│   └── Measurements          
├── Drivers             # Device drivers
│   └── Device1
│       ├── README.md   # Quick start, credential, contact points, etc.
│       └── Software    
├── Projects/Code       # Project codes
│   ├── Project1
│   ├── Project2
│   └── Project3
└── Others
```

For Presentation and Data folder, please prefix the folder/file name with the date of the experiment, e.g. `230106 - Experiment`. This will help you to keep track of your data.

**Important**: Please also store all driver, shared credential (to download the driver), contact points, and documentation. This will help other users to use the device.

## Utilize `git` for version control

`git` is a version control system that allows you to track changes to your files. It is a good practice to use `git` to track your project codes.

I highly recommend you to version control your code using git and push it to Github. There is some learning curve to get started with `git`, but it is worth the effort.

Some use cases I find git useful:
1. Easily access your project anywhere using Github
2. Ability to track changes and revert breaking changes to your code. (no need to store multiple copies of your code)
3. Ability to collaborate with others on the same project using different git branches

Here are some useful resources to get started with `git`:
1. [Git Tutorial](https://www.atlassian.com/git/tutorials)
2. [Github Guides](https://docs.github.com/en)

## Use various tools installed on the server

1. Use `btop` to monitor your CPU and memory usage. Please alert the admins if you find the CPU temperature is above 80°C.
2. Use `screen` to run long running jobs in the background. You can use `screen -r` to resume the job later.
3. Use `micro` to edit your files. It is a terminal based text editor that is easy to use. Or `nvim` if you prefer vi like syntax.
4. Use `lf` to as terminal file manager. It is a good alternative to `ranger` (also installed). Find the documentation [here](https://pkg.go.dev/github.com/gokcehan/lf).
5. Use `tldr` to find the usage of a command. It is a good alternative to `man`. Run `tldr -u` to update the cache first. Find the documentation [here](https://tldr.sh/).

## Working with Python Projects

`poetry` is a python package manager that allows you to manage your python packages in a virtual environment. It is a better alternative to `virtualenv` and `miniconda`. You can find the documentation [here](https://python-poetry.org/docs/).

 - Create a new Poetry project in the directory with a specific name:  
   `poetry new {{project_name}}`

 - Install a dependency and its subdependencies:  
   `poetry add {{dependency}}`

 - Install all dependency and sync with pyproject.toml:  
   `poetry install --sync`

 - Run a script defined in pyproject.toml:  
   `poetry run python {{script}}`

 - Run interactive shell with the virtual environment activated:  
   `poetry shell`

## Working with Mathematica Projects

Mathematica is a functional programming language our group used primarily for simulation.

Here is some tips to write better code in mathematica:
1.  Shorter is better  
    Mathematica is a functional programming language. It is a good practice to write short and concise code. Learn the [mathematica syntax](https://reference.wolfram.com/language/guide/Syntax.html) and use it to your advantage.
2.  Do not write unnecessary *"helper"* function. 
    It's not C++ or python. Probably you don't need that function.
4.  Use `Map` when manipulating data  
    Mathematica has a built-in function called `Map` that allows you to apply a function to a list of data. It is a good practice to use `Map` instead of `For` loop when manipulating data. 
5.  Use `Module` to avoid global variables
6.  Use `With` for variable substitution

## Working with Comsol Projects

TBD