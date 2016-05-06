# torpor

Torpor lets you quantify and reduce the performance variability 
associated to a computing platform. It achieves this by obtaining base 
metrics of a system and tuning container execution parameters of other 
systems where the original performance is intended to be replicated.

--------

Generate performance metrics for a system:

```bash
docker run --rm ivotron/microbench
```

The above generates JSON output and we'll assume is placed in a 
`base.json` output file.

--------

Tune a target system to replicate the performance of a container:

```bash
torpor tune
```

Which expects a `base.json` file in the current directory and 
generates the values of tunable parameters (in a `parameters.json` 
file). Example output:

```javascript
{
  "cpu-quota": 68700
}
```

--------

Finally, to run a container with the intent of replicating 
performance:

```bash
docker run --rm --cpu-quota 68700 repo/container
```

Which expects a `parameters.json` file in the current directory. The 
arguments to `run` are similar to the ones for `docker run` but 
`torpor` adds the values for the  `--cpu-quota` and `--mem-bw-limit` 
parameters that are passed to the 
[docker-run-wrapper](docker/docker-run-wrapper) command.

# `base.json` file

The results that are intended to be ported to other target platforms 
are contained in one or more files in JSON format. For example, the 
results of the STREAM micro-benchmark might be specified as:

```javascript
[
  {
    "name": "stream-copy"
    "class": "memory",
    "result": "4058"
  }
]
```

In general, the schema for results has the form:

```javascript
[
  {
    "name": "name of benchmark",
    "class": "one of memory|processor|network|io",
    "result": "number"
  },
  {
    ...
  }
]
```

If class is `processor`, units should be in seconds. If `memory`, 
`network` or `io`, units are rate-based (e.g. mb/s).

# Predefined micro-benchmarks

The `ivotron/microbench` docker image contains a list of commonly used 
micro-benchmarks. More benchmarks are available in 
[`ivotron/docker-bench`](https://github.com/ivotron/docker-bench).

<!--
## Adding new benchmarks

Torpor relies on docker, so adding a new benchmark means creating a 
docker image that executes one or more benchmarks and prints to 
`stdout` results in the JSON format shown above. Once an image that 
follows this convention is defined, one can copy the `microbench.yml` 
file and modify it accordingly. In order to have `torpor` use this, 
use the `--file` flag of the `base` command.
-->

# Dependencies

  * Docker 1.7+
  * Linux 3.19+
