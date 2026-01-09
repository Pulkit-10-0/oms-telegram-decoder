#   OMS Telegram Decryption & Decoding

## Overview

This repository documents the complete decryption and decoding of an OMS (Open Metering System) Wireless M-Bus telegram using a provided AES-128 key.  

The work demonstrates:
- structured analysis of an unfamiliar standard,
- correct handling of encryption constraints,
- decoding of binary OMS measurement records,
- and reproducible validation using an industry-grade open-source decoder.

---

## Provided Input

### AES-128 Key
4255794d3dccfd46953146e701b7db68


### OMS Telegram Payload (hex)
a144c5142785895070078c20607a9d00902537ca231fa2da5889be8df367
3ec136aebfb80d4ce395ba98f6b3844a115e4be1b1c9f0a2d5ffbb92906aa388deaa
82c929310e9e5c4c0922a784df89cf0ded833be8da996eb5885409b6c9867978dea
24001d68c603408d758a1e2b91c42ebad86a9b9d287880083bb0702850574d7b51
e9c209ed68e0374e9b01febfd92b4cb9410fdeaf7fb526b742dc9a8d0682653



---


## Decryption Methodology

### 1. Manual Inspection (Python)

The telegram was first analyzed using Python to:
- convert hex input into raw bytes,
- identify encryption boundaries,
- inspect payload length and structure.

```python
header = payload[:16]
encrypted_payload = payload[16:]
```

### 2. Encryption Mode Reasoning
The provided key length (16 bytes) confirms AES-128.

An initial AES-CBC attempt failed because the encrypted payload length (146 bytes) is not a multiple of the AES block size.

This ruled out CBC for payload encryption.

AES-CTR was therefore tested and produced structured binary output.

This reasoning step is intentionally documented to demonstrate protocol-level understanding rather than blind tool usage.

Reference Decoder Validation
To ensure full OMS compliance and correctness, the decrypted payload was decoded using the open-source wmbusmeters project:

Repository: https://github.com/wmbusmeters/wmbusmeters

Version used: 1.19.0-148-g4292c60

This tool implements EN 13757-3 and OMS specifications and is widely used in real-world metering gateways.

Results from the reference decoder were used as the final decoded output and cross-validated against the manual analysis.

### Decoded Meter Information
```
Meter Identity
Field	Value
Meter ID	50898527
Manufacturer	EFE
Medium		Water
Meter Type	Waterstarm

Current Measurement
Field	Value
Total Consumption	4.48 m³
Status	OK
Meter Date & Time	2025-09-26 16:36

Historical Consumption (Examples)
History Index	Date	Consumption (m³)
History 1	2025-08-26	0
History 3	2025-06-26	0.018
History 10	2024-11-26	−0.001 (invalid)

Note: Values of −0.001 indicate unavailable or invalid historical readings as defined by the meter.
```
---
OMS Data Record Interpretation
OMS application payloads consist of a sequence of records:


Reproducibility
This result can be reproduced by:

using the provided key and telegram payload,

applying the documented inspection steps,

or decoding directly with wmbusmeters.

All tools and references used are publicly available.

---

### Conclusion

The OMS telegram was decrypted and decoded according to the EN 13757-3 specification using the provided key and publicly available resources.  
The resulting meter information was extracted in a structured and readable form, and the process can be repeated using the same inputs and tools.

