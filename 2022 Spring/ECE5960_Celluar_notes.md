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

MIMO (multiple input/multiple output)

$N_t,N_r$ are transmit antenas and receive antenas 

We cab create up to min($N_t,N_r$)transmission layer

->independent paths from transmitter to receiver

- Diversity -> better BER performance
- Spacial multiplexing -> higher data rates

transmission rate = $N_L$ = layers

Closed loop spatial multiplexing

- CSI(channel state information) used to update channel estimates
- select precoders matrices->precoding makes layers look like independent channels

Release 8 Bcsics

- peak 150mbps donlink with 2 layers in 20mHz BW
- 75mbps in uplink (1 layer)
- overall latency - 10ms (100ms max tolerance for voice)

Release 10 - lake 2010 "Evolution"

           - carrier aggregation
           - advanced mimo
           - relaying

Release 13 - "LTE advanced pro"

- 4.5G
- Licensed-assisted access to unlicensed spectra
- enhanced carriers aggregation / mimo

**Carrier aggregation** - A big success

Release 10 - Up to component carriers -> up to 100mHz total



Later we get up to 32 component carriers->up to 640 mHz



Fragmented spectra -> batches of subcarriers not contiguous



License assisted - "gentle" use of ISM bands using cellular air interface



MIMO enhancements

"Full dimension mimo"

- increase CSI
- spatially separated devices can use the same time/frequency resources->beamforming

Densification (relaying)

- relaying: relay cell that looks like UE (user equipment) to the donor cell



**Heterogeneous Deployments**

​               microcells - neighborhood

​               femtocells - residence

- small cell on/off -> micro/femtocells can be turned off to reduce interference

**Dynamic TDD**

​                         **Duplex** - two way channel

​                         **FDD** - Frequency division

​                         **TDD** - time division

- same carriers freq. used for uplink and downlink
- change configuration -> allocation of resources varies

New Scenarios

- Device to device -> important for low-coverage areas
- Machine type communication
  - narrow band IoT (Release 13) < 250 kbps



**NR Overview / Chapter5**

main benefits of 5G over LTE

- exploitation of much higher frequency bands
  - aggregating
- Ultra-?le design in over the air (OTA) protocol
  - minimize **always-on** approach
- forward compatibility
- low latency -> expands use cases
- extensive use of beom forming 
  - strong need for CSI

**Radio-Interference Architecture / Chapter 6**

Key issue: split between radio access netork (RAN) and core network (CN)

RAN-radio-related functionality

- scheduling
- link technologies
  - HARQ protocols
  - coding
  - mimo

CN-infrastructure, non-radio

- authentication
- end-to-end connections
- charging / rules function



LTE Core - envolved packet core (EPC)

5G-RAN->EPC

​      ->5G CN (evolutionary path)

EPC -> 5GCN

- services-based architecture
- network slicing support
- control plane / user plane split



**Service-based architecture**

- focus on service/functionality/process instead of nodes/services -> services are more transportable/flexible

Network slicing

- Dates back to PSTN
- slice = logical network with necessary functions for customers   -> virtual networks
- multiple slices may run on same services

Control Plane / user plane split

- independent scaling

**5G RAN**

gNB - g node B - base station

​      part of both user/control plane

ng-eNB - next generation e node B

   - LET user/control planes

Can cover several cells

- logical node, not a physical device



AMF = access and mobility management functions



UE (user equipment) connectivity example

Dual connectivity - connections through multiple cells ( not as tightly coupled as carriers aggregation)



**Quality of Service (QoS)**

Voice-low data rate, high BER, low latency application

Streaming video - low BER, high latency

Email - medium BER, high latency 

QCI = QoS class Identifies

  1 = Voice

  6 = Data

  8 = throttling



Connected Device

- PDU sessions
  - QoS flow
  - data radio readers ?

## Lecture 4

**Radio Architecture**

Apps - user interface

​	IP packets

Apps -> mapping to QoS flows (UPF) -> mapping to radio bearers (gNB) -> channel -> UE

SDAP - service data app protocol

packets marked with quality flow identifiers (QFI)



User plane protocols (Downlink)

​                       QoS flows

user plane protocol -> SDAP -> PDCP (packet data convergence protocol) Header component -> RLC (radio link controller) segmentation ARQ -> MAC, multiplex and HARQ -> PHY

- segmentation: breakets packets up into transport blocks
- ARQ - automatic repreat request

- MAC - medium access control
- HARQ - Hybrid-ARQ



Service Data application protocol (SDAP)

- mao QoS flows to data radio bearers
- marks packets with QFI

Packet Data convergence protocol (PDCP)

- IP header compression (ROHC)
- encryption
- duplicate removed
- re-ordering for insequence delivery
- re-transmisssions of uplink packets not delivered due to handover
- dual connectivity
  - handles split between mater and secondary cell groups



**RLC**

- segmentation of RLC SDU's into RLC PDU's
- modes
  - transparent mode (no headers added)
  - unacknowledged mode (segmentation, duplicate detected)
  - acknowledged mode (ARQ)

Segmentation - Fill up transport block

             - update headers to denote segemtned SDU

ARQ 

- Included on RLC and MAC
  - RLC - sequence based, detects missing packets
  - MAC - detects symbol errors -> faster



MAC

logical channels (symantics)

- broadcast control channel (BCCH)
  - system information

- paging control channel (PCCH)
  - Notification of incoming calls
  - based on multiple cells / location area
- Common control channel (CCCH)
  - Control info related to random access
- Dedicated Control channels (DCCH)
  - Device configuration
- Dedicated Traffic Channel (DTCH) -> traffic channel
  - uses Data

Logical Control Channel (BCCH, PCCH, CCCH, DCCH)

---

Radio Interface Architecture 

logical channels -> semantic context

<img src="./ECE5960_Celluar_notes.assets/image-20220206142928186.png" alt="image-20220206142928186" style="zoom:50%;" />

Transport Channels

-> transport blocks

- one per transmission time interval = 1.0 ms

  (if layers <= 4)

- two transport blocks per TTI when layers >= 4

- TTI is scalable in 5G to reduce latency

-> Transport format (TF)

- size of blocks
- modulation and coding
- antenna mapping

All above three determines data rate



**Types of Transport Channels**

- Broadcast channel (BCH)
  - Fixed TF
  - Conveys master InFo blocks (MIB)
- Paging channel (PCH)
  - paging info
  - supports DRX (discoutinuous reception)
    - sends info at predetermined times
    - handset can power down periodically

- Downlink shared channel (DL-SCH)
  - man transport channel for downlink user data
  - dynamic rate adaptation
  - channel dependent scheduling
  - spatial multiplexing
  - HARQ with soft combining
- Uplink shared channel (UL-SCH)
- Random access channel (RACH)
  - no transport blocks

