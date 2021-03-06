![](https://github.com/KM3NeT/Neurthino.jl/raw/master/docs/src/assets/neurthino.png)

[![Build Status](https://travis-ci.com/KM3NeT/Neurthino.jl.svg?branch=master)](https://travis-ci.com/KM3NeT/Neurthino.jl)
[![Codecov](https://codecov.io/gh/KM3NeT/Neurthino.jl/branch/master/graph/badge.svg)](https://codecov.io/gh/KM3NeT/Neurthino.jl)



# Neurthino.jl

**The package is currently under development**

**Neurthino.jl** is a package for calculating neutrino oscillation probabilities.
The main focus of the package lies on atmospheric neutrino flux and the neutrino
propagation through earth.

### Usage
First of all the basic vacuum properties have to be defined via creating a
`OscillationParameters` struct with fixed number of neutrino flavours of the 
considered model.
```julia
julia> using Neurthino

julia> osc = OscillationParameters(3);
```
The values of the mixing angles, mass squared differences and CP phases are 
initialised to 0 and have to be set individually.
```
julia> mixingangle!(osc, 1=>2, 0.59);

julia> mixingangle!(osc, 1=>3, 0.15);

julia> mixingangle!(osc, 2=>3, 0.84);

julia> cpphase!(osc, 1=>3, 3.86);
```
The mass squared differences are defined as <img src="https://render.githubusercontent.com/render/math?math=\Delta_{ij}=m_i^2-m_j^2"> and
within the package the convention <img src="https://render.githubusercontent.com/render/math?math=\forall%20i%3Cj:m_i%3Cm_j"> is kept.
```
julia> masssquareddiff!(osc, 2=>3, -2.523e-3);

julia> masssquareddiff!(osc, 1=>2, -7.39e-5);

julia> masssquareddiff!(osc, 1=>3, -2.523e-3-7.39e-5);
```
These oscillation parameters can now be used in order to calculate the transition
probabilities between the flavour states. 
```
julia> transprob(osc, 1, 10000)
3×3 Array{Float64,2}:
 0.684582  0.0964727  0.218946
 0.059416  0.790557   0.150027
 0.256002  0.11297    0.631027
```
The probabilities are calculated based on the transition matrix 
(the so-called PMNS-Matrix) between flavour and mass eigenstates,
as well as the Hamiltonian in the mass eigenbasis. In order to calculating these 
just once, the `transprob` function can also be called in the following way:
```
julia> U = PMNSMatrix(osc)
3×3 Array{Complex,2}:
   0.82161+0.0im         0.550114+0.0im        -0.112505+0.0983582im
 -0.301737+0.0608595im   0.601232+0.0407488im   0.736282+0.0im      
  0.476688+0.0545516im  -0.576975+0.0365253im   0.659968+0.0im

julia> H = Hamiltonian(osc)
3-element SparseArrays.SparseVector{Float64,Int64} with 3 stored entries:
  [1]  =  -0.000865633
  [2]  =  -0.000816367
  [3]  =  0.001682

julia> transprob(U, H, 1, 10000)
3×3 Array{Float64,2}:
 0.684582  0.0964727  0.218946
 0.059416  0.790557   0.150027
 0.256002  0.11297    0.631027
```
For the neutrino propagation through matter a modified PMNS-Matrix and Hamiltonian
has to be determined. The matter is parametrised by its density. 
```
julia> H_mat, U_mat = MatterOscillationMatrices(U, H, 13);

julia> H_mat
3-element Array{Complex{Float64},1}:
 -0.0008318913110425209 + 1.402405683460369e-20im 
  0.0009755131119987117 + 4.535370547007611e-21im 
  0.0018408194174787428 - 2.1947559170628515e-20im

julia> U_mat
3×3 Array{Complex{Float64},2}:
 0.0117205-2.18724e-5im    0.8666+0.0im       -0.374878+0.32914im   
 -0.665633+0.00279569im  0.287503+0.2445im     0.643807+0.0im       
  0.746182+0.0im         0.241939+0.219159im   0.580206-0.00274678im
```
The transition probabilities can then be calculated using the `transprob` function
again.
```
julia> transprob(U_mat, H_mat, 1, 10000)
3×3 Array{Float64,2}:
 0.252869  0.428127  0.319004
 0.414751  0.306335  0.278914
 0.33238   0.265538  0.402082
```

<!-- ```@index -->
<!-- ``` -->
<!--  -->
<!-- ```@autodocs -->
<!-- Modules = [Neurthino] -->
<!-- ``` -->
