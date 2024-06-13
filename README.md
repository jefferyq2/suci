[![Build Status](https://api.cirrus-ci.com/github/discreteworks/suci.svg)](https://cirrus-ci.com/github/discreteworks/suci)

# 5G SUCI computation
In order to secure mobile subscriber identity 5G has introduced subscriber identity concealment (SUCI). There two mechanism to compute SUCI
 - USIM 
    - Computation of SUCI on USIM requires crypto support within USIM.
 - UE
    - For UE we can use available crypto library to compute SUCI.

The mechanism of computing SUCI on UE is given in 3gpp document <a href= "https://portal.3gpp.org/desktopmodules/Specifications/SpecificationDetails.aspx?specificationId=3169" target="_blank">33.501</a> section "C.3 Elliptic Curve Integrated Encryption Scheme (ECIES)".

Following diagram is taken from the same document.

<img src="https://discreteworks.com/assets/img/encrypt.png" class="figure-img img-fluid rounded" alt="...">

Suci computation generally share inputs method like public key provided by the operator a set of ephemeral private and public keys generated by UE for subscriber.
KDF, HMAC and AES 256 are shared for both the profiles.
Generation of key and encoding is divided into profiles:
- Profile A 
    - Utilizes  Curve25519 for ephemeral keys generation and shared secret.
- Profile B
    - Uses a compressed public key. Utilizes secp256r1 for ephemeral keys generation and shared secret.

Details of profile A/B is given in C.3.4.1 and C.3.4.2 respectively.

To compute SUCI we need USIM that include DF.5GS within ADF.USIM. Service table is enabled for 124 service. There is an interesting gist shared on this topic <a href="https://gist.github.com/mrlnc/01d6300f1904f154d969ff205136b753" target="_blank">pysim-SUCI.md</a>.

So basically you need a SIM that support 5G and has public key and profiles pre provisioned / self provisioned in EF SUCI_Calc_Info 3gpp <a href= "https://portal.3gpp.org/desktopmodules/Specifications/SpecificationDetails.aspx?specificationId=1803" target="_blank">31.102</a>

![31.102, EF SUCI_Calc_Info](https://discreteworks.com/assets/img/suci_calc_info.png)

## wolfSSL build instructions
```
cd wolfSSL
./autogen.sh
./configure --enable-curve25519 --enable-eccencrypt --enable-aesctr --enable-x963kdf --enable-compkey
make -j$(nproc)
sudo make install
```

## SUCI computation on UE build and run instructions
```
cd suci
mkdir build
cd build
cmake ..
make -j$(nproc)

# for dynamic linking
export LD_LIBRARY_PATH="${LD_LIBRARY_PATH}:/usr/local/lib"

# run
./suci

```

## Docker
```
docker run -ti munfasil/wolfssl /bin/bash
git clone https://github.com/discreteworks/suci.git
cd suci
mkdir build
cd build
cmake ..
make -j$(nproc)
```
