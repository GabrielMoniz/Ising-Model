Exercise 8.13 from "Entropy, Order Parameters, and Complexity (2a. edição), Oxford University Press (2021), James P. Sethna"
# Exercise: Hysteresis and avalanches.

A piece of magnetic material exposed to an increasing external field \( H(t) \) will magnetize in a series of sharp jumps, or **avalanches**. These avalanches arise as magnetic domain walls in the material are pushed by the external field through a rugged potential energy landscape due to irregularities and impurities in the magnet. The magnetic signal resulting from these random avalanches is called **Barkhausen noise**.

In this exercise, we model this system with a nonequilibrium lattice model: the **Random Field Ising Model**.

## Hamiltonian

The Hamiltonian (or energy function) for our system is given by:

$$
\mathcal{H} = -J \sum_{\langle i,j \rangle} S_i S_j - \sum_i \left( H(t) + h_i \right) S_i
$$

where:
- $\mathcal{H}$ is the Hamiltonian,
- **J** is the interaction strength between spins,
- **S_i** represents the spin at site **i**,
- **⟨i,j⟩** denotes summation over nearest neighbors,
- **H(t)** is the external magnetic field,
- **h_i** is a random field at each site **i**.

## Spin Flip Rule

Each spin $S_i = \pm 1$ lies on a square or cubic lattice with periodic boundary conditions. The coupling **J** and the external field **H** follow the traditional Ising model. The disorder in the magnet is incorporated using the random field **h_i**, independently chosen at each lattice site from a Gaussian probability distribution with standard deviation **R**:

$$
P(h) = \frac{1}{\sqrt{2 \pi R}} e^{-h^2 / 2R^2}
$$

We are not interested in thermal equilibrium (where there would be no hysteresis). Instead, we set the temperature to zero. We start with all spins pointing down and adiabatically (infinitely slowly) increase $H(t)$ from $-\infty$ to $+\infty$.

## Rules for Evolving Spin Configuration

Each spin flips when doing so would decrease the energy. This occurs at site **i** when the local field at that site,

$$
J \sum_{j \, \text{nbr to} \, i} S_j + h_i + H(t)
$$

changes from negative to positive.

A spin can be flipped in two ways:

1. It can be triggered when one of its neighbors flips (participating in a propagating avalanche).
2. It can be triggered by the slow increase of the external field, starting a new avalanche.

## Part (a): Setting up the Simulation

### Initialize the Lattices:

- Set up the spin lattice `s[m][n]` and the random field lattice `h[m][n]`.
- In three dimensions, add an extra index to the arrays.
- Fill `s[m][n]` with down-spins \( S = -1 \) and `h[m][n]` with random fields from the Gaussian distribution $P(h)$.

### Write Helper Routines:

- `FlipSpin(s, i, j)`: Given indices $i$ and $j$ and your spin lattice $s$, flip the spin from \( S = -1 \) to \( S = +1 \). Return an error if the spin is already flipped.
- `NeighborsUp(i, j)`: Calculate the number of up-neighbors for the spin, implementing periodic boundary conditions.

### Adiabatically Increase \( H \):

To start a new avalanche, search for the next unflipped spin ready to flip, increase \( H \) just enough to flip it, and propagate the avalanche as follows:

1. **Find the Triggering Spin**: Find the unflipped site with the largest local field due to neighbors and random field, $J \sum_{j \, \text{nbr to} \, i} S_j + h_i$.
2. **Increment $H(t)$** to reach this internal field threshold.

### Queue Processing:

- Place the triggering spin in a first-in–first-out queue.
- While the queue is not empty:
  - Pop a spin from the queue.
  - Flip it if it hasn’t already flipped.
  - Push all unflipped neighbors with positive local fields onto the queue.

## Part (b): Implementing the Avalanche

- `BruteForceNextAvalanche`: Check all unflipped spins and return the position of the next to flip based on its local field.
- `PropagateAvalanche`: Given the triggering spin, implement steps to propagate the avalanche. For visualization, color the spins that flip.

### Run Simulations:

- Simulate a $300 \times 300$ system at different $R$ values: \( R = 1.4 \), \( R = 0.9 \), and \( R = 0.7 \).
- If running in three dimensions, use a $50^3$ lattice with \( R = 4 \), \( R = 2.16 \), and \( R = 2 \).
- Display the resulting avalanche patterns.

## Measurements and Analysis

There are various properties you might want to measure about this system:
- Avalanche sizes and correlation functions.
- Hysteresis loop shapes.
- Average pulse shapes during avalanches.

There are lots of properties that one might wish to measure about this system: avalanche sizes, avalanche correlation functions, hysteresis loop shapes, and average pulse shapes during avalanches. It can become complex to put all these measurements inside the inner loop of your code. Instead, we suggest that you try the **subject–observer design pattern**: each time a spin is flipped, and each time an avalanche is finished, the subject (our simulation) notifies the list of observers.




### Part (d): Implement Observers for Simulation Data Collection

- **MagnetizationObserver**: This observer stores an internal magnetization starting at \(-N\), adding two to it whenever it is notified.
- **AvalancheSizeObserver**: This observer tracks the growing size of the current avalanche after each spin flip, adding the final size to a histogram of all previous avalanche sizes when the avalanche ends.
  
Set up `NotifySpinFlip` and `NotifyAvalancheEnd` routines for your simulation, and add the two observers appropriately.

Finally, plot the magnetization curve \( M(H) \) and the avalanche size distribution histogram \( D(S) \) for the three systems you ran in part (c).
