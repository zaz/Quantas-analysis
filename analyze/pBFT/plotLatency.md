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
e = 55  # degree to which infected nodes equivocate
dataDir = "../../quantas/results/pBFT/d5/r1000/p100/"

nTests = len(json.load(open(f"{dataDir}i00e55.json"))['tests'])

equivocateX = np.empty([21])  # x axis

equivocate = np.empty([21, nTests])
equivocateAvg = np.empty([21])
equivocateStd = np.empty([21])

for n in range(0, 21):
    tests = json.load(open(f"{dataDir}i{5*n:02}e{e:02}.json"))['tests']

    equivocate[n] = np.array(list(map(
        # lambda t: 1000/max(tests[t]['throughput'][-1], 100),
        lambda t: tests[t]['throughput'][-1],
                range(0, len(tests)))), dtype=np.float64)

    equivocateX[n] = n*5

equivocateAvg = np.average(equivocate, axis=1)
equivocateStd = np.std(equivocate, axis=1)

with plt.style.context('science'):
    fig, ax = plt.subplots()

    plt.plot(equivocateX, equivocateAvg, color='blue', label='delay 5')
    # shade the 95% confidence interval
    plt.fill_between(equivocateX,
                     equivocateAvg-1.96*equivocateStd,
                     equivocateAvg+1.96*equivocateStd,
                     linestyle='', color='blue', alpha=.2)

    plt.legend(loc='upper right')

    plt.xlabel('Number of peers equivocating')
    plt.ylabel('Throughput (commits/1000 rounds)')
    plt.title(f'PBFT latency with peers equivocating with degree 55\%')
    # add gridlines every 1
    plt.grid(True, axis='y')
    # set the gap between the left axis and the data to zero
    ax.set_xmargin(0)
    # make plot bigger
    plt.gcf().set_size_inches(10, 5)

    plt.savefig(f"{dataDir}i{n:02}e55.svg")
    plt.savefig(f"{dataDir}i{n:02}e55.png", dpi=300)
    plt.show()
```
