# STATA-NPIV 
Authors : Dongwoo Kim and Daniel Wilhelm

This project provides two Stata commands for nonparametric estimation of instrumental variable (NPIV) models with or without imposing monotonicity restrictions on the function of interest. The command `npiv` implements the estimators with user-chosen tuning parameters and `npivcv` with tuning parameters chosen by cross-validation.

More detailed description of the commands, their syntax, and examples can be found in Chetverikov, Kim, and Wilhelm (2017).

Files contained in this package:

- The file `npiv.ado` contains the `npiv` command.
- The file `npivcv.ado` contains the `npivcv` command.
- The file `2-step-estimation.do` contains code for a simulation experiment illustrating how to accommodate exogenous covariates with the `npiv` command.
- The file `mcsimulation.do` contains code for a simulation experiment that illustrates the relative performance of NPIV estimators with and without imposing shape restrictions.
- The files `mcsimulation_shape.ado` and `mcsimulation_shape.ado` are used in `mcsimulation.do`. 


## Installation
1. Download the package.
2. Install the `bspline` and `polyspline` functions from ssc by opening STATA and typing
	
	```
	ssc install bspline
	ssc install polyspline
	```

3. Change into the directory containing this package.
4. Use the commands `npiv` and `npivcv` as described below.

## Syntax
The commands `npiv` and `npivcv` estimate the function g(x) in the NPIV model

```
Y = g(X) + Z'γ + e      E[e | Z, W] = 0
```

where
- Y is a scalar dependent variable (`depvar`) 
- X is a scalar endogenous variable (`expvar`)
- W is a scalar instrument (`inst`)
- Z is a vector of exogeneous covariats (`exovar`) - optional (need not be included), and 

Syntax:

```
npiv depvar expvar inst [exovar] [if] [in] [, power_exp(#) power_inst(#) num_exp(#) num_inst(#) polynomial increasing decreasing]
npivcv depvar expvar inst [exovar] [if] [in] [, power_exp(#) power_inst(#) maxknot(#) polynomial increasing decreasing]
```

where
- `power_exp` is the power of basis functions for X (default = 2).
- `power_inst` is the power of basis functions for W (default = 3).
- `num_exp` is the number of knots for X (default = 2) when using a B-spline basis.
- `num_inst` is the number of knots for W (default = 3) when using a B-spline basis.
- `maxknot` is the maximum number of knots upto which cross-validation is executed. (default = 5)
- adding the `polynomial` option makes the estimator use a polynomial spline basis (default is B-spline basis).
- adding the `increasing` option makes the estimator impose the constraint that the estimator of g(x) is increasing in x.
- adding the `decreasing` option makes the estimator impose the constraint that the estimator of g(x) is decreasing in x.

In the current version of the code, shape restrictions can only be imposed with the B-spline basis. The code will produce an error when attempting to impose shape restrictions with a polynomial basis. When imposing a shape constraint, the current code also imposes the power of the B-spline for X to be 2.

Users can freely modify the power and the type of basis functions and the number of knots
when shape restrictions are not imposed.

If options are left unspecified, the command runs on the default settings.

The command `npivcv` estimates the function g(x) in the NPIV model using cross-validation to find the optimal number of knots (setting `num_exp=num_inst`).


## Output

The commands `npiv` and `npivcv` save their estimates of g(x) over a grid of values for x in the variable `npest`. In addition, the commands are of the e-class so other results such as the coefficient vector are stored in e(). 


## Examples

NPIV estimation with default options:
```
npiv y x w z
```

NPIV estimation with B-spline bases of powers 2 and 3, and 3 and 4 knots (for X and W, respectively):
```
npiv y x w z, power_exp(2) power_inst(3) num_exp(3) num_inst(4)
```

NPIV estimation with B-spline bases of powers 2 and 3, and 3 and 4 knots (for X and W, respectively), imposing that the estimator of g(x) is increasing:
```
npiv y x w z, power_exp(2) power_inst(3) num_exp(3) num_inst(4) increasing
```
NPIV estimation using cross-validation to determine the optimal number of knots, imposing that the estimator of g(x) is increasing:
```
npivcv y x w z, power_exp(2) power_inst(3) increasing
```


# Reference
[Chetverikov, D. and Wilhelm, D. (2017), "Nonparametric Instrumental Variable Estimation Under Monotonicity", Econometrica, 85(4), p. 1303-1320](http://onlinelibrary.wiley.com/doi/10.3982/ECTA13639/full)

[Chetverikov, D., Kim, D. and Wilhelm, D. (2017), "Nonparametric Instrumental Variable Estimation", CeMMAP Working Paper 47/17](http://www.ucl.ac.uk/~uctpdwi/papers/cwp471717.pdf)

