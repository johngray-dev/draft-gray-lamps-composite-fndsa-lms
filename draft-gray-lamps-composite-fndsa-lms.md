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

informative:

...

--- abstract

This document defines a composite signature scheme combining the FN-DSA (Falcon) digital signature algorithm with the Leighton-Micali Signature (LMS) scheme defined in RFC 8554. This construction is designed for use within X.509 Public Key Infrastructure (PKI) and follows the composite signature paradigm defined in [I-D.ietf-lamps-pq-composite-sigs].


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


# Security Considerations

TODO Security


# IANA Considerations

This document has no IANA actions.


--- back

# Acknowledgments
{:numbered="false"}

TODO acknowledge.
