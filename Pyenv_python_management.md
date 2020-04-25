
# Python

There are different ways to install python, you could tell the difference by checking the direcotry where it is sitting. 

If the python is installed by mac os, then it sits in: 
`/Library/Frameworks/Python.framework/Versions/3.8/bin/python3`

If the python is installed using pyenv, then it sits in one of the shims from pyenv: 

`/Users/shirzart/.pyenv/shims/python`

One of the seamingly good principle to follow is: always use python build with pyenv on different projects. Don't solely rely on system python which is inside the Framework direcotry. 

# Pyenv operations:
## Actiate:
`pyenv activate`

## Deactivate:
`deactivate` or `source deactivate`


# Pipenv
This is one thing to consider to make the pacakge and environment management easier:
https://gioele.io/pyenv-pipenv



# pip operation
Uninstalling all dependencies: 
`pip freeze | xargs pip uninstall -y`


# Create miniconda inside pyenv
`pyenv virtualenv miniconda3-latest [envname]`
*Remember* The environment created under miniconda should be acticated using `conda activate` not `pyenv activate`
Or to be more explicite: 1) pyenv should activate a virtualenv, which could be done automatically when a local directory is set to a virtualenv version. 2) When this virtualenv is activated, or you are inside the directory which this virtualenv was point to, you need to use`conda activate` to proper activate the conda environment.

*Otherwise, it could lead to not using the right pip or python while in miniconda, causing package installation in global directory*


# github fatal error: repo not found
This is mainly due to your authentification process to github is forgot? or lost? 
The way to fix it is: `git remote set-url origin https://YOUR-GIT-Username@github.com/organization/repo.git`
I had a case where this solution didn't even work, so I used another approach described in this link: https://stackoverflow.com/questions/22844806/how-to-change-my-git-username-in-terminal
> git config user.email "you@example.com"
> git config user.name "Your Name"
> git config user.password "your password"
But maybe I will need some other ways to crack it sometime.




# Common Pandas pitfalls
## Assigning value on a view or a copy of a dataframe casue failure
Due to various reason, you need to update a value of a dataframe cell 


# Pandas
## Slicing data frame according to multiple criteria
df.loc[(df["B"] > 50) & (df["C"] == 900), "A"]

# SQL Setup:
https://www.codementor.io/@engineerapart/getting-started-with-postgresql-on-mac-osx-are8jcopb 
