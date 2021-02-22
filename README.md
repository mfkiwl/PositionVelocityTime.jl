
# PVT.jl
Calculates Positions by using GPSL1 Data

## Features
* User Position calculation
* Satellite Position calculation
* Precision estimation (GDOP)

## Preparing
____________

### Install
```julia
julia> ]add PVT
```

### Initializing
```julia
julia> using PVT, GNSSDecoder
julia> #decode Signals here
```
The decoding using the GNSSDecoder module must be completed before beginning.


Data must be combined in the provided `SatelliteState` struct. 
```julia
#decoder: Solution of GNSSDecoder
#codephase, carrierphase: Solution of Acquisition 
julia> SatelliteState(
    decoder_state = decoder, 
    code_phase = codephase,
    carrier_phase = carrierphase)
```
The declaration of `carrier_phase` is optional due to its small effect on the user position.

For user position computation at least 4 decoded satellites must be handed over. 

If using single functions, please check `is_sat_healthy_and_decodable` prior to computing.

```julia
#´dc´: Decoder
julia> is_sat_healthy_and_decodable(dc)
true
```

## Usage
___________________
### Satellite position
There are two options for satellite position calculation: 

- ECEF satellite position calculation (IS-GPS-200K Table 20-IV):
```julia
sat_position_ECEF(satellite_state::SatelliteState)

julia>sat_position_ECEF(satellite_state)
3-element Array{Float64,1}:
-8.537268174201585e6, 
-1.2988350094779423e7, 
2.1851582648954894e7
```
- ECI satellite position calculation (IS-GPS-200K 20.3.3.4.3.3):
```julia
sat_position_ECI2ECEF(satellite_state::SatelliteState)

julia>sat_position_ECI2ECEF(satellite_state)
3-element Array{Float64,1}:
 -8.537268174201572e6, 
 -1.2988350094779432e7, 
 2.1851582648954894e7
```

_______________________
### User position Calculation
The function 
`calc_PVT(satellite_states::AbstractVector{SatelliteState{Float64}})` 
provides a complete position calculation. Since this function checks the input arguments for usability, all data can be passed.

```julia
#´satellite_states´: Struct of satellite data
julia> calc_PVT(satellite_states)
```

Output:
```julia
PVTSolution(
    [4.0186749839887144e6, 427051.1942215096, 4.918252576909532e6],
    -2.3409334780245904e7, 
    17.961339568218765)
```

The first member `pos` represents the user position in ECEF coordinates, the second `receiver_time_correction` the calculated travel time correction. The third member `GDOP` represents the Geometric Dilution of Precision.  



