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
n = 32  # number of peers to crash
dataDir = "../../quantas/results/pBFT/d10/r400/p100/"

nTests = len(json.load(open(f"{dataDir}i{n:02}e{e:02}.json"))['tests'])

equivocate = np.empty([21, nTests])
equivocateAvg = np.empty([21])
equivocateStd = np.empty([21])
equivocateX = np.empty([21])  # x axis

for e in range(0, 21):
    tests = json.load(open(f"{dataDir}i{n:02}e{5*e:02}.json"))['tests']

    equivocate[e] = np.array(list(map(
        lambda t: tests[t]['throughput'][-1]-tests[t]['throughput'][200],
                range(0, len(tests)))), dtype=np.float64)

    equivocateX[e] = 100-e*5

equivocateAvg = np.average(equivocate, axis=1)
equivocateStd = np.std(equivocate, axis=1)

with plt.style.context('science'):
    fig, ax = plt.subplots()

    equiv =  plt.plot(equivocateX, equivocateAvg, color='black', 
                     label='Equivocating')
    # shade the 95% confidence interval
    plt.fill_between(equivocateX,
                     equivocateAvg-1.96*equivocateStd,
                     equivocateAvg+1.96*equivocateStd,
                     linestyle='',   color='red', alpha=.2,
                     label='Equivocating 95% error')


    plt.xlabel('Degree of equivocation (\%)')
    plt.ylabel('Throughput (commits/200 rounds)')
    plt.title(f'pBFT throughput with {n}/100 peers equivocating')
    # add gridlines every 1
    plt.grid(True, axis='y')
    # set the gap between the left axis and the data to zero
    ax.set_xmargin(0)
    # make plot bigger
    plt.gcf().set_size_inches(10, 5)

    plt.savefig(f"{dataDir}i{n:02}equivocating.svg")
    plt.savefig(f"{dataDir}i{n:02}equivocating.png", dpi=300)
    plt.show()
```
