# Authentication Methods
![[802.1X Authentication Process.png]]
### Open Authentication
- Client sends authentication request, AP accepts it; no challenge
	- Can be combined with other methods to add onto the authentication process (ex. connecting to a Starbuck's AP, then being taken to a web portal to connect to the Internet)
### WEP (Wired Equivalent Privacy)
- Used for both authentication and encryption (RC4)
	- Original 802.11 standard
		- BAD, INSECURE
- Process:
	1. AP sends challenge phrase to client
	2. Client encrypts challenge phrase with WEP key and sends it back
	3. AP compares the encrypted phrase to the encrypted phrase it calculated to see if they match
### EAP (Extensible Authentication Protocol)
- Authentication framework that defines a standard set of authentication functions to be used by EAP Methods
	- Such methods are LEAP, EAP-FAST, PEAP, and EAP-TLS
- Integrated with 802.1X, which provides port-based network access control (NAC)
### LEAP (Lightweight EAP)
- Improvement over WEP
- Clients must provide username and password
	- Additionally, mutual authentication is provided by both the client and server sending a challenge phrase to each other
- Dynamic WEP keys are used, meaning that the WEP keys are changed frequently
- **Considered vulnerable, do not use**
### EAP-FAST (EAP Flexible Authentication via Secure Tunneling)
- Consists of three phases:
	1. A PAC (Protected Access Credential) is generated and passed from the server to the client (this is used to create the TLS tunnel in step 2)
	2. A secure TLS tunnel is established between the client and authentication server
	3. The client and server complete authentication and authorization within the tunnel
### PEAP (Protected EAP)
- Also uses a TLS tunnel, but uses a digital certificate on the server
	1. The server sends a digital certificate to clients
	2. The client uses the digital certificate to establish a secure TLS tunnel itself and the authentication server
	3. The client then authenticates within the TLS tunnel using something like MS-CHAP (Microsoft Challenge-Handshake Authentication Protocol)
### EAP-TLS (EAP Transport Layer Security)
- Requires the server and client to have a certificate
- Most secure, but has the most overhead
	1. Client and server mutually authenticate with each other's digital certificates
	2. The TLS tunnel is established in order to exchange encryption key info
# Encryption and Integrity Methods
### TKIP (Temporal Key Integrity Protocol)
- Temporary solution that was implemented when WEP was being replaced
	- Thus, it's basically more secure WEP
- Used with WPA
### CCMP (Counter/CBC-MAC Protocol)
- More secure than TKIP
- Used with WPA2
- Uses AES-CTR for encryption
- Uses CBC-MAC for integrity (MIC)
### GCMP (Galois/Counter Mode Protocol)
- More secure and efficient than CCMP
- Used with WPA3
- Uses AES-CTR for encryption
- Uses GMAC (Galois Message Authentication Code) for integrity (MIC)
# Wi-Fi Security Standards
- All WPA modes support PSK and Enterprise mode
### WPA
- TKIP
- 802.1X authentication or PSK
### WPA2
- CCMP
- 802.1X authentication or PSK
### WPA3
- GCMP
- 802.1X authentication or PSK
- Additional security features:
	- PMF (Protected Management Frames): protects 802.11 management frames from eavesdropping/forging
	- SAE (Simultaneous Authentication of Equals): protects the 4-way handshake during PSK authentication
	- Forward Secrecy: prevents data from being decrypted after it has been transmitted, so attackers can't capture wireless frames and then try to decrypt them later
