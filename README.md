# `algofilter` - Modular pipelines for processing Algorand transaction data

## Overview

It is assumed that you are running an Algorand Node (algod) on the same server.
The basic goal is to read blocks as they are coming in, filter ones that are relevant, extract necessary information, and trigger responses or
record data.

This is set up as a series of stages, with different scripts/programs available in each part, so that they can be composed via pipes.
Each script reads from STDIN and writes to STDOUT.
The project will be much more useful if people contribute their own scripts for filtering, extracting etc. via pull requests.
d
But it can serve as a simple framework for any custom task.

## Installation

You must have the `goal` Algorand binary installed.

To run the example which is set up as default, you will need the `jq` program. 
You can install it on Ubuntu with `sudo apt-get install jq`.  The example also assumes you have `bc` (calculator) installed on your system.
However, you can use any programs/libraries you want for reading the JSON data. This is just an example.

You can run `cp pipelines_example pipelines` to try the example pipeline.

## Usage

The core of the program is just a shell script (`app/watchblocks`) that looks for new blocks and pipes the transactions
through each line of the `pipelines` script.
Once you have followed the installation instructions, and configured your pipelines, you can run it with `./startdaemon`.

## Pipeline stages

```sh
   ________      _________      ______
  | Filter | -> | Extract | -> | Exec |
   ¯¯¯¯¯¯¯¯      ¯¯¯¯¯¯¯¯¯      ¯¯¯¯¯¯
```

Many other pipelines are possible, such as having two filter stages.

### Filter

Example `all`. Passes all transactions through.

File `filter/all`.

```sh
#!/bin/bash
cat
```

Example `pay`. All payment transactions (non close-out).

```sh
#!/bin/bash
jq 'select(has("amt"))'
```

### Extract

Example `amount`. Pull out amount from transaction record.

```sh
#!/bin/bash
jq .amt
```

### Exec

In this stage you may run a script or program to trigger an action based on the extracted data.

Example `printalgo`. Convert microalgos to ALGOs and print.

```sh
mapfile -t lines
for microalgo in "${lines[@]}"; do
  algo=$(echo "scale=3; $microalgo / 1000000" | bc)
    echo "$algo ALGO"
  done
```

## `pipelines` script

Each line in this file is run in parallel using the block of transactions as input.

Example:

```sh
filter/txns | extract/formatjson >> output/txns.json &
filter/txns | filter/acfg | extract/formatjson >> output/acfg.json &
filter/txns | filter/pay | extract/amount | exec/printalgo >> output/amounts &
```
