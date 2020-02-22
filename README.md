# CygnusRFI: An open-source Radio Frequency Interference analyzer for Ground Stations & Radio Telescopes
<p align="center">
  <img src="https://i.imgur.com/lH2OOTd.png?raw=true" alt="VIRGO Spectrometer"/>
</p>

## About CygnusRFI
**CygnusRFI** is an easy-to-use **open-source** Radio Frequency Interference (RFI) analysis tool, based on [Python](https://www.python.org) and [GNU Radio Companion](https://wiki.gnuradio.org/index.php/GNURadioCompanion) (GRC) that is conveniently applicable to any ground station/radio telescope working with a GRC-supported software-defined radio (SDR). In addition to data acquisition, CygnusRFI also carries out automated analysis of the recorded data, producing a series of **averaged spectra** covering a wide range of frequencies of interest. CygnusRFI is built for ground station operators, radio astronomers, amateur radio operators and anyone who wishes to get an idea of how "radio-quiet" their environment is, using inexpensive instruments like SDRs.

A list of GRC-supported SDRs can be found [here](https://wiki.gnuradio.org/index.php/Hardware).

![alt text](https://i.imgur.com/zS5ZjK0.png) //SCREENSHOT OF TERMINAL

## Example Observation
![alt text](https://i.imgur.com/K8g0wVd.png "Example Observation") //RFI PLOT IMAGE EXAMPLE

## GRC Data Acquisition Flowgraph
**CygnusRFI** is a [**polyphase filterbank** spectrometer](https://arxiv.org/abs/1607.03579). The raw I/Q samples are processed in real time using GNU Radio, with the amount of data stored to file being drastically reduced for further analysis. The following flowgraph handles the acquisition and early-stage processing of the data:

![alt text](https://i.imgur.com/2Xp8qnZ.png "Data Acquisition Flowgraph")

## Spectral leakage: a comparison between ACS, FTF and PFB spectrometers
The noteworthy advantage of polyphase filterbanks is **reduced spectral leakage**, with a slight increase in computational requirements. The following figure compares the spectral leakage produced by an autocorrelation spectrometer (ACS), a Fourier transform filterbank spectrometer (FTF) and a polyphase filterbank spectrometer (PFB) with a Hann FFT window:
![alt text](https://i.imgur.com/e5TwE3w.png "Spectrometer comparison regarding spectral leakage")
Source: [Danny C. Price (2018)](https://arxiv.org/abs/1607.03579)

Although not strictly necessary, in this application, minimal spectral leakage allows us to more effectively suppress sidelobes that are unintentionally produced next to the actual signal by the traditional FFT algorithm. If for any reason PFB is not used by the user (e.g. due to potential computational expense), it is suggested to at least use a window function when computing the FFT spectra, in order to suppress the generated sidelobes to a somewhat acceptable level.

## A graphical representation of a polyphase filterbank
![alt text](https://i.imgur.com/HUFTmTh.png)
Source: [Danny C. Price (2018)](https://arxiv.org/abs/1607.03579)

## Data Analysis
Once a single spectrum observation is finished and the data has been acquired and stored to `X.dat`, the FFT samples (interpreted as a [numpy array](https://docs.scipy.org/doc/numpy/reference/generated/numpy.array.html) in `rfi_plotter.py`) constitute the **dynamic spectrum (waterfall)**, from which the **averaged spectrum** of the observation can be derived.

We can mathematically interpret the dynamic spectrum as a two-dimensional matrix with ***m*** rows and ***2<sup>n</sup>*** columns, where *m* ∈ ℕ\* is the total number of FFT samples (integrations) and *2<sup>n</sup>*, *n* ∈ ℕ is the number of frequency channels (FFT size).

In `rfi_plotter.py`, this matrix is defined as a 2D numpy array at [line 29](https://github.com/0xCoto/VIRGO/blob/master/plot.py#L29). //EDIT MEEEEEEEEEEEEEEEEEEEEEEEE

![alt text](https://i.imgur.com/JksgAav.png)

### Averaged Spectrum Derivation
If we average with respect to the frequency channels (`zmean = np.mean(a=z, axis=0)`), we get a new *1* × *2<sup>n</sup>* **row matrix** (or **row vector**), which is the <ins>averaged spectrum</ins> of the observation. This is defined at [line 39](https://github.com/0xCoto/VIRGO/blob/master/plot.py#L39) of `rfi_plotter.py`. //EDIT MEEEEEEEEEEEEEEEEEEEEEEEE

## Installation
To use **CygnusRFI**, make sure **[Python](https://www.python.org/) (Version 2.7)** and **[GNU Radio](https://wiki.gnuradio.org/index.php/InstallingGR)** (with **[gr-osmosdr](https://osmocom.org/projects/gr-osmosdr/wiki)**) are installed on your machine.

Once Python and GNU Radio are installed on your system, navigate to a directory of your choice (e.g. `cd Desktop`) and run:

```
git clone https://github.com/0xCoto/CygnusRFI
```

#### If you do not use an RTL-SDR
Once the repository has been cloned, open `pfb.grc` using GNU Radio Companion and replace the `RTL-SDR Source` block with the  source block of your SDR (e.g. `UHD: USRP Source`). After modifying the properties of the new SDR Source block (optional), click the little button next to the **Play** button to generate the new and updated version of `top_block.py` that is compatible with your SDR:

![alt text](https://i.imgur.com/F16haLm.png)

(You only need to do this once.)

## Usage
Once **CygnusRFI** is downloaded on your system and the `SDR Source` block has been replaced (unless you use an RTL-SDR where you shouldn't need to change anything), you can begin monitoring RFI with **CygnusRFI** by running:

```
python CygnusRFI.py
```

From there, the interactive software should ask you for the parameters of your RFI measurement, which you can simple enter in and let **CygnusRFI** do its magic! Once the observation is finished, your data will be processed, analyzed and saved as `rfi_plot.png` (in the same directory as `observe.py`).

## To do
- [ ] Argparse argument support
- [ ] Support spectrum calibration to flatten the bandpass shape of the SDR out

## Credits
**CygnusRFI** was created by **[Apostolos Spanakis-Misirlis](https://www.github.com/0xCoto/)**.

**Contact:** [0xcoto@protonmail.com](mailto:0xcoto@protonmail.com)
