# XMM_BSO_DATA
Processed blank-sky data from the XMM-Newton Space Telescope.

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![arXiv](https://img.shields.io/badge/arXiv-21xx.xxxxx%20-green.svg)](https://arxiv.org/abs/21xx.xxxxx)

![RingFlux](/data/XMM-Ring-Flux.png "XMM Newton Flux divided into 30 rings for the MOS and PN Cameras")

Data collected by the [XMM-Newton](https://www.cosmos.esa.int/web/xmm-newton) X-ray space telescope divided into 30 concentric annuli around the Galactic Center, a dataset ideal for performing searches for dark matter decay originating from the Milky Way halo. The above figures show the total flux in [photons/s/keV/sr] in each ring separately,for both MOS and PN camereas over the energy ranges specified.

If this data is used in published work, please cite [21xx.xxxxx](https://arxiv.org/abs/21xx.xxxxx).

## Authors

- Christopher Dessert
- Joshua Foster
- Marius Kongsøre
- Yujin Park
- Nicholas Rodd
- Benjamin Safdi

## Data

A detailed description of the data provided here can be found in [21xx.xxxxx](https://arxiv.org/abs/21xx.xxxxx). In short, we take all data collected by XMM-Newton from its launch until 5 September 2018. We then remove any observation with a greater than median flux [photons/s/keV] over 2-10 keV (determined separately for MOS and PN), with a total exposure less than 500 ks, or that falls within 2 degrees of the Galactic Plane. The suriving observations are then combined into 30 rings, each of width 6 degrees as measured in angular distance from the Galactic Center.

The data can be found in the `data` directory, and is stored using the [h5py](https://www.h5py.org/) format. There are two files, `XMM-BSO-dataset-MOS.hdf5` and `XMM-BSO-dataset-PN.hdf5`, which store the MOS and PN data, respectively. Within each file there are 30 rings, accessed with keys `ring_[1-30]`. Within each ring, the following data is provided:
- `cts`: the observed (integer) counts in each output bin;
- `exp`: the total exposure in [s] for this ring;
- `wht`: the appropriate weight of this ring in [s.sr], used for combining rings if necessary;
- `Dfc_fid`: our fiducial D-factor in [keV/cm^2];
- `Dfc_NFW`: the D-factor in [keV/cm^2] for a pure the best fit NFW profile;
- `Dfc_cDM`: the D-factor in [keV/cm^2] for a contracted dark matter halo.

The D-factors are all based on data taken from [Cautun et al 2020](https://academic.oup.com/mnras/article-abstract/494/3/4291/5821286?redirectedFrom=fulltext), and additional detail is provided in our paper.

In addition, the data files contain the information regarding the energy associated with each bin, which applies for all rings. These can be accessed by key `emin_o` and `emax_o`, which give the energy in [keV] of the lower and upper edge of each output bin, which is what the `cts` data above is specified in. In addition, we provide `emin_i` and `emax_i` which is the analogous product for the input energy bins. These are only required when using the instrument response files. These are separately provided [here](...), as they are considerably larger. There, the instrument response is provided in the same ring-by-ring format as above, with key `dtr` in [cm^2].


As a minimal example, the counts in ring 5 for the MOS camera can be loaded as follows

```
import h5py

ms = h5py.File('./data/XMM-BSO-dataset-MOS.hdf5', 'r')
r5 = ms['ring_5']
cts5 = r5['cts']
```

In addition, we provide the counts, exposure, and weight files in [healpix](https://healpix.jpl.nasa.gov/) format, which can be used directly or for construction an alternative binning. These files are contained in `xmm-ring-full.tar.gz` found within the `data` directory. The format for the files is similar to the above, but each array is associated with an `nside=512` healpix index. The files are stored as [sparse matrices](https://docs.scipy.org/doc/scipy/reference/sparse.html), and an example of how the counts for the PN camera in the 18th ring can be loaded using the syntax below (after unpacking the directory),

```
from scipy import sparse

pncts = sparse.load_npz('./data/xmm-ring-full/pn_cts_ring18.npz')
```

## Supplementary Data for [21xx.xxxxx](https://arxiv.org/abs/21xx.xxxxx)

In `SuppData/Fiducial_Exposures_Deep_Search.csv` we provide the full list of 29,578 exposures used in the
fiducial analysis of [21xx.xxxxx](https://arxiv.org/abs/21xx.xxxxx). For each exposure, the following
information is
provided:

1. `Observation ID`: the 10 digit identifier for this observation.
2. `Camera`: the camera this exposure was collected with: MOS1, MOS2, or PN.
3. `Exposure Identifier`: the unique identifier for this exposure given the camera and observation ID.
4. `Exposure Time`: the length of this exposure [ks].
5. `l`: the location of this exposure in galactic longitude [deg].
6. `b`: the location of this exposure in galactic latitude [deg].
7. `Target`: name of the intended observation target.
8. `Target Type`: the intended type of the target.
