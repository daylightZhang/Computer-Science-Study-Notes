#  ECE5960 Modern Cellular Communication

[TOC]



## Lecture 1

5G revolutionary

- **Gpbs rates in a handheld device**
  - massive mimo (higher frequencies, FCC->towers everywhere)
  - celluar convergence (WiFi+celluar+Bluetooth, all apps)
  - Edge computing - cloud

### Syllabus

- Book-5G NR
- Intro to 5G read chapters 1-3
- Review LTE / 4G  chapter 4
  - OFDM
  - carrier aggregation
- NR Overview chapter 5
- Radio Interface Architecture chapter 6
  - 5G Core Network
  - Radio Access
- Transmission Structure
  - OFDM part 2
- Channel Estimation
  - Downlink CSI
  - Uplink CSI
  - measurements and Reporting
- Transport Channel Processing
  - Error control codes
    - LDPC
    - Polar
  - Modulation / Reference signals
  - Multi-Antenna Precoding
- Physical layer control signaling
- Massive mimo
- retransmission Protocols
- Scheduling
- Uplink Power Control / Initial Access
- Edge cloud computing
- Privacy and Security

### Cellular

Cellular -> Divide coverage area up into clusters of cells

​				clusters has a "reuse pattern"

​                every cell in the cluster receive a 

​                portion of the cellular resources

​         -> pattern repeats with every clusters

<img src="./ECE5960_Celluar_notes.assets/image-20220205162753951.png" alt="image-20220205162753951" style="zoom:50%;" />

- Co-channel interferers are well separated due to the re-use pattern

**1G**

- 1980 NMT nordic mobile telecom
- 1983 amps advanced mobile phone service
- Analog FM voice
- Digital BPSK on control channel
- FDMA / FDD

**2G** - TDMA

- 1990-GSM-Big winner
- Digital voice, 9600 bps data
- Texting

**3G**

WCDMA - 2000

CDMA-2000

Both used CDMA / FDD -> TDD introduced important for 5G

**4G**

LTE - 2010

OFDM / FDD and TDD

Pachet vocie -> VoLTE

(Voice is now VoIP)

**5G**

NR - new radio 2020

new use cases

- EMBB enhanced mobile broadband
  - maxmize data rates > 1Gbps
- MMTC massive machine type comm
  - large numbers of devices
  - low cost, low energy, low data rate->OFDM makes this possible
- VRLLC (ultra reliable, low latency comm)
  - traffic safety -> autonomous vehicles
  - factory cutomatory
  - special link technologies make this possible

## Lecutre 2

==standards==

- interfaces
- protocols
  - messages
  - responses to events
- 3GPP advelops cellular standards
  - working groups
- ITU - International Telecom Union->national representatives
  - keep everyone organized
  - Governs use of spectrum
  - Includes placement of geosynchronous satellites

Celluar Spectrum (Chapter 3)

- more to higher bands
  - changes in propagation
  - smaller antennas -> massive mimo

**60 GHz** - oxygen line

-> limited propagation

​	good for small cells

​	great for small antennas（天线）

- Lower (<2GHz)

  600,700mHz

  20mHz Bandwidth

- medium (3-6GHz)

  3300-4200 mHz

  100 mHz Bandwidth

- Higher (>24GHz)

  millimeter ware

  24.25-29.5 GHz

  400 mHz Bandwidth

Current Aggregation

Use second bands at a time -> much higher data rates



**LTE/4G**

Release 8 (2008, implemented in 2009)

OFDM - was already in use in 802.11 (WiFi)

->orthogonal frequency division multiplexing

- multicarriers transmission

  - N subcarriers

  - Spaced in frequency as closely as possible, while still orthogonal

  - Orthogonality is a function of symbol rate

OFDM review - read bingham paper

Wireless - time dispension / delay spread

- multiple images received, variable delays
- intersymbol interferences (ISI)



Bit error rate becomes a function of ISI

-> increasing power does not help

**Solution**

- limit symbol rate on carriers to limit ISI
- Use multiple carriers to increaase data rate
- old idea - kineplex 1957
- Key technology - FFT 

Data -> Serial / Parallel -> IFFT -> D/A -> LPF(time domain waveform whose frequency components are data)



- Symbol rate per carrier is slower -> reduce ISI

- Place cyclic prefix is guard interval?
  - repreat end of last symbol in guard interval
  - makes linear convolution of received signal into a circular convolution->further reduces ISI

**LTE** subcarriers spacing 15kHz

- 15K symbols / sec / subcarriers
- 1200 subcarriers in 20 mHz
- cyclic prefix 4.7 us

*cyclic prefix should as long (induration) as the period of time over which the channel creates substantial replicas

- confined space -> small time dispersion

  ​               -> short cyclic prefix

- open areas -> larger time dispersion

  ​           -> longer cyclic prefix (less date rate)



Transmission Timings

1 radio frame = 10 one-millisecond subframes

1 subframe = 14 OFDM symbols

* subframe is smallest schedulable unit in LTE

1 subframe = 2 slots

1 slot = 0.5 ms = 7 symbols (normal cyclic prefix)

​                = 6 symbols (extended cyclic prefix)



Resource block = 12 subcarriers x 1 slot

Resource element = 1 subcarriers x 1 slot



-  We vary date rate by varying number of assigned resource blocks

- Not all subcarriers are used

  - fading

  - reference signals

    pilot symbols provide info needed for coherent demodulation (demodulation requiring phase information)

  - reference signals are known complex values in known places in the OFDM time/frequency grid. Values are a function of cell ID and location in grid



---

Contributions to data rate

- resources blocks assigned

  - modulation format QPSK -> 64QAM

- error control 

  - turbo coding

  - LDPC (low density paradity check)

  - Polar codes (control plane)

  - Fast-HARQ with soft combining 

    Hybrid-Automatic-Repeat-Request

**modulating**

- varying frequency, phase, and/or amplitude in accord with information

QPSK  Quadrature - PSK



16-Quadrature Amplitude Modulation (16-QAM)



64 QAM -> 6 bits symbol



7 symbols/slot/subcarriers

12 subcarriers/resource block

84 symbols/slot/resource block



Example 16-QAM

4 bits/symbol -> 84*4 = 336 bits/slot/resource block

2 slot/ms -> 672 bits/ms/RB

Rate 1/3 turbo code -> 224 kbps/RB

1200 subcarriers in 20mHz -> 100 RB -> 22.4 mbps / 20 mHz



**Scheduling**

Uplink assignment once per ms

- prorided on physical downlink controol channel (PDCCH)

<img src="./ECE5960_Celluar_notes.assets/image-20220205173009539.png" alt="image-20220205173009539" style="zoom:67%;" />

PDCCH - up to first 3 symbols of each subframe full BW of 20mHz

PUCCH - uplink - 1ms

​	ACICs, NACKs, CSI (channel stak information)





## Lecture 3

