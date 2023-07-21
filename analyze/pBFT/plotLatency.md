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
dataDir = "../../quantas/results/pBFT/d1/r1000/p100/"

nTests = len(json.load(open(f"{dataDir}i{n:02}e00.json"))['tests'])

equivocateX = np.empty([21])  # x axis

equivocateD1 = np.empty([21, nTests])
equivocateD1Avg = np.empty([21])
equivocateD1Std = np.empty([21])

for e in range(0, 21):
    tests = json.load(open(f"{dataDir}i{n:02}e{5*e:02}.json"))['tests']

    equivocateD1[e] = np.array(list(map(
        lambda t: 1000/tests[t]['throughput'][-1],
                range(0, len(tests)))), dtype=np.float64)

    equivocateX[e] = 100-e*5

equivocateD1Avg = np.average(equivocateD1, axis=1)
equivocateD1Std = np.std(equivocateD1, axis=1)


equivocateD5 = np.empty([21, nTests])
equivocateD5Avg = np.empty([21])
equivocateD5Std = np.empty([21])

dataDir = "../../quantas/results/pBFT/d5/r1000/p100/"
for e in range(0, 21):
    tests = json.load(open(f"{dataDir}i{n:02}e{5*e:02}.json"))['tests']

    equivocateD5[e] = np.array(list(map(
        lambda t: 1000/tests[t]['throughput'][-1],
                range(0, len(tests)))), dtype=np.float64)

equivocateD5Avg = np.average(equivocateD5, axis=1)
equivocateD5Std = np.std(equivocateD5, axis=1)


equivocateD10 = np.empty([21, nTests])
equivocateD10Avg = np.empty([21])
equivocateD10Std = np.empty([21])

dataDir = "../../quantas/results/pBFT/d10/r1000/p100/"
for e in range(0, 21):
    tests = json.load(open(f"{dataDir}i{n:02}e{5*e:02}.json"))['tests']

    equivocateD10[e] = np.array(list(map(
        lambda t: 1000/tests[t]['throughput'][-1],
                range(0, len(tests)))), dtype=np.float64)

equivocateD10Avg = np.average(equivocateD10, axis=1)
equivocateD10Std = np.std(equivocateD10, axis=1)

with plt.style.context('science'):
    fig, ax = plt.subplots()

    plt.plot(equivocateX, equivocateD1Avg, color='black', label='delay 1')
    # shade the 95% confidence interval
    plt.fill_between(equivocateX,
                     equivocateD1Avg-1.96*equivocateD1Std,
                     equivocateD1Avg+1.96*equivocateD1Std,
                     linestyle='', color='black', alpha=.2)

    plt.plot(equivocateX, equivocateD5Avg, color='blue', label='delay 5')
    # shade the 95% confidence interval
    plt.fill_between(equivocateX,
                     equivocateD5Avg-1.96*equivocateD5Std,
                     equivocateD5Avg+1.96*equivocateD5Std,
                     linestyle='', color='blue', alpha=.2)

    plt.plot(equivocateX, equivocateD10Avg, color='red', label='delay 10')
    # shade the 95% confidence interval
    plt.fill_between(equivocateX,
                     equivocateD10Avg-1.96*equivocateD10Std,
                     equivocateD10Avg+1.96*equivocateD10Std,
                     linestyle='', color='red', alpha=.2)

    plt.legend(loc='upper right')

    plt.xlabel('Degree of equivocation (\%)')
    plt.ylabel('Latency (rounds/commit)')
    plt.title(f'PBFT latency with {n}/100 peers equivocating')
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
