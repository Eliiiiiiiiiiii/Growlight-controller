# 1.1 Design criteria


| Criteria     | Value      |
| ------------ | ---------- |
| $Vin_{max}$  | $24V$      |
| $Vout$       | $8V-12V$   |
| $Iout_{max}$ | $6.66A$    |
| $ΔVout$      | $< 12.5mV$ |
| $Fsw$        | $1MHz$     |

| Results    | Value           |
| ---------- | --------------- |
| $Vout$     | $7.2V-12.7V$    |
| $R1$       | $121kΩ$         |
| $RV$       | $5kΩ$           |
| $R2$       | $6kΩ$           |
| $Inductor$ | YSPIT0530A-2R2M |
| $L$        | $2.2µH$         |
| $Cin$      | $20µF$          |
| $Cout$     | $52µF$          |
| $MODE1$    | $499kΩ$ to AGND |
| $MODE2$    | $499kΩ$ to VDD  |

# 2.1 Calculations

## 2.2 Feedback divider

To set the desired output voltage of 8V-12V, use the formula below, and tweak R1 and R2+RV values until, an acceptable balance between how accurate Vout is and using standard resistor values.
R2 should not exceed 10kΩ to keep output from drifting in low load scenarios.

$R1 = \dfrac{R2\cdot(Vout-Vfb)}{Vfb}→\dfrac{6kΩ\cdot(12.7V-0.6V)}{0.6V}=121kΩ$
$\dfrac{11kΩ\cdot(7.2V-0.6V)}{0.6V}=121kΩ$

As these resistors are already readily available values, there is no need to adjust the values.

## 2.3 Input capacitance

Theres a long complicated formula for this, so i'm just gonna use two 10µF caps and hope thats enough.


## 2.4 Output capacitance

To determine the minimum suitable output capacitance at any desired output ripple voltage, fill in the formula below and calculate.
Once that is done, you can choose a suitable capacitance from already used values on your board, and then calculate the actual voltage ripple including ESR induced voltage ripple with the formula below the previously discussed formula.

(Due to previous "test" calculations beforehand, $ΔI_{L}$ is known to be roughly 3A@12.7V and 2.55A@7.2V. This is the calculations used to arrive at these numbers: $\small ΔI_{L}=\dfrac{\left(Vin_{max}-Vout\right)\cdot D}{Fsw\cdot L}\to \dfrac{\left(24V-12.7V\right)\cdot \left( \dfrac{12.7V}{24V\cdot 90\%} \right)}{1MHz\cdot 2.2µH}=3.03A$)




$Cout_{min}=\dfrac{ΔI_{L}}{8\cdot Fsw\cdot ΔVout}\to \dfrac{3A}{8\cdot 1MHz\cdot 12.5mV}=30µF$

$52µF$ is chosen as a suitable output capacitor to leave headroom for voltage derating. Specifically three 10µF 50V X5R or better capacitors and one aluminium polymer 22µF capacitor rated for 25V.

$ΔVout=ESR\cdot ΔI_{L}+\dfrac{ΔI_{L}}{8\cdot Fsw\cdot Cout}\to$ 

$1mΩ\cdot 3A+\dfrac{3A}{8\cdot 1MHz\cdot 52µF}=10.2mV$


## 2.5 Output inductor

To find the minimum suitable inductance, the formula below can be used, and as the inductance is not known prior to this, inductor ripple current cannot be calculated either. Therefore this is to be estimated with $ΔI_{L}=(0.2\: to\: 0.5)\cdot Iout_{max}$.

$L = \dfrac{Vout×(Vin-Vout)}{Vin×ΔI_{L}×Fsw} → \dfrac{12.7V×(24V-12.7V)}{24V×(0.5\cdot 6.66A)×1MHz} = 1.796µH$

Due to parts availability it has been decided to use a $2.2µH$ inductor.

Next the maximum duty cycle: $D$ must be found using the formula below. $\eta$ is assumed to be around 90%.

$D=\dfrac{Vout}{Vin_{max}\cdot \eta}\to \dfrac{12.7V}{24V\cdot 90\%}=0.59$

Then this is used alongside the formula below.

$ΔI_{L}=\dfrac{(Vin_{max}-Vout)\cdot D}{Fsw×L}\to \dfrac{(24V-12.7V)\cdot 0.59}{1MHz×2.2µH}=3.03A$

Lastly the formula below is used to determine the maximum current seen by both the switching device and the inductor. Both should be rated accordingly.

$I_{LP} = Iout_{max}+\dfrac{ΔI_{L}}{2} → 6.66A+\dfrac{3.03A}{2} = 8.175A$

A suitable inductor is then found.

| Parameter           | Value           |
| ------------------- | --------------- |
| Part number         | YSPIT0530A-2R2M |
| Inductance          | $2.2µH$         |
| Saturation current  | $10A$           |
| Trise = 40℃ current | $9.7A$          |
| DCR                 | $14.5mΩ$        |
| PLoss               | $969mW@8.175A$  |


## 2.6 VDRV capacitor

Datasheet suggests connecting a 2.2µF capacitor between VDRV and GND.


## 2.7 Bootstrap capacitor

The datasheet recommends a 100nF capacitor and a 1Ω bootstrap resistor between PHASE and the bootstrap capacitor.


## 2.8 VDD capacitor

The datasheet suggests a 1µF decoupling capacitor between VDD and GND.

## 2.9 MODE1


![[Pasted image 20260101025946.png]]

For the desired operation of skip and 1MHz switching frequency, a 499kΩ resistor should be connected between MODE1 and AGND.


## 2.10 MODE2

![[Pasted image 20260101030120.png]]

For the desired operation of 6ms soft start time and minimizing BOM, a 499kΩ resistor should be connected between MODE2 and VDD.