---
layout: default
---

# DSC 180A Methodology Assignment 4

**Name:** Jay Manjrekar  
**Email:** [jmanjrekar@ucsd.edu](mailto:jmanjrekar@ucsd.edu)

**Section:** D10, Fusion Data Science: Databases and AI Models for Gyrokinetic Transport  
**Mentors:** Igor Sfiligoi (SDSC), Jeff Candy (General Atomics), and Emily Belli (General Atomics)

---

**1. What is the most interesting topic covered in your domain this quarter?**  
The most interesting topic was the hierarchy of turbulence models in fusion plasmas. Nonlinear gyrokinetic codes like CGYRO simulate turbulence from first principles but take about a day on 1,280 processes per case. The quasilinear model TGLF approximates the same fluxes in about a third of a second by using a fitted saturation rule. Neural network surrogates sit one level below that and can run more than 100,000 times faster than TGLF. What I found most interesting is that each step trades accuracy for speed, and errors carry down the chain. On the 43 SAT3 cases, TGLF misses the full CGYRO simulations by 19 to 46 percent on average, so a surrogate that copies TGLF perfectly still inherits those errors. It changed how I think about what "ground truth" means when the training labels come from another model.

**2. Describe a potential investigation you'd like to pursue for your Quarter 2 Project.**  
Our main Quarter 2 goal is to get the TGLF surrogate from about 97 percent to 99.9 percent of TGLF's variation explained (on the arcsinh scale). The investigation I'd most like to lead is a multi-fidelity model that beats TGLF against the full CGYRO simulations. The idea is to pretrain a network on thousands of cheap TGLF runs, then learn a small correction from the 43 CGYRO simulations, testing on cases left out of the fit. If that goes further than expected, or doesn't work out, a few related projects I could pursue are:
- **Active learning for data generation:** use ensemble uncertainty to decide where to run new TGLF cases, and measure how many fewer runs it takes to reach the same accuracy compared to random sampling.
- **Error bars that hold outside the training range:** our calibrated 90 percent error bars dropped to as low as 66 percent coverage when one input was out of range, so I'd study methods like conformal prediction with distance-aware adjustments.
- **Predicting the flux spectrum:** predict the flux at each of TGLF's wavenumbers and sum them, instead of predicting only the total flux, to see if it improves accuracy and physics trends such as isotope scaling.

**3. What is a potential change you'd make to the approach in your Quarter 1 Project?**  
I would set up shared infrastructure before splitting into individual models. In Quarter 1, our team used two different train/test splits and three different target transforms (arcsinh, log1p, and log10). Because of this, our scores could not be compared as a single leaderboard. My log10 transform also forced me to drop the four runs with negative ion heat flux. I'd switch to one common split, the arcsinh transform (which keeps negative fluxes), and a separate calibration set. I would also save test predictions from every model. My Gaussian process notebook had no saved outputs, so we could not report its results, and timing all models on the same machine would make our speed-up numbers comparable.

**4. What other techniques would you be interested in using?**  
- **Gaussian processes and Bayesian neural networks**, done properly this time, to get uncertainty estimates that can be compared directly with our ensemble and conditional VAE error bars.
- **Physics-informed constraints**, such as enforcing known trends (flux increasing with temperature gradient) or built-in symmetries, so the surrogate extrapolates more sensibly.
- **Transfer learning and multi-fidelity modeling**, such as fine-tuning a TGLF-trained network on CGYRO data, or co-kriging between the two fidelities.
- **Larger architectures and GPU training**, including residual networks and hyperparameter search with tools like Optuna, since our learning curves had not flattened yet.
- **Sensitivity analysis** (for example Sobol indices or SHAP) to explain which of the 11 inputs drive each flux, and to check the surrogate against known physics.
