# Learning a Turbocharged Engine's Air Path from ECU Datalogs

**Read the writeup: https://tuckerjashmore-afk.github.io/neural-ode-engine-model/**

A grey-box Universal Differential Equation model of a turbocharged engine, trained on datalogs
from a stock ECU with no dynamometer. Known thermodynamics stay as a fixed ODE skeleton; small
neural networks stand in for the constitutive maps a dyno would normally measure (volumetric
efficiency, compressor behavior, turbine and compressor efficiency, wall film, torque, EGT,
knock). Julia/SciML fits it, and a pure-Python predictor serves it from an exported JSON artifact.

Held-out manifold pressure is 6.12 kPa RMSE across a 9-232 kPa range on the current engine.

The project turned on two results. The first is negative: training every unknown map jointly
against the single observable a stock ECU log provides gives the best RMSE (9.8 kPa) and the worst
model, with volumetric efficiency above 1.0, turbo speed collapsed to a non-physical floor and
exhaust pressure below boost pressure. The errors cancel, so the trajectory fits while every map is
individually wrong. Fixing it meant identifying each map against its own ground truth offline, one
at a time, in an order where each stage leaves exactly one unknown.

The second is the one I am more pleased with. The remaining error looked like turbo lag and was not.
Boost was an algebraic function of a quantity that itself depended on boost, and at roughly 21% of
wide-open-throttle points that loop crossed a bifurcation that destroyed the high-boost equilibrium.
Replacing it with a first-order boost state improved every error percentile at a sixth of the
compute.

The writeup covers the model, the diagnostic chain, staged identification, the boost-state result,
a negative result about what a holdout score cannot see, and the measured limits.

## Source

This repository holds the writeup only. The model source is AGPL-3.0 but kept in a private
repository, because the ECU logs and the digitized manufacturer compressor map aren't mine to
redistribute. Code access on request.

Tucker Ashmore, 2026.
