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

## Sharing the image with a development team

Note that the Docker repository created above, namely `phtcon/grader-python`, is associated with a specific individual account (namely `phtcon`, which is
associated with Phill Conrad <phtcon@ucsb.edu>, who led the initial development effort to make PrarieLearn questions for the Data 8 curriculum as taught
in CMPSC 5A/5B at UC Santa Barbara.

(Note: in the paragraph above, we are referring to a *Docker repository*, not a Github repository).

The question arises: how do you share this with an entire development team that may change from term to term, as different instructors teach the course, and as those different instructor employ various Teaching Assistants, Learning Assistants, and other staff to help with developing PrairieLearn questions?

One might initially propose creating a Docker "Organization", similar to a Github Organization (for example, `ucsb-ds` is a Github Organization shared by various faculty from both the Computer Science department, and the PSTAT department at UCSB).  Unfortunately, it no longer seems to be possible to create a Docker organization on a "free" plan, the way it is possible for Github.   

Therefore, the best currently available solution seems to be this:
* Create the image under the personal account of a faculty member that will likely be around for a while.
* Use the features of Docker to add/delete "collaborators" that are other instructors, TAs, LAs, etc.
  The idea is that collaborators are allowed to  "push", while everyone is allowed to "pull"
  (since the image is "public", and must be in order for PrairieLearn to be able to access it.

   
