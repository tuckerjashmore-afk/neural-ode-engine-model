# Identifying a Grey-Box Engine Model from a Single ECU Datalog

**Read the writeup: https://tuckerjashmore-afk.github.io/neural-ode-engine-model/**

A grey-box Universal Differential Equation model of a turbocharged Subaru EJ255 — known
thermodynamics as a fixed ODE skeleton, neural networks standing in for the unknown constitutive
maps (volumetric efficiency, compressor pressure ratio, turbine and compressor efficiency,
wall-film, torque, EGT, knock). Julia/SciML fits it; a pure-Python predictor serves it from an
exported JSON artifact.

The headline result is a negative one. Training all eleven maps jointly against the single
observable a stock ECU log provides produces the best manifold-pressure RMSE (9.8 kPa) and the
worst model: volumetric efficiency above 1.0, turbo speed collapsed to a non-physical floor,
exhaust pressure below boost pressure. The errors cancel, so the trajectory fits while every map
is individually wrong.

Fixing it meant identifying each map against its own ground truth offline, one at a time, in an
order where each stage leaves exactly one unknown — recovering the engine's real compressor
operating points by inverting the steady manifold balance rather than fitting them. Turbo speed
went from a flat collapse to tracking its physics-derived target at correlation 0.948.

The writeup covers the model, the diagnostic chain, the staged identification method, the
results, and the measured limits.

## Source

This repository holds the writeup only. The model source is AGPL-3.0 but kept in a private
repository, because the ECU logs and the digitized manufacturer compressor map aren't mine to
redistribute. Code access on request.

Tucker Ashmore, 2026.
