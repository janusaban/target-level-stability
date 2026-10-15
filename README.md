# target-level-stability

Pilot simulations for **target-level stability**: when can a specific scientific
quantity, such as the aperture flux of a star, be estimated reliably even though
the detector gain is unknown and must be estimated jointly with the sky image?

The notebooks support the pilot results reported in the research statement
*Reliable Machine Learning for Scientific Inverse Problems under Calibration
Uncertainty* (J. C. Aban).

## Model

Dithered exposures of a static sky are modeled as

    y_k = diag(g) A_k x + ε_k,    g = 1 + Bθ,

where A_k is a known shift, blur, and sampling operator, x is the sky image
(constant sky level, smooth extended emission, and stars at known positions),
and B holds smooth, zero-mean gain modes. For a target φ = wᵀx, the sensitivity
κ_φ splits, through a Schur complement, into a known-gain term and a
calibration term:

    κ_φ² = cᵀ(J_zᵀJ_z)⁻¹c + Σ_i (cᵀ M v_i)² / s_i²,

where s_i and v_i are the singular values and right singular vectors of the
gain block after projecting out image directions. A direction in which image
and gain are hard to separate inflates κ_φ only if the target couples to it.

## Notebooks

| Notebook | What it tests | Key results |
|---|---|---|
| `pilot_target_stability.ipynb` | Gaussian noise on a 32 × 32 detector with eight stars and four dithered exposures: estimability, the Schur-complement formula, joint estimation, and Monte Carlo validation | Median star-flux variance inflation 2.6 versus 44 for the least stable image direction; joint flux errors near 1% versus biases up to 8% under a flat-gain assumption; κ_φ predicts Monte Carlo scatter within 5% |
| `pilot_target_stability_poisson.ipynb` | The same problem under Poisson photon-counting noise, with κ_φ from the Poisson Fisher information, and a low-light stress test | Median inflation 1.8 versus 39; κ_φ predicts observed scatter within about 15% for sky backgrounds as low as 10 photon counts per pixel |
| `pilot_scaling.ipynb` | Matrix-free computation of the calibration term from 128 × 128 to 1024 × 1024 pixels, checked against a dense calculation | All 2,500 stars of a one megapixel field in 2.5 s and 89 MB on a CPU, versus 85 GB for an explicit Jacobian; conjugate-gradient iterations nearly constant across sizes |

Each notebook runs from top to bottom and includes checks before any result is
reported: the Schur-complement formula is compared with direct inversion, and
the matrix-free operators are compared with a dense Jacobian on a small field.

## Requirements

Python 3 with `numpy`, `pandas`, and `matplotlib`.

    pip install numpy pandas matplotlib

## Reproducing the results

Open a notebook and run all cells (Kernel → Restart & Run All). Random seeds are
fixed, so the Gaussian and Poisson results reproduce exactly. Timings in the
scaling notebook depend on the machine; memory use and iteration counts do not.

## Scope and limitations

- Star positions and the point spread function are known (forced photometry).
- Integer-pixel dithers and smooth gain only; pixel-level gain variations are
  not yet included.
- κ_φ is a local, linearized measure at the true parameters (a Cramér–Rao
  bound for locally unbiased estimators), validated here by Monte Carlo.
- Learned priors and conformal calibration at scale are future work.

## License

MIT
