---
title: PySpice usage in Jupyter
date: 2025-02-08 00:00:00 -500
categories: [electronics]
tags: [electronics, programming]
---

### Simple buffer

```python
    def g(F_sb):
    ## Circuit Netlist
    circuit = Circuit('Op-amp circuits - Example 1 Non-inverting op-amp Amplifier')
    circuit.include("uA741.lib")

    # Define amplitude and frequency of input sinusoid
    amp = 0.2@u_V
    # Define transient simulation step time and stop time
    steptime = 0.1@u_us
    finaltime = 0.004
```

Input signal definition

```python
    source = circuit.SinusoidalVoltageSource('in', 'v+', circuit.gnd, amplitude=0.5@u_V, frequency=F_sb@u_kHz)
```

Circuit elements definition:

```python
    # Power supply for the op-amp
    circuit.V(2, '+Vcc', circuit.gnd, 15@u_V)
    circuit.V(3, '-Vcc', circuit.gnd, -15@u_V)
    # Op-amp
    circuit.X(1, 'uA741', 'v+', 'v-', '+Vcc', '-Vcc', 'out')

    circuit.R(1, 'v-', 'out',        0@u_kΩ)
    circuit.R('L', 'out', circuit.gnd, 1000@u_kΩ)
```

Setting simulator object:

```python
    ## Simulation: Transient Analysis
    simulator = circuit.simulator(temperature=25, nominal_temperature=25)
```

Perform transient type of analysis

```python
    analysis = simulator.transient(step_time=steptime, end_time=finaltime)
```

Plot signals

```python

    # PLOTTING COMMANDS
    figure, (axe1, axe2) = plt.subplots(2, 1, figsize=(10, 7))

    axe1.set_title('')
    axe1.set_xlabel('Час [s]')
    axe1.set_ylabel('Вхідна напруга [V]')
    axe1.grid()
    plot(analysis['v+'], axis=axe1)
    axe1.legend(('Вхід', 'Вхід'), loc=(.05, .1))
    cursor = Cursor(axe1, useblit=True, color='red', linewidth=1)

    axe2.set_title('')
    axe2.set_xlabel('Час [s]')
    axe2.set_ylabel('Вихідна напруга [V]')
    axe2.grid()
    plot(analysis['out'], axis=axe2)
    axe2.legend(('Вихід', 'Вихід'), loc=(.05, .1))
    cursor = Cursor(axe2, useblit=True, color='red', linewidth=1)

w = interactive(g,
                {'manual': True},
                F_sb=widgets.IntSlider(min=1, max=15, step=1, value=15, description ='$F_{вх}, [кГц]$')
               )
display(w)
```
