# Basics
- Radio Frequency Identification
```mermaid
flowchart TD
	A[RFID System] --> B[RFID Reader]
	A --> C[RFID Tags]
	C --> D[Active Tags]
	C --> E[Passive Tags]
	C --> F[Semi Passive Tags]
```
### Active Tags
- Have their own power suply
- Are bulky
### Passive Tags
- Relies on the radio frequeny from the RFID reader to generate power using electromagnetic coupling to transmit the feedback signal.
- Range is less
- More prominent
### Semi Passive Tags
- Have their own power suply.
- For sending the feedback signal, they rely on radio frequency from the RFID tags.
```mermaid
flowchart TD
	A[Frequency of Opertaion] --> B["LF (LOW Frequeny)\n125khz to 134khz\nRange:10cm"]
	A --> C["HF (HIGH Frequency)\n 13.56MHz\nRange:1m"]
	A --> D["UHF (Ultra High Frequency)\n890MHz to 960MHz\nRange:10m-15m"]
```

