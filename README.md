# Introduction

This readme will contain a short description with code snippets on how to go from a small (python) package to a bioconda installable package and biocontainer.

A very nice and detailed video about part of the process can be found [here](https://www.youtube.com/watch?v=hOuS6mXCwhk). This is highly recommended and definitely and while this guide is based on that, the video is definitely more thorough.

The python tool that will be used here is a simple python script to parse the header of a bam file and extract the barcode and umi. 

By following these steps you will obtain the following goals:
1. Make a simple python tool
2. Include all additional files to make it compatible to publish the tool to PyPi
3. Publish the python tool to PyPi, enabling installation of your tool through `pip`
4. Use the [bioconda/recipes](https://github.com/bioconda/bioconda-recipes/) github to push your tool to bioconda
5. Install your tool through bioconda
6. Run your tool through a (bio-)container (Docker, Singularity or Wave)

## Making a python tool

You can use any tool that you've created. The tool I will use as an example can be found [in this repository](https://github.com/ljwharbers/flexiplex_tag_formatter). The 'tool' itself is a simple python script that can be found in the `src/flexi_formatter` folder, [link to script](https://github.com/ljwharbers/flexiplex_tag_formatter/blob/master/src/flexi_formatter/reformat_flexiplex_tags.py). To briefly go over the script, there are 4 dependencies, `re` (used for regular expressions), `sys` (used for system functions such as writing to standard output), `typer` (used to read in command line options, for more information check their documentation [here](https://typer.tiangolo.com/), and `simplesam` (to read in and write out sam files). After that there is a `main()`  block that includes the `Typer` argument `infile: str`. This allows the user to supply the `infile` through command line invocation. 

You can check how to use the tool by running the python script with `--help` in your terminal, this does require that you have the `typer` module installed (possible for example through conda or pip). For example (assuming you are in the directory where the script is located): `python reformat_flexiplex_tags.py --help`

## Include all files requires for PyPi publishing (and local installation through pip)

There are multiple ways to enable your tool to be installed through pip (and later published to PyPi). This is just one example and of course you're free to use whatever method you prefer. Once you've done it once you can copy over most of the files to future tools to reduce having to manually create these every time.

1. Create a new folder for your tool. You should create two directories in here, `src` and `.github/workflows`. The `src` folder should contain your script and an `__init__.py` file that includes the version of your [script](https://github.com/ljwharbers/flexiplex_tag_formatter/blob/master/src/__init__.py). The `.github/workflows` folder will contain a deploy script to automatically deploy your tool to PyPi which will be explained later.

For software to know how to install and which requirements are needed you need, these files should be in the base directory (not `src` or `.github` subdirectory):
1. requirements.txt
   This file includes the exact requirements needed to run your tool. In my case this is a specific version of typer and simplesam. You can find my `requirements.txt` file [here](https://github.com/ljwharbers/flexiplex_tag_formatter/blob/master/requirements.txt).
2. setup.py
   This file just contains two lines and can be found [here](https://github.com/ljwharbers/flexiplex_tag_formatter/blob/master/setup.py).
3. setup.cfg
   Information about, among others, the version, author, email, licenses, and how to run the tool.
4. README.md
   Readme file with information about your tool
5. LICENSE
   License regarding use of your tool/code. 

Now you should already be able to install your tool simply by running `pip install .` in the directory.

## Automatically publish your tool to PyPi

To automatically upload the tool whenever we do a certain 'action', we can use github actions. We will include a file called `deploy-pypi.yml` in the `.github/workflows` directory. This file can be found [here](https://github.com/ljwharbers/flexiplex_tag_formatter/blob/master/.github/workflows/deploy-pypi.yml), and has three key sections. 
1. name:
   the name of the action.
2. on:
   When to run the action. In my example this will be run whenever we release and publish our tool on github.
3. jobs:
   The exact steps it will run when the action is triggered.

For this to work you need a PyPi account and have a PyPi token generated for github to use. You can do this here: https://pypi.org/manage/account/. Once you have created the API code, go to your github repository, go to setttings -> secrets -> actions and add a new repository secret called `PYPI_API_TOKEN` with the token.

Now, whenever you make a new release on your github, a new action should start (which you can view under `Actions` tab in your repository. After this is complete, your tool should be available on PyPi (give it a little time to process and update).

## Use the bioconda/recipes github to push your tool to bioconda.

Bioconda (and conda-forge for non bioinformatics specific tools) has an in-depth guide available [here](https://bioconda.github.io/contributor/guidelines.html).

In short, you need to fork the bioconda/recipes github repository: https://github.com/bioconda/bioconda-recipes/
Then you need to add a folder for your tool in the `recipes` folder. Here you need to include a `meta.yaml` file with all the instructions for your tool to be published to bioconda and subsequently containerized in bio-containers. An example of my file can be found [here](https://github.com/bioconda/bioconda-recipes/blob/master/recipes/flexi-formatter/meta.yaml). Once you've created the `meta.yaml` you can PR request to get it to build and merge in the bioconda-recipes github. Once this succesfully merges, your tool will be available in bioconda and also as a docker/singularity container: https://biocontainers.pro/


