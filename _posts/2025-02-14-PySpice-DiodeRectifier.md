---
title: PySpice diode rectifier simulation
date: 2025-02-14 00:00:00 -500
categories: [electronics]
tags: [electronics, programming]
---

## Interactive Simulation of a Diode Rectifier Circuit

This Python script uses **PySpice** to simulate a **diode bridge rectifier** with user-adjustable parameters for load resistance $R_1$, capacitance $C_1$, and input frequency $F_{sw}$.

### 🔹 Key Features
- ✅ **Full-Wave Rectifier Simulation** using four 1N4007 diodes.
- ✅ **Interactive Sliders** to adjust \(R_1\), \(C_1\), and \(F_{sw}\) dynamically.
- ✅ **Real-Time Voltage Plots** displaying both input and output waveforms.

### 🛠 How It Works
- The AC input is a sinusoidal voltage source.
- The rectifier converts the AC voltage to DC.
- A capacitor smooths the output voltage.
- The interactive UI allows real-time tuning of circuit parameters.

Adjust the values and observe how they impact the rectified output! 📈🔍


### The code to run a diode rectifier in Jupyter


```python
def g(R1_sb, C1_sb, F_sb):
    ## Circuit Netlist
    circuit = Circuit('Diode rectifier')
    circuit.include("1N4007.mod")

    # Define transient simulation step time and stop time
    steptime = 0.0001
    finaltime = 0.5

    circuit.SinusoidalVoltageSource('Vsin', 'in1', 'in2', offset=0, amplitude=315, frequency=F_sb)

    circuit.D('D1', 'in1',   'out_p', model='DI_1N4007')
    circuit.D('D2', 'in2',   'out_p', model='DI_1N4007')
    circuit.D('D3', 'out_n', 'in1',   model='DI_1N4007')
    circuit.D('D4', 'out_n', 'in2',   model='DI_1N4007')

    circuit.R('R1', 'out_p', 'out_n', R1_sb@u_kΩ)
    circuit.R('R2', 'out_n', circuit.gnd, 0@u_kΩ)

    circuit.C('C1', 'out_p', 'out_n', C1_sb@u_uF)

    ## Simulation: Transient Analysis
    simulator = circuit.simulator(temperature=25, nominal_temperature=25)
    analysis = simulator.transient(step_time=steptime, end_time=finaltime)

    # PLOTTING COMMANDS
    figure, (axe1, axe2) = plt.subplots(2, 1, figsize=(10, 6))

    axe1.set_title('Вхідна напруга')
    axe1.set_xlabel('Час [s]')
    axe1.set_ylabel('Вхідна напруга [V]')
    axe1.grid()
    plot(analysis['in1']-analysis['in2'], axis=axe1)
    #axe1.set_ylim(-9, 9)  # Adjust limit for better visualization
    axe1.legend(('Вхід діодного випрямляча', 'Вхід'), loc=(.05, .1))
    cursor = Cursor(axe1, useblit=True, color='red', linewidth=1)

    axe2.set_title('')
    axe2.set_xlabel('Час [s]')
    axe2.set_ylabel('Вихідна напруга [V]')
    axe2.grid()
    plot(analysis['out_p']-analysis['out_n'], axis=axe2)
    #axe2.set_ylim(0, 9)  # Adjust limit for better visualization
    axe2.legend(('Вихід діодного випрямляча', 'Вихід'), loc=(.05, .1))
    cursor = Cursor(axe2, useblit=True, color='red', linewidth=1)

w = interactive(g,
                {'manual': True},
                R1_sb=widgets.IntSlider(min=1, max=50, step=1, value=20, description ='$R_{1}, [kOhm]$'),
                C1_sb=widgets.IntSlider(min=1, max=1000, step=1, value=45, description ='$C_{1}, [nF]$'),
                F_sb=widgets.IntSlider(min=1, max=20, step=1, value=5, description ='$F_{sw}, [kHz]$')
               )
display(w)
```

### Resulting interactive window

![alt text](_images/2025-02-14-PySpice_DiodeRectifier.png)
