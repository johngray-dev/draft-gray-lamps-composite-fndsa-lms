---
title: "Composite FN-DSA and LMS Digital Signature Algorithm for use in X.509 Public Key Infrastructure"
abbrev: "composite-fndsa-lms"
category: std

docname: draft-gray-lamps-composite-fndsa-lms-latest
submissiontype: IETF  # also: "independent", "editorial", "IAB", or "IRTF"
number:
date:
consensus: true
v: 3
area: "Security"
workgroup: "Limited Additional Mechanisms for PKIX and SMIME"
keyword:
 - composite
 - fn-dsa
 - lms
venue:
  group: "Limited Additional Mechanisms for PKIX and SMIME"
  type: "Working Group"
  mail: "spasm@ietf.org"
  arch: "https://mailarchive.ietf.org/arch/browse/spasm/"
  github: "johngray-dev/draft-gray-lamps-composite-fndsa-lms"
  latest: "https://johngray-dev.github.io/draft-gray-lamps-composite-fndsa-lms/draft-gray-lamps-composite-fndsa-lms.html"

author:
 -
    fullname: "John Gray"
    organization: Entrust
    email: john.gray@entrust.com
    street: 2500 Solandt Road – Suite 100
    city: Ottawa, Ontario
    country: Canada
    code: K2K 3G5
 -
    ins: J.P Fiset
    name: Jean-Pierre Fiset
    org: Crypto4a
    email: jp@crypto4a.com
    country: Canada

normative:
  I-D.ietf-lamps-pq-composite-sigs:
  RFC8554:
  RFC5280:

informative:
  RFC9794:

...

--- abstract

This document defines a composite signature scheme combining the FN-DSA (Falcon) digital signature algorithm with the Leighton-Micali Signature (LMS) scheme defined in RFC 8554. This construction is designed for use within X.509 Public Key Infrastructure (PKI) and follows the composite signature paradigm defined in [I-D.ietf-lamps-pq-composite-sigs].

Composite FN-DSA-LMS combines a stateless lattice-based signature scheme with a stateful hash-based scheme to provide hybrid security properties.

--- middle

# Introduction

Post-Quantum Cryptography (PQC) introduces new cryptographic primitives that may require operational experience and hardening prior to full deployment. Hybrid mechanisms enable combining multiple algorithms such that security is preserved if at least one remains secure.

This document defines a composite signature scheme combining:

- FN-DSA (Falcon), a lattice-based signature algorithm
- LMS, a stateful hash-based signature algorithm [RFC8554]

The composite construction presents a single algorithm interface while internally invoking both primitives.

This specification follows the composite design framework described in [I-D.ietf-lamps-pq-composite-sigs].

# Conventions and Definitions

{::boilerplate bcp14-tagged}


# Overview of the Composite FN-DSA-LMS Scheme

Composite FN-DSA-LMS is a hybrid signature scheme formed by combining FN-DSA and LMS.

The construction follows the composite signature combiner approach:

```
M' := Prefix || Label || len(ctx) || ctx || PH(M)
```

Both component algorithms independently sign `M'`.

## Pre-hashing

Composite FN-DSA-LMS uses a pre-hash function PH:

PH(M)

This is incorporated into the message representative:

```
M' := Prefix || Label || len(ctx) || ctx || PH(M)
```

## Prefix, Label, and Context

Prefix:

"CompositeAlgorithmSignatures2025"

Label:

Unique per algorithm OID.

ctx:

Application-defined context (0–255 bytes).

# Composite Functions

## Key Generation

Composite-FNDSA-LMS.KeyGen() -> (pk, sk)

Steps:

1. Generate component keys:

(fndsaPK, fndsaSK) = FNDSA.KeyGen()
(lmsPK, lmsSK)     = LMS.KeyGen()

2. Output:

pk = SerializePublicKey(fndsaPK, lmsPK)
sk = SerializePrivateKey(fndsaSK, lmsSK)

## Sign

Composite-FNDSA-LMS.Sign(sk, M, ctx) -> s

Steps:

1. Check:
   if len(ctx) > 255: error

2. Compute:
   M' := Prefix || Label || len(ctx) || ctx || PH(M)

3. Deserialize keys:
  (fndsaSK, lmsSK) = DeserializePrivateKey(sk)

4. Sign:
  fndsaSig = FNDSA.Sign(fndsaSK, M')
  lmsSig   = LMS.Sign(lmsSK, M')

5. Output:
  s = SerializeSignatureValue(fndsaSig, lmsSig)

---

## Verify

Composite-FNDSA-LMS.Verify(pk, M, s, ctx) -> boolean

Steps:

1. Deserialize:
   (fndsaPK, lmsPK) = DeserializePublicKey(pk)
   (fndsaSig, lmsSig) = DeserializeSignatureValue(s)

2. Compute M'

3. Verify:
  FNDSA.Verify(fndsaPK, M', fndsaSig)
  LMS.Verify(lmsPK, M', lmsSig)

Both MUST succeed.

## Serialization

### Public Key

SerializePublicKey(fndsaPK, lmsPK):
return fndsaPK || lmsPK

### Private Key

SerializePrivateKey(fndsaSK, lmsSK):
return fndsaSK || lmsSK

### Signature
LMS signatures are variable length. Parsing relies on the fixed size of the FN-DSA signature.

SerializeSignatureValue(fndsaSig, lmsSig):
return fndsaSig || lmsSig

# Use within X.509 and PKIX

Composite FN-DSA-LMS is used identically to other composite algorithms.

- Public key encoded as BIT STRING
- Signature encoded as BIT STRING
- Raw serialized values used without ASN.1 wrapping

# Algorithm Identifiers

## id-FNDSA512-LMS-SHA256

- Label: `COMPSIG-FNDSA512-LMS-SHA256`
- PH: SHA256
- FN-DSA: FN-DSA-512
- LMS: LMS_SHA256_M32_H10

## id-FNDSA1024-LMS-SHA512

- Label: `COMPSIG-FNDSA1024-LMS-SHA512`
- PH: SHA512
- FN-DSA: FN-DSA-1024
- LMS: LMS_SHA256_M32_H15

TODO:  Define combinations here.


# Security Considerations

## LMS Statefulness Requirement

LMS private keys are stateful.

Each invocation of LMS.Sign MUST use a unique leaf index. Reuse of a leaf index results in catastrophic loss of security.

## Hybrid Security

Composite FN-DSA-LMS is EUF-CMA secure if at least one component remains secure.

## SUF-CMA

Composite FN-DSA-LMS is NOT SUF-CMA secure.

## Key Reuse

Component keys MUST NOT be reused between:

- composite vs standalone
- multiple composites


# IANA Considerations

IANA is requested to assign OIDs under:

1.3.6.1.5.5.7.6

TODO for each combination


--- back

# Acknowledgments
{:numbered="false"}

TODO acknowledge.
