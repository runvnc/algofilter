# `algofilter` - Modular pipelines for processing Algorand transaction data

## Overview

It is assumed that you are running an Algorand Node (algod) on the same server.
The basic goal is to read blocks as they are coming in, filter ones that are relevant, extract necessary information, and trigger responses or
record data.

This is set up as a series of stages, with different scripts/programs available in each part, so that they can be composed via pipes.
The project will be much more useful if people contribute their own scripts for filtering, extracting etc. via pull requests.

But it can serve as a simple framework for any custom task.

## Usage

The core of the program is just a shell script (`app/watchblocks`) that looks for new blocks and then sends them to the `pipelines` script.
Once you have followed the installation instructions below, and configured your pipelines, you can run it with `./startdaemon`.

## Installation

You must have the `goal` Algorand binary installed.

To run the example which is set up as default, you will need the `jq` program. 
You can install it on Ubuntu with `sudo apt-get install jq`.
However, you can use any programs/libraries you want for reading the JSON data. This is just an example.

## Pipeline stages

```sh
  _Filter_ -> _Extract_ -> _Exec_
```

### Filter

Example `all`. Passes all transactions through.

File `filter/all`.

```sh
#!/bin/bash
cat
```


### Extract

### Exec

In this stage you may run a script or program to trigger an action based on the extracted data.


## `pipelines` script

