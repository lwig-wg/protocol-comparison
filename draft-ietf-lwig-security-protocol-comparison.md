---
title: Comparison of CoAP Security Protocols
docname: draft-ietf-lwig-security-protocol-comparison-latest

ipr: trust200902
wg: LWIG Working Group
cat: info

coding: utf-8
pi: # can use array (if all yes) or hash here
  toc: yes
  sortrefs: yes
  symrefs: yes
  tocdepth: 2

author:
      -
        ins: J. Mattsson
        name: John Mattsson
        org: Ericsson AB
        email: john.mattsson@ericsson.com
      -
        ins: F. Palombini
        name: Francesca Palombini
        org: Ericsson AB
        email: francesca.palombini@ericsson.com

informative:

  I-D.ietf-core-object-security:
  I-D.ietf-tls-dtls13:
  I-D.ietf-tls-dtls-connection-id:
  I-D.selander-ace-cose-ecdhe:

  RFC5246:
  RFC6347:
  RFC7400:
  RFC7252:
  RFC7925:
  RFC8323:
  RFC8446:

  OlegHahm-ghc:
    target: https://github.com/OlegHahm/ghc
    title: Generic Header Compression
    author:
      -
        ins: O. Hahm
    date: July 2016

--- abstract

This document analyzes and compares handshake and per-packet message size overheads when using different security protocols to secure CoAP. The analyzed security protocols are DTLS 1.2, DTLS 1.3, TLS 1.2, TLS 1.3, EDHOC and OSCORE. DTLS and TLS are analyzed with and without 6LoWPAN-GHC compression. DTLS is analyzed with and without Connection ID.

--- middle

# Introduction

This document analyzes and compares handshake and per-packet message size overheads when using different security protocols to secure CoAP over UPD {{RFC7252}} and TCP {{RFC8323}}. The analyzed security protocols are DTLS 1.2 {{RFC6347}}, DTLS 1.3 {{I-D.ietf-tls-dtls13}}, TLS 1.2 {{RFC5246}}, TLS 1.3 {{RFC8446}}, EDHOC {{I-D.selander-ace-cose-ecdhe}}, and OSCORE {{I-D.ietf-core-object-security}}. {{handshake}} compares the handshake overhead for the protocols considered, while {{record}} covers the overhead for application data. or record layer.

The DTLS and TLS record layers are analyzed with and without compression. DTLS is anlyzed with and without Connection ID {{I-D.ietf-tls-dtls-connection-id}}. Readers are expected to be familiar with some of the terms described in RFC 7925 {{RFC7925}}, such as ICV.


# Overhead of Handshake Protocols {#handshake}

In this section we present the overhead of the handshake for different protocols.

To enable a fair comparison between protocols with similar outcome, a number of assumptions are made for each protocol. These assumptions, which have an effect on the total number, are:

* All the overhead calculations in this section use AES-CCM with a tag length of 8 bytes (e.g.  AES_128_CCM_8 or AES-CCM-16-64).
* A minimum number of algorithms and cipher suites is offered during the handshake. The algorithm used/offered are Curve25519, ECDSA with P-256, AES-CCM_8, SHA-256.
* The length of key identifiers for EDHOC is 4 bytes.
* The length of connection identifiers for DTLS and TLS is 1 byte.
* DTLS RPK makes use of point compression, which saves 32 bytes.
* DTLS handshake message fragmentation is not considered.
* Only the DTLS mandatory extensions are considered, except for Connection ID.

## Overhead with Different Parameters

{{fig-compare1}} compares the message sizes of EDHOC {{I-D.selander-ace-cose-ecdhe}} with the DTLS 1.3 {{I-D.ietf-tls-dtls13}} and TLS 1.3 {{RFC8446}} handshakes with connection ID.

~~~~~~~~~~~~~~~~~~~~~~~
=====================================================================
Flight                             #1         #2        #3      Total
---------------------------------------------------------------------
DTLS 1.3 RPK + ECDHE              150        373       213        736
DTLS 1.3 PSK + ECDHE              187        190        57        434
DTLS 1.3 PSK                      137        150        57        344
---------------------------------------------------------------------
EDHOC RPK + ECDHE                  39        120        85        244
EDHOC PSK + ECDHE                  44         46        11        101
=====================================================================
~~~~~~~~~~~~~~~~~~~~~~~
{: #fig-compare1 title="Comparison of message sizes in bytes with Connection ID" artwork-align="center"}

{{fig-compare2}} compares of message sizes of EDHOC with the DTLS 1.3 {{I-D.ietf-tls-dtls13}} and TLS 1.3 {{RFC8446}} handshakes without connection ID.

~~~~~~~~~~~~~~~~~~~~~~~
=====================================================================
Flight                             #1         #2        #3      Total
---------------------------------------------------------------------
DTLS 1.3 RPK + ECDHE              144        364       212        722
DTLS 1.3 PSK + ECDHE              181        183        56        420
DTLS 1.3 PSK                      131        143        56        330
---------------------------------------------------------------------
TLS 1.3  RPK + ECDHE              129        322       194        645
TLS 1.3  PSK + ECDHE              166        157        50        373
TLS 1.3  PSK                      116        117        50        283
---------------------------------------------------------------------
EDHOC RPK + ECDHE                  38        119        84        241
EDHOC PSK + ECDHE                  44         45        10         98
=====================================================================
~~~~~~~~~~~~~~~~~~~~~~~
{: #fig-compare2 title="Comparison of message sizes in bytes without Connection ID" artwork-align="center"}

The details of the message size calculations are given in the following sections.

## DTLS 1.3

## TLS 1.3

## EDHOC


This appendix gives an estimate of the message sizes of EDHOC with different authentication methods. Note that the examples in this appendix are not test vectors, the cryptographic parts are just replaced with byte strings of the same length. All examples are given in CBOR diagnostic notation and hexadecimal.

### Message Sizes RPK

#### message_1

~~~~~~~~~~~~~~~~~~~~~~~
message_1 = (
  1,
  h'c3',
  0,
  0,
  h'000102030405060708090a0b0c0d0e0f101112131415161718191a1b1c1d
    1e1f'
)
~~~~~~~~~~~~~~~~~~~~~~~

~~~~~~~~~~~~~~~~~~~~~~~
message_1 (39 bytes):
01 41 C3 00 00 58 20 00 01 02 03 04 05 06 07 08 09 0A 0B 0C
0D 0E 0F 10 11 12 13 14 15 16 17 18 19 1A 1B 1C 1D 1E 1F
~~~~~~~~~~~~~~~~~~~~~~~

#### message_2

~~~~~~~~~~~~~~~~~~~~~~~
plaintext = <<
  { 4 : 'acdc' },
  h'000102030405060708090a0b0c0d0e0f101112131415161718191a1b1c1d
    1e1f202122232425262728292a2b2c2d2e2f303132333435363738393a3b
    3c3d3e3f'
>>
~~~~~~~~~~~~~~~~~~~~~~~

The protected header map is 7 bytes. The length of plaintext is 73 bytes so assuming a 64-bit MAC value the length of ciphertext is 81 bytes.

~~~~~~~~~~~~~~~~~~~~~~~
message_2 = (
  null,
  h'c4',
  h'000102030405060708090a0b0c0d0e0f101112131415161718191a1b1c1d
    1e1f',
  h'000102030405060708090a0b0c0d0e0f101112131415161718191a1b1c1d
    1e1f202122232425262728292a2b2c2d2e2f303132333435363738393a3b
    3c3d3e3f404142434445464748494a4b4c4d4e4f50'
)
~~~~~~~~~~~~~~~~~~~~~~~

~~~~~~~~~~~~~~~~~~~~~~~
message_2 (120 bytes):
F6 41 C4 58 20 00 01 02 03 04 05 06 07 08 09 0A 0B 0C 0D 0E
0F 10 11 12 13 14 15 16 17 18 19 1A 1B 1C 1D 1E 1F 58 51 00
01 02 03 04 05 06 07 08 09 0A 0B 0C 0D 0E 0F 10 11 12 13 14
15 16 17 18 19 1A 1B 1C 1D 1E 1F 20 21 22 23 24 25 26 27 28
29 2A 2B 2C 2D 2E 2F 30 31 32 33 34 35 36 37 38 39 3A 3B 3C
3D 3E 3F 40 41 42 43 44 45 46 47 48 49 4A 4B 4C 4D 4E 4F 50
~~~~~~~~~~~~~~~~~~~~~~~

#### message_3

The plaintext and ciphertext in message_3 are assumed to be of equal sizes as in message_2.

~~~~~~~~~~~~~~~~~~~~~~~
message_3 = (
  h'c3',
  h'000102030405060708090a0b0c0d0e0f101112131415161718191a1b1c1d
    1e1f202122232425262728292a2b2c2d2e2f303132333435363738393a3b
    3c3d3e3f404142434445464748494a4b4c4d4e4f50'
)
~~~~~~~~~~~~~~~~~~~~~~~

~~~~~~~~~~~~~~~~~~~~~~~
message_3 (85 bytes):
41 C3 58 51 00 01 02 03 04 05 06 07 08 09 0A 0B 0C 0D 0E 0F
10 11 12 13 14 15 16 17 18 19 1A 1B 1C 1D 1E 1F 20 21 22 23
24 25 26 27 28 29 2A 2B 2C 2D 2E 2F 30 31 32 33 34 35 36 37
38 39 3A 3B 3C 3D 3E 3F 40 41 42 43 44 45 46 47 48 49 4A 4B
4C 4D 4E 4F 50
~~~~~~~~~~~~~~~~~~~~~~~

### Message Sizes Certificates

When the certificates are distributed out-of-band and identified with the x5t header and a SHA256/64 hash value, the protected header map will be 13 bytes instead of 7 bytes (assuming labels in the range -24&hellip;23).

~~~~~~~~~~~~~~~~~~~~~~~
protected = << { TDB1 : [ TDB6, h'0001020304050607' ] } >>
~~~~~~~~~~~~~~~~~~~~~~~

When the certificates are identified with the x5chain header, the message sizes depends on the size of the (truncated) certificate chains. The protected header map will be 3 bytes + the size of the certificate chain (assuming a label in the range -24&hellip;23).

~~~~~~~~~~~~~~~~~~~~~~~
protected = << { TDB3 : h'0001020304050607...' } >>
~~~~~~~~~~~~~~~~~~~~~~~

### Message Sizes PSK

#### message_1

~~~~~~~~~~~~~~~~~~~~~~~
message_1 = (
  2,
  h'c3',
  0,
  0,
  h'000102030405060708090a0b0c0d0e0f101112131415161718191a1b1c1d
    1e1f',
  'abba'
)
~~~~~~~~~~~~~~~~~~~~~~~

~~~~~~~~~~~~~~~~~~~~~~~
message_1 (44 bytes):
02 41 C3 00 00 58 20 00 01 02 03 04 05 06 07 08 09 0A 0B 0C
0D 0E 0F 10 11 12 13 14 15 16 17 18 19 1A 1B 1C 1D 1E 1F
44 61 63 64 63
~~~~~~~~~~~~~~~~~~~~~~~

#### message_2

Assuming a 0 byte plaintext and a 64-bit MAC value the ciphertext is 8 bytes

~~~~~~~~~~~~~~~~~~~~~~~
message_2 = (
  null,
  h'c4',
  h'000102030405060708090a0b0c0d0e0f101112131415161718191a1b1c1d
    1e1f',
  h'0001020304050607'
)
~~~~~~~~~~~~~~~~~~~~~~~

~~~~~~~~~~~~~~~~~~~~~~~
message_2 (46 bytes):
F6 41 C4 58 20 00 01 02 03 04 05 06 07 08 09 0A 0B 0C 0D 0E
0F 10 11 12 13 14 15 16 17 18 19 1A 1B 1C 1D 1E 1F 48 61 62
63 64 65 66 67 68
~~~~~~~~~~~~~~~~~~~~~~~

#### message_3

The plaintext and ciphertext in message_3 are assumed to be of equal sizes as in message_2.

~~~~~~~~~~~~~~~~~~~~~~~
message_3 = (
  h'c3',
  h'0001020304050607'
)
~~~~~~~~~~~~~~~~~~~~~~~

~~~~~~~~~~~~~~~~~~~~~~~
message_3 (11 bytes):
41 C3 48 00 01 02 03 04 05 06 07
~~~~~~~~~~~~~~~~~~~~~~~

### Summary

The previous estimates of typical message sizes are summarized in {{fig-summary}}.

~~~~~~~~~~~~~~~~~~~~~~~
=====================================================================
                PSK       RPK       x5t     x5chain
---------------------------------------------------------------------
message_1       44        39        39        39
message_2       46       120       126       116 + Certificate chain
message_3       11        85        91        81 + Certificate chain
---------------------------------------------------------------------
Total          101       244       256       236 + Certificate chains
=====================================================================
~~~~~~~~~~~~~~~~~~~~~~~
{: #fig-summary title="Typical message sizes in bytes" artwork-align="center"}

# Overhead for Protection of Application Data {#record}

To enable comparison, all the overhead calculations in this section use AES-CCM with a tag length of 8 bytes (e.g.  AES_128_CCM_8 or AES-CCM-16-64), a plaintext of 6 bytes, and the sequence number ‘05’. This follows the example in {{RFC7400}}, Figure 16.

Note that the compressed overhead calculations for DLTS 1.2, DTLS 1.3, TLS 1.2 and TLS 1.3 are dependent on the parameters epoch, sequence number, and length, and all the overhead calculations are dependent on the parameter Connection ID when used. Note that the OSCORE overhead calculations are dependent on the CoAP option numbers, as well as the length of the OSCORE parameters Sender ID and Sequence Number. The following are only examples.

## DTLS 1.2

### DTLS 1.2

This section analyzes the overhead of DTLS 1.2 {{RFC6347}}. The nonce follow the strict profiling given in {{RFC7925}}.  This example is taken directly from {{RFC7400}}, Figure 16.

~~~~~~~~~~~
DTLS 1.2 record layer (35 bytes, 29 bytes overhead):
17 fe fd 00 01 00 00 00 00 00 05 00 16 00 01 00
00 00 00 00 05 ae a0 15 56 67 92 4d ff 8a 24 e4
cb 35 b9

Content type:
17
Version:
fe fd
Epoch:
00 01
Sequence number:
00 00 00 00 00 05
Length:
00 16
Nonce:
00 01 00 00 00 00 00 05
Ciphertext:
ae a0 15 56 67 92
ICV:
4d ff 8a 24 e4 cb 35 b9
~~~~~~~~~~~

DTLS 1.2 gives 29 bytes overhead.

### DTLS 1.2 with 6LoWPAN-GHC

This section analyzes the overhead of DTLS 1.2 {{RFC6347}} when compressed with 6LoWPAN-GHC {{RFC7400}}. The compression was done with {{OlegHahm-ghc}}.

Note that the sequence number ‘01’ used in {{RFC7400}}, Figure 15 gives an exceptionally small overhead that is not representative.

Note that this header compression is not available when DTLS is used over transports that do not use 6LoWPAN together with 6LoWPAN-GHC.

~~~~~~~~~~~
Compressed DTLS 1.2 record layer (22 bytes, 16 bytes overhead):
b0 c3 03 05 00 16 f2 0e ae a0 15 56 67 92 4d ff
8a 24 e4 cb 35 b9

Compressed DTLS 1.2 record layer header and nonce:
b0 c3 03 05 00 16 f2 0e
Ciphertext:
ae a0 15 56 67 92
ICV:
4d ff 8a 24 e4 cb 35 b9
~~~~~~~~~~~

When compressed with 6LoWPAN-GHC, DTLS 1.2 with the above parameters (epoch, sequence number, length) gives 16 bytes overhead.

### DTLS 1.2 with Connection ID

This section analyzes the overhead of DTLS 1.2  {{RFC6347}} with Connection ID {{I-D.ietf-tls-dtls-connection-id}}. The overhead calculations in this section uses Connection ID = '42'. DTLS recored layer with a Connection ID = '' (the empty string) is equal to DTLS without Connection ID.

~~~~~~~~~~~
DTLS 1.2 record layer (36 bytes, 30 bytes overhead):
17 fe fd 00 01 00 00 00 00 00 05 42 00 16 00 01
00 00 00 00 00 05 ae a0 15 56 67 92 4d ff 8a 24
e4 cb 35 b9

Content type:
17
Version:
fe fd
Epoch:
00 01
Sequence number:
00 00 00 00 00 05
Connection ID:
42
Length:
00 16
Nonce:
00 01 00 00 00 00 00 05
Ciphertext:
ae a0 15 56 67 92
ICV:
4d ff 8a 24 e4 cb 35 b9
~~~~~~~~~~~

DTLS 1.2 with Connection ID gives 30 bytes overhead.

### DTLS 1.2 with Connection ID and 6LoWPAN-GHC

This section analyzes the overhead of DTLS 1.2 {{RFC6347}} with Connection ID {{I-D.ietf-tls-dtls-connection-id}} when compressed with 6LoWPAN-GHC {{RFC7400}} {{OlegHahm-ghc}}.

Note that the sequence number ‘01’ used in {{RFC7400}}, Figure 15 gives an exceptionally small overhead that is not representative.

Note that this header compression is not available when DTLS is used over transports that do not use 6LoWPAN together with 6LoWPAN-GHC.

~~~~~~~~~~~
Compressed DTLS 1.2 record layer (23 bytes, 17 bytes overhead):
b0 c3 04 05 42 00 16 f2 0e ae a0 15 56 67 92 4d
ff 8a 24 e4 cb 35 b9

Compressed DTLS 1.2 record layer header and nonce:
b0 c3 04 05 42 00 16 f2 0e
Ciphertext:
ae a0 15 56 67 92
ICV:
4d ff 8a 24 e4 cb 35 b9
~~~~~~~~~~~

When compressed with 6LoWPAN-GHC, DTLS 1.2 with the above parameters (epoch, sequence number, Connection ID, length) gives 17 bytes overhead.

## DTLS 1.3

### DTLS 1.3

This section analyzes the overhead of DTLS 1.3 {{I-D.ietf-tls-dtls13}}. The changes compared to DTLS 1.2 are: omission of version number, merging of epoch into the first byte containing signalling bits, optional omission of length, reduction of sequence number into a 1 or 2-bytes field.

In this example, the length field is omitted, and the 1-byte field is used for the sequence number. The minimal DTLSCiphertext structure is used (see Figure 4 of {{I-D.ietf-tls-dtls13}}).

~~~~~~~~~~~
DTLS 1.3 record layer (17 bytes, 11 bytes overhead):
21 05 ae a0 15 56 67 92 ec 4d ff 8a 24 e4 cb 35 b9

First byte (including epoch):
21
Sequence number:
05
Ciphertext (including encrypted content type):
ae a0 15 56 67 92 ec
ICV:
4d ff 8a 24 e4 cb 35 b9
~~~~~~~~~~~

DTLS 1.3 gives 11 bytes overhead.

### DTLS 1.3 with 6LoWPAN-GHC

This section analyzes the overhead of DTLS 1.3 {{I-D.ietf-tls-dtls13}} when compressed with 6LoWPAN-GHC {{RFC7400}} {{OlegHahm-ghc}}.

Note that this header compression is not available when DTLS is used over transports that do not use 6LoWPAN together with 6LoWPAN-GHC.

~~~~~~~~~~~
Compressed DTLS 1.3 record layer (18 bytes, 12 bytes overhead):
11 21 05 ae a0 15 56 67 92 ec 4d ff 8a 24 e4 cb
35 b9

Compressed DTLS 1.3 record layer header and nonce:
11 21 05
Ciphertext (including encrypted content type):
ae a0 15 56 67 92 ec
ICV:
4d ff 8a 24 e4 cb 35 b9
~~~~~~~~~~~

When compressed with 6LoWPAN-GHC, DTLS 1.3 with the above parameters (epoch, sequence number, no length) gives 12 bytes overhead.

### DTLS 1.3 with Connection ID

This section analyzes the overhead of DTLS 1.3 {{I-D.ietf-tls-dtls13}} with Connection ID {{I-D.ietf-tls-dtls-connection-id}}.

In this example, the length field is omitted, and the 1-byte field is used for the sequence number. The minimal DTLSCiphertext structure is used (see Figure 4 of {{I-D.ietf-tls-dtls13}}), with the addition of the Connection ID field.

~~~~~~~~~~~
DTLS 1.3 record layer (18 bytes, 12 bytes overhead):
31 42 05 ae a0 15 56 67 92 ec 4d ff 8a 24 e4 cb 35 b9

First byte (including epoch):
31
Connection ID:
42
Sequence number:
05
Ciphertext (including encrypted content type):
ae a0 15 56 67 92 ec
ICV:
4d ff 8a 24 e4 cb 35 b9
~~~~~~~~~~~

DTLS 1.3 with Connection ID gives 12 bytes overhead.

### DTLS 1.3 with Connection ID and 6LoWPAN-GHC

This section analyzes the overhead of DTLS 1.3 {{I-D.ietf-tls-dtls13}} with Connection ID {{I-D.ietf-tls-dtls-connection-id}} when compressed with 6LoWPAN-GHC {{RFC7400}} {{OlegHahm-ghc}}.

Note that this header compression is not available when DTLS is used over transports that do not use 6LoWPAN together with 6LoWPAN-GHC.

~~~~~~~~~~~
Compressed DTLS 1.3 record layer (19 bytes, 13 bytes overhead):
12 31 05 42 ae a0 15 56 67 92 ec 4d ff 8a 24 e4
cb 35 b9

Compressed DTLS 1.3 record layer header and nonce:
12 31 05 42
Ciphertext (including encrypted content type):
ae a0 15 56 67 92 ec
ICV:
4d ff 8a 24 e4 cb 35 b9
~~~~~~~~~~~

When compressed with 6LoWPAN-GHC, DTLS 1.3 with the above parameters (epoch, sequence number, Connection ID, no length) gives 13 bytes overhead.

## TLS 1.2

### TLS 1.2

This section analyzes the overhead of TLS 1.2 {{RFC5246}}. The changes compared to DTLS 1.2 is that the TLS 1.2 record layer does not have epoch and sequence number, and that the version is different.

~~~~~~~~~~~
TLS 1.2 Record Layer (27 bytes, 21 bytes overhead):
17 03 03 00 16 00 00 00 00 00 00 00 05 ae a0 15
56 67 92 4d ff 8a 24 e4 cb 35 b9

Content type:
17
Version:
03 03
Length:
00 16
Nonce:
00 00 00 00 00 00 00 05
Ciphertext:
ae a0 15 56 67 92
ICV:
4d ff 8a 24 e4 cb 35 b9
~~~~~~~~~~~

TLS 1.2 gives 21 bytes overhead.

### TLS 1.2 with 6LoWPAN-GHC

This section analyzes the overhead of TLS 1.2 {{RFC5246}} when compressed with 6LoWPAN-GHC {{RFC7400}} {{OlegHahm-ghc}}.

Note that this header compression is not available when TLS is used over transports that do not use 6LoWPAN together with 6LoWPAN-GHC.

~~~~~~~~~~~
Compressed TLS 1.2 record layer (23 bytes, 17 bytes overhead):
05 17 03 03 00 16 85 0f 05 ae a0 15 56 67 92 4d
ff 8a 24 e4 cb 35 b9

Compressed TLS 1.2 record layer header and nonce:
05 17 03 03 00 16 85 0f 05
Ciphertext:
ae a0 15 56 67 92
ICV:
4d ff 8a 24 e4 cb 35 b9
~~~~~~~~~~~

When compressed with 6LoWPAN-GHC, TLS 1.2 with the above parameters (epoch, sequence number, length) gives 17 bytes overhead.

## TLS 1.3

### TLS 1.3

This section analyzes the overhead of TLS 1.3 {{RFC8446}}. The change compared to TLS 1.2 is that the TLS 1.3 record layer uses a different version.

~~~~~~~~~~~
TLS 1.3 Record Layer (20 bytes, 14 bytes overhead):
17 03 03 00 16 ae a0 15 56 67 92 ec 4d ff 8a 24
e4 cb 35 b9

Content type:
17
Legacy version:
03 03
Length:
00 0f
Ciphertext (including encrypted content type):
ae a0 15 56 67 92 ec
ICV:
4d ff 8a 24 e4 cb 35 b9
~~~~~~~~~~~

TLS 1.3 gives 14 bytes overhead.

### TLS 1.3 with 6LoWPAN-GHC

This section analyzes the overhead of TLS 1.3 {{RFC8446}} when compressed with 6LoWPAN-GHC {{RFC7400}} {{OlegHahm-ghc}}.

Note that this header compression is not available when TLS is used over transports that do not use 6LoWPAN together with 6LoWPAN-GHC.

~~~~~~~~~~~
Compressed TLS 1.3 record layer (21 bytes, 15 bytes overhead):
14 17 03 03 00 0f ae a0 15 56 67 92 ec 4d ff 8a
24 e4 cb 35 b9

Compressed TLS 1.3 record layer header and nonce:
14 17 03 03 00 0f
Ciphertext (including encrypted content type):
ae a0 15 56 67 92 ec
ICV:
4d ff 8a 24 e4 cb 35 b9
~~~~~~~~~~~

When compressed with 6LoWPAN-GHC, TLS 1.3 with the above parameters (epoch, sequence number, length) gives 15 bytes overhead.

## OSCORE

This section analyzes the overhead of OSCORE {{I-D.ietf-core-object-security}}.

The below calculation Option Delta = ‘9’, Sender ID = ‘’ (empty string), and Sequence Number = ‘05’, and is only an example. Note that Sender ID = ‘’ (empty string) can only be used by one client per server.

~~~~~~~~~~~
OSCORE request (19 bytes, 13 bytes overhead):
92 09 05
ff ec ae a0 15 56 67 92 4d ff 8a 24 e4 cb 35 b9

CoAP option delta and length:
92
Option value (flag byte and sequence number):
09 05
Payload marker:
ff
Ciphertext (including encrypted code):
ec ae a0 15 56 67 92
ICV:
4d ff 8a 24 e4 cb 35 b9
~~~~~~~~~~~

The below calculation Option Delta = ‘9’, Sender ID = ‘42’, and Sequence Number = ‘05’, and is only an example.

~~~~~~~~~~~
OSCORE request (20 bytes, 14 bytes overhead):
93 09 05 42
ff ec ae a0 15 56 67 92 4d ff 8a 24 e4 cb 35 b9

CoAP option delta and length:
93
Option Value (flag byte, sequence number, and Sender ID):
09 05 42
Payload marker:
ff
Ciphertext (including encrypted code):
ec ae a0 15 56 67 92
ICV:
4d ff 8a 24 e4 cb 35 b9
~~~~~~~~~~~

The below calculation uses Option Delta = ‘9’.

~~~~~~~~~~~
OSCORE response (17 bytes, 11 bytes overhead):
90
ff ec ae a0 15 56 67 92 4d ff 8a 24 e4 cb 35 b9

CoAP delta and option length:
90
Option value:
-
Payload marker:
ff
Ciphertext (including encrypted code):
ec ae a0 15 56 67 92
ICV:
4d ff 8a 24 e4 cb 35 b9
~~~~~~~~~~~

OSCORE with the above parameters gives 13-14 bytes overhead for requests and 11 bytes overhead for responses.

Unlike DTLS and TLS, OSCORE has much smaller overhead for responses than requests.

## Overhead with Different Parameters

The DTLS overhead is dependent on the parameter Connection ID. The following overheads apply for all Connection IDs with the same length.

The compression overhead (GHC) is dependent on the parameters epoch, sequence number, Connection ID, and length (where applicable). The following overheads should be representative for sequence numbers and Connection IDs with the same length.

The OSCORE overhead is dependent on the included CoAP Option numbers as well as the length of the OSCORE parameters Sender ID and sequence number. The following overheads apply for all sequence numbers and Sender IDs with the same length.

~~~~~~~~~~~
Sequence Number                '05'       '1005'     '100005'
-------------------------------------------------------------
DTLS 1.2                        29          29          29
DTLS 1.3                        11          12          12
-------------------------------------------------------------
DTLS 1.2 (GHC)                  16          16          16
DTLS 1.3 (GHC)                  12          13          13
-------------------------------------------------------------
TLS  1.2                        21          21          21
TLS  1.3                        14          14          14
-------------------------------------------------------------
TLS  1.2 (GHC)                  17          18          19
TLS  1.3 (GHC)                  15          16          17
-------------------------------------------------------------
OSCORE request                  13          14          15
OSCORE response                 11          11          11
~~~~~~~~~~~
{: #fig-overhead title="Overhead in bytes as a function of sequence number &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;(Connection/Sender ID = '')"}
{: artwork-align="center"}

~~~~~~~~~~~
Connection/Sender ID            ''         '42'       '4002'
-------------------------------------------------------------
DTLS 1.2                        29          30          31
DTLS 1.3                        11          12          13
-------------------------------------------------------------
DTLS 1.2 (GHC)                  16          17          18
DTLS 1.3 (GHC)                  12          13          14
-------------------------------------------------------------
OSCORE request                  13          14          15
OSCORE response                 11          11          11
~~~~~~~~~~~
{: #fig-overhead2 title="Overhead in bytes as a function of Connection/Sender ID &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;(Sequence Number = '05')"}
{: artwork-align="center"}

~~~~~~~~~~~
Protocol                     Overhead      Overhead (GHC)
-------------------------------------------------------------
DTLS 1.2                        21               8
DTLS 1.3                         3               4
-------------------------------------------------------------
TLS  1.2                        13               9
TLS  1.3                         6               7
-------------------------------------------------------------
OSCORE request                   5
OSCORE response                  3
~~~~~~~~~~~
{: #fig-overhead3 title="Overhead (excluding ICV) in bytes &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;(Connection/Sender ID = '', Sequence Number = '05')"}
{: artwork-align="center"}

## Summary

DTLS 1.2 has quite a large overhead as it uses an explicit sequence number and an explicit nonce. TLS 1.2 has significantly less (but not small) overhead. TLS 1.3 has quite a small overhead. OSCORE and DTLS 1.3 (using the minimal structure) format have very small overhead.

The Generic Header Compression (6LoWPAN-GHC) can in addition to DTLS 1.2 handle TLS 1.2, and DTLS 1.2 with Connection ID. The Generic Header Compression (6LoWPAN-GHC) works very well for Connection ID and the overhead seems to increase exactly with the length of the Connection ID (which is optimal). The compression of TLS 1.2 is not as good as the compression of DTLS 1.2 (as the static dictionary only contains the DTLS 1.2 version number). Similar compression levels as for DTLS could be achieved also for TLS 1.2, but this would require different static dictionaries. For TLS 1.3 and DTLS 1.3, GHC increases the overhead. The 6LoWPAN-GHC header compression is not available when (D)TLS is used over transports that do not use 6LoWPAN together with 6LoWPAN-GHC.

Only the minimal header format for DTLS 1.3 was considered, which reduces the header of 3 bytes compared to the full header, by omitting the 2-byte-long length value and sending 1 byte of sequence number instead of 2. This may create problems reconstructing the full sequence number, if ~ 2000 datagrams in sequence are lost.

OSCORE has much lower overhead than DTLS 1.2 and TLS 1.2. The overhead of OSCORE is smaller than DTLS 1.2 and TLS 1.2 over 6LoWPAN with compression, and this small overhead is achieved even on deployments without 6LoWPAN or 6LoWPAN without DTLS compression. OSCORE is lightweight because it makes use of CoAP, CBOR, and COSE, which were designed to have as low overhead as possible.

# Security Considerations

This document is purely informational.

# IANA Considerations

This document has no actions for IANA.

--- back

# Acknowledgments
{: numbered="no"}

The authors want to thank Ari Keränen, Carsten Bormann, Göran Selander, and Hannes Tschofenig for comments and suggestions on previous versions of the draft.

All 6LoWPAN-GHC compression was done with {{OlegHahm-ghc}}.

--- fluff
