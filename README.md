# "Deep Learning Applications on Gravitational Waves"

**MSc "Digital Media & Computational Intelligence", Department of Informatics, Aristotle University of Thessaloniki, September 2021**

Supervisor: Anastasios Tefas, *In collaboration with Virgo group at AUTh*


This is the code of the experiments during my master Thesis. 
This is the final step during the process of the creation of a surrogate model, built upon waveforms from [SEOBNRv4](https://journals.aps.org/prd/abstract/10.1103/PhysRevD.95.044028/) model. During the series of experiments that were tried out, this is the idea where we divided the range of one of the input parameters in order to obtain better results. Two seperate trainings took place, one for the amplitude and one for the phase components of the complex waveform. The final errors measurement takes place, in the script where we combine our reconstructed waveforms from the models' predictions and obtain the final mismatch.

**1) Creating a Surrogate Model**

- A training of $N$ waveforms was created, using [SEOBNRv4](https://journals.aps.org/prd/abstract/10.1103/PhysRevD.95.044028/) with [PyCBC](https://zenodo.org/record/4849433/),  $\{ h_i(t; \lambda_i) \}_{i=1}^{N}$ where $\lambda = (q, x_1,x_2)$, $q=\frac{m1}{m2}$ is the mass   ratio $1\leq q \leq 8$,  $-1\leq x_1 \leq 1$ and  $-1\leq x_2 \leq 1$ are the spins.
- Greedy algorithm selects $m < N$ waveforms (and their $\lambda$ values), which create the reduced basis $\{e_i\}_{i=1}^{m}$ for a given tolerance.
- the Empirical Interpolation (EIM) algorithm finds informative time points (empirical nodes $\{ T_i \}_{i=1}^{m}$) that can be used to reconstruct the whole waveform for arbitrary $\lambda$.

**2) Implementing Neural Networks**

- Following [Khan & Green](https://journals.aps.org/prd/abstract/10.1103/PhysRevD.103.064015/), we use neural networks to map the input $\lambda$ to the coefficients from the empirical nodes $T_j$.  
- After testing to work with complex form of waveforms, two separate networks are used; one for the amplitude and one for the phase of the waveforms, for better results.

**3) Improvement through Residual Errors Network**

The first improvement (which also turned out to be the most significant) was obtained by adding a second network (after the training) which can make predictions for the errors of the first network.  We remind that for the baseline model, the first network  had three-dimensional input $\{\boldsymbol{\lambda_i}\}_{i=1}^{N}$  and produced predictions $\hat{\boldsymbol{y}}(\boldsymbol{\lambda})$ (for an arbitrary $\boldsymbol{\lambda}$) with 18 dimensions for the amplitude and 8 for the phase network. For all ${\boldsymbol{\lambda}_i^N}$ in the training set, one can obtain the corresponding predictions  $\{\hat{\boldsymbol{y}}(\boldsymbol{\lambda}_i)\}$ and calculate the *residual* 

$$\boldsymbol{e}_i \equiv \boldsymbol{y}(\boldsymbol{\lambda}_i) - \hat {\boldsymbol{y}}(\boldsymbol{\lambda}_i)$$ where $\boldsymbol{y}$ is the ground truth.

**4) Exploration of Feature Space and Output Manipulation**

**4.1) Feature Space Manipulation:**

- a) *Exploitation of similarities between waveforms*. One such idea was to enlarge the input parameter space to four-dimensional, by adding a new parameter that describes physical relations between different waveforms

- b) *Augmentation of the training set*. Next, we tried to improve the problem of the presence of the worst mismatches at boundary values of the
mass ratio and spins. As a remedy for the large mismatch when $q = 1$ was approached, we first tried to augment the dataset with additional input samples $1/q$, $χ2$, $χ1$.

- c) *Dissection of the input space*. Another tactic that was followed was that of dissecting the input feature space into a number of K groups and evaluate the performance of the networks in each group. To that end, the input was divided to $K = 2$ groups according to the value of the mass ratio $q$ and two separate networks were trained for both cases of amplitude and phase, each followed by its corresponding residual errors network.

**4.2) Output Manipulation**

- a) *Dedicated network per output coefficient*. In an effort to achieve smaller mismatches by manipulating the output, we used a dedicated training net- work for each coefficient. For the baseline case examined in this work, we therefore used 18 networks for the amplitude and 8 networks for the phase.

- b) *Output augmentation*. Finally, another idea to push the network to learn the desired output, was to insert a new branch with a function $f(y)$. For that reason, the quantity $(1 − y)$ was added as an extra output for the training network.

<p align = "center">
<img src="/figures/couples1.png"  width="450" height="350">
</p>

<p align = "center">
<img src="/figures/couples2.png"  width="450" height="350">
</p>

<p align = "justify">
Violin plots comparing the mismatches for different variants of the ANN surrogate model without (left panel for each variant) and with the addition of a network that models the residual error (right panel for each variant).The middle horizontal line marks the median and the extent of the lines show the minimum and maximum values. In each panel, the envelope is proportional to the density of points. A significant reduction of the mismatch is achieved in several variants.
</p>

# Running this Project


# Citation

Our work has been submitted in the arXiv preprint database [Deep Residual Error and Bag-of-Tricks Learning for Gravitational Wave Surrogate Modeling.](https://arxiv.org/abs/2203.08434/)
