# README_UCSB.md

This fork of https://github/PrairieLearn/PrairieLearn exists for one purpose:

* We make small changes in the subdirectory grader/python, and then use the files in that directory to build a new docker image suitable
  for writing PrairieLearn autograders with the necessary Python dependencies for grading CMPSC 5A and CMPSC 5B questions.

  Specficially, we need the `datascience` module that is associated with the [*Data 8* curriculum from UC Berkeley](https://www.data8.org); the curriculum that goes
  with the textbook [Inferential Thinking, by Ani Adhikari and John DeNero and David Wagner](https://inferentialthinking.com/chapters/intro.html).

## Instructions for building the Docker Image

To build the docker image:

1. Create a docker account at https://hub.docker.com
2. Download the Docker software for your platform [Windows](https://docs.docker.com/desktop/setup/install/windows-install/), [Mac](https://docs.docker.com/desktop/setup/install/mac-install/).  You can navigate from those sites to installation instructions for other platforms if needed.
3. Open a terminal session, and use the command `docker login` to login to your account.
4. Clone this repo
5. `cd` into the directory `graders/python`
6. Make whatever changes you need to make; typically this is simply editing the file `requirements.txt`
   to add additional Python packages that you would normally `pip install` when working normally.

   You will need to add both the package name, and a version number, using the correct syntax for `requirements.txt`.
   If you are unsure, go to a different directory, and do `pip install packageName` followed by `pip freeze`, and it will show you the correct line in the correct syntax.
7. Create the docker image with this command.  Here, the `phtcon` is standing in place for your docker login
   ```
   docker buildx build --platform linux/amd64,linux/arm64 -t phtcon/grader-python:latest --push .
   ```
   Note that the `linux/amd64` part is important.  If you leave this out it will not work in the PrairieLearn environment.  The `linux/arm64` part is
   only there so that the Docker image can also be run separately on Macs with the newer "Apple Silicon" chips.   You may add other architectures if
   needed for the dev team, but only the `linux/amd64` part is necessary for running directly on PrairieLearn.
8. When this command is complete, you will have an image named, for example, `phtcon/grader-python:latest` than can be mentioned in the files for a
   PrairieLearn question that requires the specific environment for UCSB CMPSC 5A/5B.

   For example, you would put the line in the correct spot (illustrated below) in the `info.json` file
   when creating a PrairieLearn question.

   ```json
      "image": "phtcon/grader-python:latest",
   ```

   This shows the full context of the contents of `info.json`, including where this line goes:
   
   ```json
   {
    "uuid": "30fcdc49-3692-47dc-85a2-this-must-be-your-own-unique-valid-uuid",
    "title": "Show Labels",
    "topic": "6. Tables",
    "tags": ["code", "basic table ops", "datascience lib"],
    "type": "v3",
    "singleVariant": true,
    "gradingMethod": "External",
    "externalGradingOptions": {
      "enabled": true,
      "image": "phtcon/grader-python:latest",
      "entrypoint": "/python_autograder/run.sh",
      "timeout": 20
    }
  }
  ```
   
   
