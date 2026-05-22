# PSK
# Aim
Write a simple Python program for the modulation and demodulation of PSK and QPSK.
# Tools required
1. Google Colab
# Program
```
import numpy as np
import matplotlib.pyplot as plt

data = "10110011"

samples_per_bit = 100
carrier_freq = 5

# Time axis
t = np.linspace(0, len(data), len(data) * samples_per_bit)


digital_signal = []

for bit in data:
    if bit == '1':
        digital_signal.extend([1] * samples_per_bit)
    else:
        digital_signal.extend([0] * samples_per_bit)

digital_signal = np.array(digital_signal)


bpsk_signal = []

for i, bit in enumerate(data):

    start = i * samples_per_bit
    end = (i + 1) * samples_per_bit

    tt = t[start:end]

    # Phase 0 for bit 1
    if bit == '1':
        signal = np.sin(2 * np.pi * carrier_freq * tt)

    # Phase 180 for bit 0
    else:
        signal = -np.sin(2 * np.pi * carrier_freq * tt)

    bpsk_signal.extend(signal)

bpsk_signal = np.array(bpsk_signal)


demod_bpsk = []

for i in range(len(data)):

    start = i * samples_per_bit
    end = (i + 1) * samples_per_bit

    segment = bpsk_signal[start:end]
    tt = t[start:end]

    reference = np.sin(2 * np.pi * carrier_freq * tt)

    product = np.sum(segment * reference)

    if product > 0:
        demod_bpsk.append(1)
    else:
        demod_bpsk.append(0)


# Group bits into pairs
bit_pairs = [data[i:i+2] for i in range(0, len(data), 2)]

# QPSK phase mapping
phase_map = {
    '00': np.pi/4,
    '01': 3*np.pi/4,
    '11': 5*np.pi/4,
    '10': 7*np.pi/4
}

samples_per_symbol = 200

qpsk_signal = []

t_qpsk = np.linspace(0,
                     len(bit_pairs),
                     len(bit_pairs) * samples_per_symbol)

for i, pair in enumerate(bit_pairs):

    start = i * samples_per_symbol
    end = (i + 1) * samples_per_symbol

    tt = t_qpsk[start:end]

    phase = phase_map[pair]

    signal = np.sin(2 * np.pi * carrier_freq * tt + phase)

    qpsk_signal.extend(signal)

qpsk_signal = np.array(qpsk_signal)


demod_qpsk = ""

reverse_map = {
    np.pi/4: '00',
    3*np.pi/4: '01',
    5*np.pi/4: '11',
    7*np.pi/4: '10'
}

for i in range(len(bit_pairs)):

    start = i * samples_per_symbol
    end = (i + 1) * samples_per_symbol

    segment = qpsk_signal[start:end]
    tt = t_qpsk[start:end]

    max_corr = -999999
    detected_phase = None

    for phase in phase_map.values():

        reference = np.sin(
            2 * np.pi * carrier_freq * tt + phase
        )

        corr = np.sum(segment * reference)

        if corr > max_corr:
            max_corr = corr
            detected_phase = phase

    demod_qpsk += reverse_map[detected_phase]


print("Original Data        :", data)

print("BPSK Demodulated Data:",
      ''.join(map(str, demod_bpsk)))

print("QPSK Demodulated Data:",
      demod_qpsk)

plt.figure(figsize=(14, 12))

# Digital Signal
plt.subplot(4,1,1)
plt.plot(digital_signal)
plt.title("Digital Input Signal")
plt.ylim(-0.5, 1.5)

# BPSK Signal
plt.subplot(4,1,2)
plt.plot(bpsk_signal)
plt.title("BPSK Modulated Signal")

# QPSK Signal
plt.subplot(4,1,3)
plt.plot(qpsk_signal)
plt.title("QPSK Modulated Signal")

# Demodulated Signals
plt.subplot(4,1,4)

plt.step(range(len(demod_bpsk)),
         demod_bpsk,
         where='mid',
         label='BPSK')

qpsk_bits = [int(b) for b in demod_qpsk]

plt.step(range(len(qpsk_bits)),
         qpsk_bits,
         where='mid',
         label='QPSK')

plt.title("Demodulated Signals")
plt.ylim(-0.5, 1.5)
plt.legend()

plt.tight_layout()
plt.show()
```
# Output Waveform

<img width="1398" height="1189" alt="PSK_exp5" src="https://github.com/user-attachments/assets/62122f49-e27d-4042-a6ac-95235d96220e" />


# Results
```
```
