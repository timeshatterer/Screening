
# Tracing SCF

## Lattice Parameter


how to Tracing SCF lattice Parameter.
let's say we've literature(lattice parameters) for our material to screening around it.
for example the material, FeS-194 material that we will use here.
if you dont have the literature or previous work you can visit materialproject.org to get it.
Screening calculations require quite a lot of input, so to make things easier, you can use the code below

```python:
def text(i):
    text_file = open("%.2f,%.2fFeS-scf.in" % (i, j), "w")
    text_file.write("""&CONTROL
    calculation='scf',
    etot_conv_thr = 1.0d-4,
    forc_conv_thr = 1.0d-3,
    nstep = 100,
    !max_seconds = 3500,
    prefix='FeS-194-AFM',
    pseudo_dir='./',
    outdir='./tmp',
    verbosity='high'
    !restart_mode='restart'
/

&SYSTEM    
    ibrav = 4,
    A = %.2f,
    C = %.2f,
    cosAB = -0.50,
    nat =  4,
    ntyp = 3,
    nspin= 2,
    ecutwfc = 80,
    ecutrho = 640,
    starting_magnetization(1) =  0.5,
    starting_magnetization(2) = -0.5,
    input_dft = 'vdw-df2-b86r',
    occupations = 'smearing',
    smearing = 'gauss',
    degauss = 0.01
/

&ELECTRONS
    startingwfc = 'atomic+random'
    !startingwfc = 'file',
    diagonalization = 'cg',
    mixing_beta = 0.7,
    conv_thr = 1.0d-9,
    electron_maxstep = 2000
/

&IONS
    ion_dynamics = 'bfgs'
/

&CELL
    cell_dynamics = 'bfgs'
/     

ATOMIC_SPECIES
    Fe1  55.845 Fe.pbe-spn-rrkjus_psl.1.0.0.UPF
    Fe2  55.845 Fe.pbe-spn-rrkjus_psl.1.0.0.UPF
    S    32.06  S.pbe-n-rrkjus_psl.1.0.0.UPF

ATOMIC_POSITIONS {crystall}
Fe1   0.00000   0.00000   0.00000 0 0 0
Fe2   0.00000   0.00000   0.50000
S     0.33333   0.66667   0.75000
S     0.66667   0.33333   0.25000


K_POINTS automatic
    12 12 6    1 1 1
""" % (i, j))

    text_file.close()


for i in range(330,351):
    i = i / 100
    for j in range(550,571):
        j=j/100
        #print(i,j)
        text(i)
        # few code on file.sh
        print("mpiexec.hydra -n $NUM_PROCS ./pw.x -ndiag 1 -inp  %.2f,%.2fFeS-scf.in > %.2f,%.2fFeS-scf.out" % (i, j, i, j))
```
that code will produce input file and command line for submit file.
441 files and command lines were generated from 3.3 Armstrong to 3.5 Amstrong and 5.5 Amstrong to 5.7 Amstrong with intervals of 0.01 Amstrong.

so we can run it easily with this code.

after get 441 output
put yout data into excel
and use this code to represent the countour of that 441 file output

```python:
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import matplotlib.ticker as ticker
df=pd.read_excel('datanya nih.xlsx', sheet_name='Lembar4')
a=df.values.tolist()
c=np.transpose(a)
xlist = np.linspace(3.3, 3.5, 21)
ylist = np.linspace(5.5, 5.7, 21)
X, Y = np.meshgrid(xlist, ylist)
fig,ax=plt.subplots(1,1)
cp = ax.contourf(X, Y, c,50,cmap='jet')
fig.colorbar(cp) # Add a colorbar to a plot
ax.set_title('Energy Contour Plot')
ax.set_xlabel('A (Å)')
ax.set_ylabel('C (Å)')
ax.xaxis.set_major_locator(ticker.MultipleLocator(0.02))
ax.yaxis.set_major_locator(ticker.MultipleLocator(0.02))
plt.plot(3.4, 5.68, "o", color="#FF1493")
plt.show()
```

that code will produce countour as below
![Alt text](SCFlatticeparameters.png?raw=true "The countour")

## Tracing Energy Cut Off

how to Tracing SCF Energy Cut Off.
You can read the upf file to find out the energy cut off wavefunction and rho that will be used but it will be more detailed if you do a screening.
so here you can do a screening of energy cut off wvfc and rho with a ratio of 1:8 because it has been seen from the upf file.
Screening calculations require quite a lot of input, so to make things easier, you can use the code below

```python:
def text(i):
    text_file = open("%.0f,%.0fFeS-scf.in" % (4*i, 32*i), "w")
    text_file.write("""&CONTROL
    calculation='scf',
    etot_conv_thr = 1.0d-4,
    forc_conv_thr = 1.0d-3,
    nstep = 100,
    !max_seconds = 3500,
    prefix='FeS-194-AFM',
    pseudo_dir='./',
    outdir='./tmp',
    verbosity='high'
    !restart_mode='restart'
/

&SYSTEM    
    ibrav = 4,
    A = 3.37509062,
    C = 5.63214115,
    cosAB = -0.50,
    nat =  4,
    ntyp = 3,
    nspin= 2,
    ecutwfc = %.0f,
    ecutrho = %.0f,
    starting_magnetization(1) =  0.5,
    starting_magnetization(2) = -0.5,
    input_dft = 'vdw-df2-b86r',
    occupations = 'smearing',
    smearing = 'gauss',
    degauss = 0.01
/

&ELECTRONS
    startingwfc = 'atomic+random',
    diagonalization = 'cg',
    mixing_beta = 0.7,
    conv_thr = 1.0d-9,
    electron_maxstep = 2000
/

&IONS
    ion_dynamics = 'bfgs'
/

&CELL
    cell_dynamics = 'bfgs'
/     

ATOMIC_SPECIES
    Fe1  55.845 Fe.pbe-spn-rrkjus_psl.1.0.0.UPF
    Fe2  55.845 Fe.pbe-spn-rrkjus_psl.1.0.0.UPF
    S    32.06  S.pbe-n-rrkjus_psl.1.0.0.UPF

ATOMIC_POSITIONS {crystall}
Fe1   0.00000   0.00000   0.00000 0 0 0
Fe2   0.00000   0.00000   0.50000
S     0.33333   0.66667   0.75000
S     0.66667   0.33333   0.25000

K_POINTS automatic
    12 12 6    1 1 1
""" % (4*i, 32*i))

    text_file.close()


for i in range(1, 41):
    text(i)
    # few code on file.sh
    print("mpiexec.hydra -n $NUM_PROCS ./pw.x -ndiag 1 -inp  %.0f,%.0fFeS-scf.in > %.0f,%.0fFeS-scf.out" % (4*i, 32*i, 4*i, 32*i))

```
that code will produce input file and command line for submit file.
40 files and command lines were generated energy cutt off input from 4 to 160 (wavefunction) and 32 to 1280(rho) with intervals 4(wavefunction) and 32(rho).
after that you can easily turn to the graph.

![Alt text](Chart1.png?raw=true "ecut graph") 

## Tracing Energy K-points

how to Tracing SCF K-points.
for K-points, this can determine the precision in the grid, but also must consider the computational cost and ease of expansion to supercell.
Screening calculations require quite a lot of input, so to make things easier, you can use the code below

```python:
def text(i):
    text_file = open("%.0f,%.0f,%.0fFeS-scf.in" % (2*i, 2*i, i), "w")
    text_file.write("""&CONTROL
    calculation='scf',
    etot_conv_thr = 1.0d-4,
    forc_conv_thr = 1.0d-3,
    nstep = 100,
    !max_seconds = 3500,
    prefix='FeS-194-AFM',
    pseudo_dir='./',
    outdir='./tmp',
    verbosity='high'
    !restart_mode='restart'
/

&SYSTEM    
    ibrav = 4,
    A = 3.37509062,
    C = 5.63214115,
    cosAB = -0.50,
    nat =  4,
    ntyp = 3,
    nspin= 2,
    ecutwfc = 80.0,
    ecutrho = 640.0
    starting_magnetization(1) =  0.5,
    starting_magnetization(2) = -0.5,
    input_dft = 'vdw-df2-b86r',
    occupations = 'smearing',
    smearing = 'gauss',
    degauss = 0.01
/

&ELECTRONS
    startingwfc = 'atomic+random'
    diagonalization = 'cg',
    mixing_beta = 0.7,
    conv_thr = 1.0d-9,
    electron_maxstep = 2000
/

&IONS
    ion_dynamics = 'bfgs'
/

&CELL
    cell_dynamics = 'bfgs'
/     

ATOMIC_SPECIES
    Fe1  55.845 Fe.pbe-spn-rrkjus_psl.1.0.0.UPF
    Fe2  55.845 Fe.pbe-spn-rrkjus_psl.1.0.0.UPF
    S    32.06  S.pbe-n-rrkjus_psl.1.0.0.UPF

ATOMIC_POSITIONS {crystall}
Fe1   0.00000   0.00000   0.00000 0 0 0
Fe2   0.00000   0.00000   0.50000
S     0.33333   0.66667   0.75000
S     0.66667   0.33333   0.25000

K_POINTS automatic
    %.0f %.0f %.0f    1 1 1
""" % (2*i, 2*i, i))

    text_file.close()


for i in range(1, 19):
    text(i)
    # few code on file.sh
    print("mpiexec.hydra -n $NUM_PROCS ./pw.x -ndiag 1 -inp  %.0f,%.0f,%.0fFeS-scf.in > %.0f,%.0f,%.0fFeS-scf.out" % (2*i, 2*i, i, 2*i, 2*i, i))

```
that code will produce input file and command line for submit file.
18 files and command lines were generated energy cutt off input from 1 to 18 (k in kpoints) and 2 to 36(i an j in kpoints with interval 2).
after that you can easily turn to the graph.

![Alt text](Chart2.png?raw=true "kpoints graph") 


### you can download the file from here:

input QE lattice parameter

https://gist.github.com/timeshatterer/98b4e99f5e48f7e8df90ed1a336eeed7

plot countour

https://gist.github.com/timeshatterer/9a0568120786f85d247a5e8224d0afce

input QE ecut

https://gist.github.com/timeshatterer/4b39aae7735fb3101278c59b4f8b0dfe

input QE kpoints

https://gist.github.com/timeshatterer/c332c85f8430508b4858c5e784e67aba
