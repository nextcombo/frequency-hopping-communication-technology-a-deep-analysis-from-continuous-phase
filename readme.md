# Frequency-hopping Communication Technology: A Deep Analysis from Continuous Phase FSK to System Engineering for Interference Reduction Transmission [with MATLAB Code]

Frequency-hopping communication technology in-depth analysis: from continuous phase FSK to anti-interference transmission system engineering
The FH-CPFSK transmitter system described in this paper operates at a frequency of about 2 GHz, based on a 6-channel frequency hopping set. It employs continuous phase frequency shift keying scheme with the modulation index and achieves phase accumulation for each bit under Sunde's FSK regime. The entire system completes the transmission of a 96-bit complete frame within a 12 microsecond stay time, with a high frequency hopping rate of 83.3 thousand hops per second.
II. System Architecture Overview: This transmitter system consists of the following core modules:
Source Coding Layer: 6 independent 8-character ASCII messages, each encoded as 64 bits of payload.
Frame assembly layer: Forward Error Correction (FEC) + Frame delimiter + header + payload + CRC, totaling 96 bits
CPFSK modulation layer: Modulation index, rectangular frequency pulses, full-response CPM
Frequency hopping layer: Based on 6-channel frequency hopping set, supports two modes of arrangement hopping and independent identically distributed hopping.
RF output layer: Real-valued RF waveform with random silence time.
The system parameters are summarized as follows:
Parameter item
Value
Modulation method
CPFSK,(Sunde's FSK)  
Bitrate
8 Mbps (ns)  
Frequency of Jump Frequency Center
2 GHz  
Number of channels
6  
Channel spacing
18 MHz  
Single hop bandwidth occupancy
16 MHz ()  
Protected Bandwidth  
2 MHz  
Frame Length  
96 bits (12.0 μs)  
Frequency Hopping Set Range  
1.955 – 2.045 GHz  
Total Occupied Frequency Spectrum  
106 MHz (1.947 – 2.053 GHz)  
III. Mathematical Basis of Continuous Phase FSK 3.1 Signal Model The core of continuous phase frequency shift keying lies in the fact that the phase is the time integral of the symbol stream, not reset at each symbol boundary. This characteristic endows the modulated signal with good spectral roll-off characteristics, significantly reducing out-of-band radiation.  
The complex envelope of the baseband CPFSK signal can be represented as:  
In which, the instantaneous phase is:  
In which:  
is the modulation index, determining the amplitude of phase change per bit  
is the polarity mapping for the ith bit (bit 1 → +1, bit 0 → -1)  
is the phase pulse function, whose derivative is the frequency pulse  
is the bit period  
For a full response CPM signal, the phase pulse function satisfies:  
This is the phase trajectory corresponding to the rectangular frequency pulse (1REC).  
3.2 Modulation Index h=1: Sunde's FSK In this system, we choose the modulation index, meaning that the net phase accumulation per bit is:  
Taking the derivative of the phase trajectory yields the instantaneous frequency offset:  
Therefore, two signals respectively lie at carrier frequencies MHz, with a pitch interval exactly equal to the bit rate MHz.  
This parameter selection has profound theoretical significance—it precisely meets the conditions for Sunde's FSK, namely that the pitch intervals between two signal tones are integer multiples of the bit rate:  
Under these conditions, both frequency signals maintain orthogonality under coherent detection:  
Sunde's FSK is the regime where orthogonal FSK systems achieve the minimum pitch interval, achieving an optimal balance between bandwidth efficiency and detection performance.
3.3 Phase Continuity Analysis
The accumulated phase at the end of the bit number is:
Under the action of a rectangular frequency pulse, the instantaneous phase during the bit period shows linear variation:
It should be noted that phase continuity only holds within a single hop frequency dwell. At the boundary of the hop, the frequency synthesizer retunes to the new channel, and the residual phase is unpredictable, requiring the receiver to treat each dwell as an independent synchronous process.
IV. Frequency Hopping Spread Spectrum Mechanism
The carrier frequencies and corresponding signal tones for each channel are as follows:
4.2 The selection of channel spacing must meet the following requirements:
The worst-case effective modulation index, taking into account phase tolerances. At that time:
Therefore, there is a 1.2 MHz protection margin. This constraint ensures that adjacent channels do not overlap in the frequency domain, avoiding inter-channel interference.
4.3 The processing gain of frequency hopping spread spectrum system against narrowband interference is:
For different types of interference sources, the system performs as follows:
Single-channel interference: Precise hit on one of the six dwells, processing gain 7.8 dB
Partial Band Interference (Individual Channel): Hitting persistence, processing gain
Full-band interference (covering 106 MHz): All dwells are affected, and the hopping processing gain is zero.
It is noteworthy that in the frequency hopping mode, each channel is used exactly once. A single-channel interference source will definitively destroy a message with an erasure rate of 16.7%. This analysis has direct implications for the reliability assessment of the system. V. Frame structure design Each hopping frequency carries a complete 96-bit frame:
The functional definitions of each field are as follows:
The leading code adopts an alternating bit pattern with a length of 8 bits. Under the modulation scheme, this pattern drives the phase trajectory to alternate between and ensures that the frequency-domain detector outputs a pure symbol rate square wave signal, providing an ideal reference for automatic gain control convergence and symbol timing recovery. The capture budget of 1 microsecond is compact, but it is sufficient to achieve synchronization locking under ideal channel conditions.
The frame boundary marker is selected as the Barker-5 sequence, which has a sharp peak characteristic in its autocorrelation function:
The sidelobe ratio is 5:1, still providing reliable frame boundary detection even at lower SNR.
The header consists of a 3-bit sequence number, supporting message numbers from 1 to 7. The receiver relies on this field to restore the original ordering of messages when they arrive scattered across a frequency hopping channel.
The CRC is implemented using the CRC-16/CCITT-FALSE standard, with a polynomial of:
It covers a total of 67 bits including the header + payload. The check equation is:
VI. Modeling Non-ideal Factors and Impact Analysis Real radio transceivers inevitably have non-idealities. This system models two key tolerances to assess the system's robustness against hardware deviations.
6.1 The phase trajectory tolerance modulator introduces a phase tolerance factor when generating phase changes, accumulating the actual phase by each bit:
The corresponding effective modulation index drift range is:
The frequency offset range fluctuates between and MHz.
The resampled mode of tolerance corresponds to different physical scenarios:
The "hop" mode (default) simulates the gain offset caused by tuning voltage variations in a varactor diode, closely approximating engineering reality.
Impact on detection performance: When, the two signals are no longer strictly orthogonal, but within the range, coherent detection has a matching loss of less than 1 dB. Non-coherent frequency domain detection is almost immune to this tolerance, as the decision quantity depends on the sign of the frequency deviation rather than the exact amplitude.
6.2 Bit period tolerance The frequency offset of the symbol clock is modeled with a tolerance factor, with a bit period range of:
That is, 124.875 ns to 125.125 ns, equivalent bit rate ranging from 7.992 to 8.008 Mbps.
The default mode is "burst" - physically, one transmitter has only one primary clock source whose frequency offset remains constant throughout the transmission rather than randomized hop-by-hop. Within a 96-bit frame length, the accumulated drift is about bit periods. This means that after receiving end acquires timing synchronization during the leading code period, it can maintain symbol alignment throughout the entire frame without continuous tracking.
Design Highlights: BitBorder is positioned on a cumulative score grid and processed in whole numbers, ensuring that constant clock offsets produce stable drift instead of accumulated rounding errors. The phase step for each sample point is independently scaled, ensuring that the phase tolerance and bit cycle tolerance are decoupled—the jitter in the bit cycle will not permeate into the frequency deviation. VII. Signal Envelope Shaping and Spectral Control
The slope length (number of samples). Due to the closely adjacent dwells, the envelope exactly passes through zero at the channel step, making the frequency jump occur almost without energy radiation. The spectrum spattering is thus significantly suppressed - although the phase trajectory of the rectangular frequency pulse itself is not band-limited signal, residual spattering still exists (the use of Gaussian filter frequency pulse can further eliminate it).
7.2 Bandwidth occupancy analysis: The theoretical bandwidth occupied (99% power bandwidth) is:
The worst-case occupied bandwidth considering phase tolerance is:
VIII. Receiver Requirements Derivation: The design parameters of this transmitter impose clear constraints on the receiver architecture.
8.1 The instantaneous bandwidth receiver can adopt two architectural routes:
Frequency hopping tracking: The instantaneous bandwidth requires only 16 MHz (single dwell), but the frequency synthesizer must complete re-tuning within the dwell interval.
With the advancement of ADC technology, a digital instrument with a sampling rate of 400 MSps can now perform bandpass sampling across the entire frequency range, significantly reducing the cost of implementing broadband acquisition routes.
8.2 The 1 microsecond capture window provided by the leading code of synchronization performance constraints needs to meet:
Receive
Condensation
Determined
Time
Lock
Determined
μ  
Parameters
Values
Symbol Rate
8 Msps
Required Oversampling Rate (Non-coherent Detection)
4–8 times
Required Oversampling Rate (Coherent Synchronization)
2–4 times
Corresponding ADC Sampling Rate
32–64 Msps
Interference Tolerance System Design: The required signal-to-interference ratio (SIR) for the interference tolerance system is dB, meaning the desired signal should be 20 dB stronger than the interference. For a single-channel interference source, even if the interference hits a stationary point completely, only one message (16.7% erasure rate) can be lost without forward error correction code protection. A feasible path to improve system robustness includes frame interleaving and error-correction coding, increasing the number of frequency hopping channels, and introducing automatic retransmission request mechanisms in bidirectional links.
Limitations of the System and Improvement Directions: Any engineering technical solution must balance performance, complexity, and feasibility. The known limitations of this system include:
Inter-hop zero guard interval problem. After a carrier step-up at 62.5 MHz, the frequency synthesizer needs time to re-lock, with the actual lock-in loop establishment time ranging from dozens to hundreds of microseconds. Even with direct digital frequency synthesis, there is still a certain pipeline delay. Placing inter-hop guard bits at a level of magnitude (corresponding to 125 μs to 1.25 ms) closer to hardware implementation is more practical.
No forward error correction protection. CRC-16 only provides error detection capability without error correction. In the mode of arranging frequency hopping, the probability of hitting a single-channel interference source is 1, inevitably losing one message. Introduce frame interleaving and error-correction coding (such as selecting any 6 frames out of 8 to restore all information), which can effectively deal with deterministic interference.
8-bit preamble capture budget is tight. Acquiring AGC convergence and timing lock within 1 microsecond poses stringent requirements for engineering implementation. Especially under real noise conditions, especially when the transient overlap of frequency synthesizer switching occurs, the length of preamble may need to extend to 16 or even 32 bits.
X. Conclusion This paper, from the perspective of signal systems and system engineering, systematically elaborates on the design principles of the FH-CPFSK transmitter. Starting from the continuous phase FSK mathematical model of modulation index, through frequency hopping set planning, frame structure design, to the quantification modeling of non-ideal factors, every link in the engineering decision is rooted in rigorous theoretical analysis.
This design paradigm that organically couples the modulation domain (CPFSK) with the spread spectrum domain (FHSS), while maintaining the optimal performance of single-stay signal detection, gains considerable anti-interference processing gain through frequency diversity. Whether it serves as an interference resistance scheme prototype in communication adversarial scenarios or as a teaching simulation case on SDR platform, this architecture provides a clear structure and adjustable parameters for reference.
The scheme described in this paper is designed at the transmitter end and does not include channel models, receiver demodulation, or noise analysis. Complete link simulation requires introducing additive white Gaussian noise (AWGN) at the receiver end and conducting bit error rate analysis.

## Images

![Deep Analysis of Frequency Shift Keying Technology: From Continuous Phase FSK to System Engineering for Interference-Resistant Transmission [with MATLAB Code]](01.jpg)
![Deep Analysis of Frequency Shift Keying Technology: From Continuous Phase FSK to System Engineering for Interference-Resistant Transmission [with MATLAB Code]](02.jpg)
![Deep Analysis of Frequency Shift Keying Technology: From Continuous Phase FSK to System Engineering for Interference-Resistant Transmission [with MATLAB Code]](03.jpg)
![Deep Analysis of Frequency Shift Keying Technology: From Continuous Phase FSK to System Engineering for Interference-Resistant Transmission [with MATLAB Code]](04.jpg)

<!-- PAYMENT_BLOCK -->

## Get this dataset

- Payment link (Stripe): [Buy now](https://buy.stripe.com/3cs8yP7sY87d0vu9AB)
- Payment QR code: ![Payment QR code](pay.png)

<!-- /PAYMENT_BLOCK -->
