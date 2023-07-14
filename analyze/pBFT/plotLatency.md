---
jupytext:
  formats: ipynb,md:myst
  text_representation:
    extension: .md
    format_name: myst
    format_version: 0.13
    jupytext_version: 1.14.7
kernelspec:
  argv: [python, -m, ipykernel_launcher, -f, '{connection_file}']
  display_name: Python 3 (ipykernel)
  env: null
  interrupt_mode: signal
  language: python
  metadata:
    debugger: true
  name: python3
---

```{code-cell} ipython3
#!make run
%matplotlib inline
import matplotlib.pyplot as plt
import scienceplots  # scientific themes for matplotlib
import numpy as np
import json

# number of peers to crash
n = 33
dataDir = "../../quantas/results/pBFT/d10/r400/p100/"

correct  = json.load(open(f"{dataDir}i00t.json"))
infected = json.load(open(f"{dataDir}i{n:02}t.json"))

correctLatency  = np.array(list(map(lambda x: x['throughput'],  correct['tests'])), dtype=np.float64)
infectedLatency = np.array(list(map(lambda x: x['throughput'], infected['tests'])), dtype=np.float64)

# averages and standard deviations, ignoring None
correctLatencyAvg  = np.nanmean( correctLatency, axis=0)
correctLatencyStd  = np.nanstd(  correctLatency, axis=0)
infectedLatencyAvg = np.nanmean(infectedLatency, axis=0)
infectedLatencyStd = np.nanstd( infectedLatency, axis=0)

# create an array for the x axis. A range from 0 to ...
x = np.arange(0, len(correctLatency[0]))

with plt.style.context('science'):
    fig, ax = plt.subplots()

    unin =  plt.plot(x, correctLatency.T, ':', color='blue', alpha=.035,
                     label='Uninfected')
    uninAvg = plt.plot(x, correctLatencyAvg.T, color='blue', linewidth=3,
                       label='Uninfected average')
    # shade the 95% confidence interval
    plt.fill_between(x,
                     correctLatencyAvg.T-1.96*correctLatencyStd.T,
                     correctLatencyAvg.T+2*correctLatencyStd.T,
                     linestyle='',   color='blue', linewidth=3, alpha=.2,
                     label='Uninfected 95% error')

    crsh =  plt.plot(x, infectedLatency.T, ':', color='red', alpha=.035,
                     label='Crashing')
    crshAvg = plt.plot(x, infectedLatencyAvg.T, color='red', linewidth=3,
                       label='Crashing average')
    # shade the 95% confidence interval
    plt.fill_between(x,
                     infectedLatencyAvg.T-1.96*infectedLatencyStd.T,
                     infectedLatencyAvg.T+2*infectedLatencyStd.T,
                     linestyle='',   color='red', linewidth=3, alpha=.2,
                     label='Crashing 95% error')

    plt.legend(handles=[uninAvg[0], crshAvg[0]], mode="expand")

    plt.xlabel('Round')
    plt.ylabel('Cumulative throughput (commits)')
    plt.title(f'pBFT latency with {n}/100 peers crashing at round 200')
    # add gridlines every 1
    plt.grid(True, axis='y')
    # set the gap between the left axis and the data to zero
    ax.set_xmargin(0)
    # make plot bigger
    plt.gcf().set_size_inches(10, 5)

    plt.savefig(f"{dataDir}i{n:02}t.svg")
    plt.savefig(f"{dataDir}i{n:02}t.png", dpi=300)
    plt.show()
```
