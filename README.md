# Hakuna Ma-data: Identify Wildlife on the Serengeti with AI for Earth

This repository contains runtime configuration for the [Hakuna Ma-data: Identify Wildlife on the Serengeti with AI for Earth](https://www.drivendata.org/competitions/59/camera-trap-serengeti/) competition, as well as example benchmark solutions.

## Adding dependencies to runtime configuration

We accept contributions to add additional dependencies to the runtime environment. To do so, you'll have to follow these steps:

1. Fork this repository
2. Make your changes, test them, and commit using git
3. Open a pull request

Our repository is set up to run some automated tests using Azure Pipelines, and our team will review your pull request before merging.

If you're new to the GitHub contribution workflow, check out [this guide by GitHub](https://guides.github.com/activities/forking/).

### Python

We use conda to manage Python dependencies.

Add your new dependencies to both `runtime/py-cpu.yml` and `runtime/py-gpu.yml`.

Please also add your dependencies to `runtime/tests/test-installs.py`, below the line `## ADD ADDITIONAL REQUIREMENTS BELOW HERE ##`.

### R

We prefer to use conda to manage R dependencies. Take a look at what packages are available from [Anaconda's `pkgs/r`](https://repo.anaconda.com/pkgs/r/) and from [`conda-forge`](https://conda-forge.org/feedstocks/). Note that R packages in conda typically start with the prefix `r-`. Add your new dependencies to both `runtime/r-cpu.yml` and `runtime/r-gpu.yml`.

If your dependencies are not available from the Anaconda or `conda-forge`, you can also add installation code to both the install scripts `runtime/package-installs-cpu.R` and `runtime/package-installs-gpu.R` to install from CRAN or GitHub.

Please also add your dependencies to `runtime/tests/test-installs.R`, below the line `## ADD ADDITIONAL REQUIREMENTS BELOW HERE ##`.

### Testing new dependencies locally

Please test your new dependency locally by recreating the relevant conda environment using the associated `.yml` file (and running the associated `package-installs-*.R` script if R). Try activating that environment and loading your new dependency.

If you would like to locally run our CI test (this requires [Docker](https://www.docker.com/products/docker-desktop)), you can use:

```bash
cd runtime
docker build --build-arg CPU_GPU={{PROCESSOR}} -t ai-for-earth-serengeti/inference .
docker run --mount type=bind,source=$(pwd)/run-tests.sh,target=/run-tests.sh,readonly \
                  --mount type=bind,source=$(pwd)/tests,target=/tests,readonly \
                  ai-for-earth-serengeti/inference \
                  /bin/bash -c "bash /run-tests.sh {{PROCESSOR}}"
```

replacing `{{PROCESSOR}}` with `cpu` or `gpu` as appropriate for your setup.

### Opening a pull request

After making and testing your changes, commit your changes and push to your fork. Then, when viewing the repository on github.com, you will see a banner that lets you open the pull request. For more detailed instructions, check out [GitHub's help page](https://help.github.com/en/articles/creating-a-pull-request-from-a-fork).

Once you open the pull request, Azure Pipelines will automatically try building the Docker images with your changes and run the tests in `runtime/tests`. These tests take up to an hour to run through, and may take longer if your build is queued behind others. You will see a section on the pull request page that shows the status of the tests and links to the logs.

You may be asked to submit revisions to your pull request if the tests fail, or if a DrivenData team member asks for revisions. Pull requests won't be merged until all tests pass and the team has reviewed and approved the changes.

## Testing submission with benchmark model

Testing the submission locally requires [Docker](https://www.docker.com/products/docker-desktop) and a set of images to predict on in the `inference-data` directory.

First, to prepare necessary files for submission, run:

```bash
bash prep.sh
```

This prepares the Python benchmark solution by default. You can also directly specify whether to prepare the Python or R benchmark solutions with `bash prep.sh py` or `bash prep.sh r`, respectively.

Next, build and run the Docker container with

```bash
bash run.sh
```
