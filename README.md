# Simulated Annealing

A simulated annealing process to determine the optimal weight values of an artificial neuron.
The neuron serves as a boolean function for a logic gate. In this example, a NAND-gate is replicated.

That is, the weights of the linear equation $(W_x, W_y, W_b)$ for the 
function $f(n) = W_x*x + W_y*y + W_b$ should give results the same as a 
NAND-gate for the input domains $X : \{0, 1\}$ and $Y : \{0, 1\}$.

See the algorithm in [src/simulated_annealing/api/anneal.py](src/simulated_annealing/api/anneal.py).

Python 3.8+ is required.

```
function SIMULATED-ANNEALING(problem, schedule) returns a solution State
  current <- problem.INITIAL
  for t = 1 to inf do
      T <- schedule(t)
      if T = 0 then return current
      next <- a randomly selected successor of current
      delta_E <- VALUE(current) - VALUE(next)
      if delta_E > 0 then current <- next
      else current <- next only with probability e^(-delta_E/T)
```

## Temperature Analysis

Simulated annealing simulates the process of annealing glass or metal.
It involves heating it up until malleable, then shaping it until it cools and hardens.

The temperature parameter, as defined by the `schedule` lambda function in this implementation, 
can have a large impact on the output of this algorithm.

$$\lambda x : x / 1.2$$

---

### Recommended Schedule

$$\lambda x : x / 1.2$$

This was the temperature schedule given as a starting point.
It performed well enough, producing incorrect weights about half of the time.

See the $x : x / 1.2$

![output graph](docs/_static/Figure_3_Temp=1.2_Path=23.png)

![Temp over time](docs/_static/Figure_6_Temp=1.2_Temp-over-Time.png)

![Delta_E over time](docs/_static/Figure_9_Temp=1.22_Delta-E-over-Time.png)

![Obj Fn over time](docs/_static/Figure_10_Temp=1.22_Obj-Fn-over-Time.png)

---

### Rapidly Cooling Schedule

$$\lambda x : x / 5$$

This was a temperature schedule to see what an extreme change would do to the algorithm.
It performed very poorly, failing to produce correct results in most runs.

Cooling off more quickly can speed up the search, but at the cost of accuracy.

See the $x : x / 5$

![output graph](docs/_static/Figure_4_Temp=5_Path=5.png)

![Temp over time](docs/_static/Figure_7_Temp=5_Temp-over-Time.png)

![Delta_E over time](docs/_static/Figure_12_Temp=5_Delta-E-over-Time.png)

![Obj Fn over time](docs/_static/Figure_11_Temp=5_Obj-Fn-over-Time.png)

---

### Simmering Schedule

$$\lambda x : x / 1.02$$

Now shifting to the opposite end of the spectrum by slowing down the rate of cooling.
It performed the worse, failing to ever produce correct results 
likely due to the extremely small $\Delta X$ causing near random-walk behavior.

Cooling off too quickly can slow down the search significantly 
and lead to unpredictable behavior.

See the $x : x / 1.02$

![output graph](docs/_static/Figure_5_Temp=1.02_Path=1031.png)

![Temp over time](docs/_static/Figure_8_Temp=1.02_Temp-over-Time.png)

---


Check out the [detailed temperature analysis](docs/temperature_analysis.md) in the `docs/` directory.

---

## Development

### Create a Virtual Environment

Create the virtual environment, enter into it with `source`-ry.

```bash
$ python -m venv .venv
$ source .venv/Scripts/activate
$ echo "Confirm you're using the correct python with: "
$ which python
=> Should show path to your .venv
```

And install the package locally:

```bash
(.venv)
$ pip install -e ".[dev]"
```

#### Install with Optional Dependencies

In the above, `".[dev]"` means you want to install the optional packages under 'dev' in [the project pyproject.toml](pyproject.toml).  
There is also a visual mode driven by networkx and matplotlib. To install dev tools plus visual mode, use this pip install command instead:

```bash
(.venv)
$ pip install -e ".[dev,visual]"
```

See this reference for more info about [installing Python packages](https://packaging.python.org/en/latest/tutorials/installing-packages/).

### Running

Now you can run the application from within the virtual environment:

```bash
(.venv)
$ simulated_annealing
```

#### Test Suite

Run tests:

```bash
(.venv)
$ python -m pytest -v tests/
```


### Documentation

See the [docs](docs/) directory and see some [example output](docs/example_output.md).

---

## Execution

Once installed, the application can be run via the command line. Inside your virtual environment:

```bash
(.venv)
$ simulated_annealing --help
usage: simulated_annealing [-h] [-c CONFIG] [-v] [-w WARN] {anneal} ...

options:
  -h, --help            show this help message and exit
  -c CONFIG, --config CONFIG
                        config file [etc/config.toml]
  -v, --version         print version and exit
  -w WARN, --warn WARN  logger warning level [WARN]

subcommands:
  {anneal}
(.venv-ai)
```

---

### Configuration

The application uses `TOML` files for configuration. Configuration supports
runtime parameter substitution via a shell-like variable syntax, *i.e.*
`var = ${VALUE}`. CLI invocation will use the current environment for
parameter substitution, which makes it simple to pass host-specific values
to the application without needing to change the config file for every
installation. Config file is located in [/etc](etc/).

```toml
    logging = "INFO"
```

### Logging

The application uses standard `Python logging`. All logging is to `STDERR`,
and the logging level can be set via the config file or on the command line.
Find the code in the [core module](src/simulated_annealing/core/).


* [TOML](https://toml.io)
* [Python logging](https://docs.python.org/3/library/logging.html)

---
