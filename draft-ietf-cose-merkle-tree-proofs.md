---
v: 3

title: Concise Encoding of Signed Merkle Tree Proofs
abbrev: CoMETRE
docname: draft-ietf-cose-merkle-tree-proofs-latest
stand_alone: true
ipr: trust200902
area: Security
wg: TBD
kw: Internet-Draft
cat: std
submissiontype: IETF
pi:
  toc: yes
  sortrefs: yes
  symrefs: yes

author:
- ins: O. Steele
  name: Orie Steele
  organization: Transmute
  email: orie@transmute.industries
  country: United States
- ins: H. Birkholz
  name: Henk Birkholz
  org: Fraunhofer SIT
  abbrev: Fraunhofer SIT
  email: henk.birkholz@sit.fraunhofer.de
  street: Rheinstrasse 75
  code: '64295'
  city: Darmstadt
  country: Germany
- ins: A. Delignat-Lavaud
  name: Antoine Delignat-Lavaud
  organization: Microsoft
  email: antdl@microsoft.com
  country: UK
- ins: C. Fournet
  name: Cedric Fournet
  organization: Microsoft
  email: fournet@microsoft.com
  country: UK

normative:
  RFC8949:
  RFC6962: certificate-transparency-v1
  RFC9162: certificate-transparency-v2
  RFC6234:
  RFC8032:
  RFC6979:
  RFC7049: CBOR
  RFC9053: COSE
  RFC8126: iana-considerations-guide
  BCP205: RFC7942

informative:
  I-D.ietf-cose-countersign:
  I-D.ietf-scitt-architecture: scitt-architecture

--- abstract

This specification describes verifiable data structures and associated proof types for use with COSE.
The extensibility of the approach is demonstrated by providing CBOR encodings for RFC9162.

--- middle

# Introduction

Merkle trees are one of many verifiable data structures that enable tamper evident secure information storage,
through their ability to protect the integrity of batches of documents or collections of statements.

Merkle trees can be constructed from simple operations such as concatenation and digest via a cryptographic hash function,
however, more advanced constructions enable proofs of different properties of the underlying verifiable data structure.

Verifiable data structure proofs can be used to prove a document is in a database (proof of inclusion),
that a database is append only (proof of consistency), that a smaller set of statements are contained
in a large set of statements (proof of disclosure, a special case of proof of inclusion),
or proof that certain data is not yet present in a database (proofs of non inclusion).

Differences in the representation of verifiable data structures, and verifiable data structure proof types,
can increase the burden for implementers, and create interoperability challenges for transparency services.

This document describes how to convey verifiable data structures, and associated proof types in COSE envelopes.

## Requirements Notation

{::boilerplate bcp14-tagged}

# Terminology

Verifiable Data Structure:

: A data structure which supports one or more Proof Types.

Proof Type:

: A verifiable process, that proves properties of one or more Verifiable Data Structures.

Proof Value:

: An encoding of a Proof Type in CBOR.

Proof Signature:

: A COSE Sign1 encoding of a specific Proof Type for a specific Verifiable Data Structure.

# Verifiable Data Structures in CBOR {#sec-generic-verifiable-data-structures}

This section describes representations of verifiable data structure proofs structures in CBOR.

Different verifiable data structures support the same proof types,
but the representations of the proofs varies greatly.

For example, construction of a merkle tree leaf, or an inclusion proof from a leaf to a merkle root,
might have several different representations, depending on the verifiable data structure used.

Some differences in representations are necessary to support efficient
verification of different kinds of proofs and for compatibility with specific implementations.

Some proof types benefit from standard envelope formats for signing and encryption, whilst others require no further cryptographic intervention at all.

In order to improve interoperability we define two extension points for
enabling verifiable data structures with COSE, and we provide concrete examples for
the structures and proofs defined in {{-certificate-transparency-v2}}.

## Algorithms Registry {#sec-verifiable-data-structure-algorithms}

This document establishes a registry of verifiable data structure algorithms,
with the following initial contents:

| Identifier            | Algorithm | Reference
|---
|0 | N/A                |
|1 | RFC9162_SHA256     | {{-certificate-transparency-v2}}
{: #verifiable-data-structure-values align="left" title="Verifiable Data Structure Alogrithms"}

### Registration Requirements

Each specification MUST define how to encode the algorithm and proof types in CBOR.

Each specification MUST define how to produce and consume the supported proof types.

See {{sec-rfc-9162-verifiable-data-structure-definition}} as an example.

# Proof Types in CBOR

Proof types are specific to their associated "verifiable data structure",
for example, different Merkle trees might support different representations of "inclusion proof" or "consistency proof".

Implementers should not expect interoperability accross "verifiable data structures",
but they should expect conceptually similar properties across registered proof types.

For example, 2 different merkle tree based verifiable data structures might both support proofs of inclusion.
Protocols requiring proof of inclusion ought to be able to preserve their functionality,
while switching from one verifiable data structure to another, so long as both structures support the same proof types.

## Proof Types Registry {#sec-verifiable-data-structure-proof-types}

This document establishes a registry of verifiable data structure proof types tags,
with the following initial contents:

| Identifier  | Proof Type   | Proof Value | Reference
|---
|0            | N/A          | N/A | N/A
|TBD_2        | inclusion    | array of bstr | {{sec-generic-inclusion-proof}}
|TBD_3        | consistency  | array of bstr | {{sec-generic-consistency-proof}}
{: #verifiable-data-structure-proof-types-values align="left" title="Verifiable Data Structure Proof Types"}

## Inclusion Proof {#sec-generic-inclusion-proof}

Inclusion proofs provide a mechanism for a verifier to validate set membership.

The integer identifier for this Proof Type is TBD_2.
The string identifier for this Proof Type is "inclusion".
The value of this Proof Type is array of bstr.

{{sec-rfc9162-sha256-inclusion-proof}} provides a concrete example.

## Consistency Proof {#sec-generic-consistency-proof}

Consistency proofs provide a mechanism for a verifier to validate the consistency of a verifiable data structure.

The integer identifier for this Proof Type is TBD_3.
The string identifier for this Proof Type is "consistency".
The value of this Proof Type is array of bstr.

{{sec-rfc9162-sha256-consistency-proof}} provides a concrete example.

# RFC9162_SHA256 as a Verifiable Data Structure {#sec-rfc-9162-verifiable-data-structure-definition}

This section defines how the data structures described in {{-certificate-transparency-v2}}
are mapped to the terminology defined in this document, using cbor and cose.

RFC9162_SHA256 requires the following:

- TBD_1 (verifiable-data-structure): 1, the integer representing the RFC9162_SHA256 verifiable data structure algorithm.
- TBD_2 (inclusion-proof): an array of bstr representing RFC9162_SHA256 inclusion proofs
- TBD_3 (consistency-proof): an array of bstr representing RFC9162_SHA256 consistency proofs

## Algorithm Definition

The integer identifier for this Verifiable Data Structure is 1.
The string identifier for this Verifiable Data Structure is "RFC9162_SHA256".

See {{sec-verifiable-data-structure-algorithms}}.

See {{-certificate-transparency-v2}}, 2.1.1. Definition of the Merkle Tree,
for a complete description of this verifiable data structure.

## Inclusion Proof Definition {#sec-rfc9162-sha256-inclusion-proof}

See {{-certificate-transparency-v2}}, 2.1.3.1. Generating an Inclusion Proof,
for a complete description of this verifiable data structure proof type.

The cbor representation of an inclusion proof for RFC9162_SHA256 is:

~~~~ cddl
inclusion-proof = #TBD_2([
    tree-size: int
    leaf-index: int
    inclusion-path: [+ bstr]
])
~~~~

### Inclusion Proof Signature

In a signed inclusion proof, the previous merkle tree root, maps to tree-size-1, and is a detached payload.

Other specifications refer to signed inclusion proofs as "receipts",
profiles of proof signatures are encouraged to make additional protected header parameters mandatory.

TODO: reference to scitt receipts.

The protected header for an RFC9162_SHA256 inclusion proof signature is:

* alg (label: 1): REQUIRED. Signature algorithm identifier. Value type: int / tstr.
* verifiable-data-structure (label: TBD_1): REQUIRED. verifiable data structure algorithm identifier. Value type: int / tstr.
* kid (label: 4): OPTIONAL. Key identifier. Value type: bstr
* crit (label: 2): OPTIONAL. Criticality marker. Value type: [ +label ]

The unprotected header for an RFC9162_SHA256 inclusion proof signature is:

* inclusion-proof (label: TBD_2): REQUIRED. proof type identifier. Value type: bstr.

The payload of an RFC9162_SHA256 inclusion proof signature is the previous Merkle tree hash as defined in {{-certificate-transparency-v2}}.

The payload MUST be detached.

Detaching the payload forces verifiers to recompute the root from the inclusion proof signature,
this protects against implementation errors where the signature is verified but the root does not match the inclusion proof.

~~~~ cddl

inclusion-proofs = [ + bstr ]

unprotected-header-map = {
  &(inclusion-proof: TBD_2) => inclusion-proofs
  * cose-label => cose-value
}
~~~~

The following example needs to be converted to proper CDDL:

~~~~
# COSE_Sign1
18([

  # Protected Header
  h'a2012604588368747470733a2f2f73636974742e78797a2f75726e3a696574663a706172616d733a7472616e733a696e636c7573696f6e3a726663393136325f7368613235363a303a65343263333764326638306361613464323035353635376534303463386538363838313534346136663264313731356530663564616435643436343833633531',
  # {
  #   "alg" : "ES256",
  #   1 : -7,
  #   "verifiable-data-structure" : "RFC9162_SHA256",
  #   TBD_1 : 1,
  # }

  # Unprotected Header
  {
      # "inclusion-proof" : "h'3133312c322c302c3132392c3231362c36342c38382c33322c3235342c3132382c33392c34392c3131382c312c3230352c38372c3235332c3136312c31332c3136312c38352c3139302c3133322c3234312c3137332c34352c3132372c32302c35302c35342c31332c3134342c33332c3233372c3234382c3132382c32332c3138392c3133352c3932'"
      TBD_2 : h'3133312c322c302c3132392c3231362c36342c38382c33322c3235342c3132382c33392c34392c3131382c312c3230352c38372c3235332c3136312c31332c3136312c38352c3139302c3133322c3234312c3137332c34352c3132372c32302c35302c35342c31332c3134342c33332c3233372c3234382c3132382c32332c3138392c3133352c3932'
  },

  # Detached Payload

  # Signature
  h'4862c1dced27ceeb1f7a6277d13be127a8969a7171ae000ffa90ef5757b817ca8ee61d57645d1a087251a97f06eb61aec46ecf958e4a0fb94ae37f410c7f77ea'
])
~~~~

## Consistency Proof Definition {#sec-rfc9162-sha256-consistency-proof}

See {{-certificate-transparency-v2}}, 2.1.4.1. Generating a Consistency Proof,
for a complete description of this verifiable data structure proof type.

The cbor representation of a consistency proof for RFC9162_SHA256 is:

~~~~ cddl
consistency-proof = #TBD_3([
    tree-size-1: int ; size of the tree, when the previous root was produced.
    tree-size-2: int ; size of the tree, when the latest root was produced.
    consistency-path: [+ bstr] ; consistency path, from previous root to latest root.
])
~~~~

Editors note: tree-size-1, could be ommited, if an inclusion-proof is always present, since the inclusion proof contains, tree-size-1.

### Consistency Proof Signature

In a signed consistency proof, the latest merkle tree root, maps to tree-size-2, and is an attached payload.

The protected header for an RFC9162_SHA256 consistency proof signature is:

* alg (label: 1): REQUIRED. Signature algorithm identifier. Value type: int / tstr.
* verifiable-data-structure (label: TBD_1): REQUIRED. verifiable data structure algorithm identifier. Value type: int / tstr.
* kid (label: 4): OPTIONAL. Key identifier. Value type: bstr
* crit (label: 2): OPTIONAL. Criticality marker. Value type: [ + label ]

The unprotected header for an RFC9162_SHA256 consistency proof signature is:

* consistency-proof (label: TBD_2): REQUIRED. proof type identifier. Value type: bstr.

The payload of an RFC9162_SHA256 consistency proof signature is:

The latest Merkle tree hash as defined in {{-certificate-transparency-v2}}.

The payload MUST be attached.

~~~~ cddl

consistency-proofs = [ + bstr ]

unprotected-header-map = {
  &(consistency-proof: TBD_3) => consistency-proofs
  * cose-label => cose-value
}
~~~~

The following example needs to be converted to proper CDDL:

~~~~
# COSE_Sign1
18([

  # Protected Header
  h'a2012604588568747470733a2f2f73636974742e78797a2f75726e3a696574663a706172616d733a7472616e733a636f6e73697374656e63793a726663393136325f7368613235363a303a66653830323733313736303163643537666461313064613135356265383466316164326437663134333233363064393032316564663838303137626438373563',
  # {
  #   "alg" : "ES256",
  #   1 : -7,
  #   "verifiable-data-structure" : "RFC9162_SHA256",
  #   TBD_1 : 1,
  # }

  # Unprotected Header
  {
      # "consistency-proof" : "h'3133312c312c312c3132392c3231362c36342c38382c33322c3235342c3132382c33392c34392c3131382c312c3230352c38372c3235332c3136312c31332c3136312c38352c3139302c3133322c3234312c3137332c34352c3132372c32302c35302c35342c31332c3134342c33332c3233372c3234382c3132382c32332c3138392c3133352c3932'"
      TBD_3 : h'3133312c312c312c3132392c3231362c36342c38382c33322c3235342c3132382c33392c34392c3131382c312c3230352c38372c3235332c3136312c31332c3136312c38352c3139302c3133322c3234312c3137332c34352c3132372c32302c35302c35342c31332c3134342c33332c3233372c3234382c3132382c32332c3138392c3133352c3932'
  },

  # Protected Payload
  h'fe8027317601cd57fda10da155be84f1ad2d7f1432360d9021edf88017bd875c',

  # Signature
  h'fe476fcddb783805fe344fc96837f4a5531c2e5a56d6f6353831e84e17ac69d4407a5a0d6eadf27f3a570bcf604181fd11b4692d3ac17b116c6226ba43726113'
])
~~~~

# Privacy Considerations

See the privacy considerations section of:

- {{-certificate-transparency-v2}}
- {{-COSE}}

Although the word transparency implies to some degree read access,
it is important to note that transparency logs might include sensitive information.

Depending on the verifiable data structure used, a service provider might be able to count unique entries.

In the case that an entry is produced from a cose sign 1 envelope,
adding information to the unprotected header can be used to produce a unique entry.

However, this could impact privacy, and some transparency service operators might prefer only integrity protected content be made transparent.

## Leaf Blinding {#sec-leaf-blinding}

In cases where a single merkle root and multiple inclusion paths are used to prove inclusion for multiple payloads. There is a risk that an attacker may be able to learn the content of undisclosed payloads, by brute forcing the values adjacent to the disclosed payloads through application of the cryptographic hash function and comparison to the the disclosed inclusion paths. This kind of attack can be mitigated by including a cryptographic nonce in the construction of the leaf, however this nonce must then be disclosed along side an inclusion proof which increases the size of multiple payload signed inclusion proofs.

Tree algorithm designers are encouraged to comment on this property of their leaf construction algorithm.

### Blinding Example {#sec-leaf-blinding-example}

Implementers wishing to leverage multiple inclusion proofs to support selective disclosure,
can prepend each payload with extra data before computing the inclusion proof, where extra data is a cryptographic nonce.

# Security Considerations

See the security considerations section of:

- {{-certificate-transparency-v2}}
- {{-COSE}}

## Hash Function Agility

The choice of cryptographic hash function is the primary primitive impacting the security of authenticating payload inclusion in a merkle root. Tree algorithm designers should review the latest guidance on selecting a suitable cryptographic hash function.

# IANA Considerations

## Additions to Existing Registries

### New Entries to the COSE Header Parameters Registry

This document requests IANA to add new values to the 'COSE
Algorithms' and to the 'COSE Header Algorithm Parameters' registries
in the 'Standards Action With Expert Review category.

#### COSE Header Algorithm Parameters

* Name: verifiable-data-structure
* Label: TBD_1 (requested assignment 12)
* Value type: int / tstr
* Value registry: See {{verifiable-data-structure-values}}
* Description: Tag indicating the Verifiable Data Structure, see {{sec-generic-verifiable-data-structures}}.

* Name: inclusion-proof
* Label: TBD_2 (requested assignment 13)
* Value type: bstr
* Value registry: See {{verifiable-data-structure-proof-types-values}}
* Description: Tag indicating the "inclusion" Proof Type, see {{sec-generic-inclusion-proof}}.

* Name: consistency-proof
* Label: TBD_2 (requested assignment 14)
* Value type: bstr
* Value registry: See {{verifiable-data-structure-proof-types-values}}
* Description: Tag indicating the "consistency" Proof Type, see {{sec-generic-consistency-proof}}.

### Verifiable Data Structures {#verifiable-data-structure-registry}

IANA will be asked to establish a registry of tree algorithm identifiers,
named "Verifiable Data Structures" to be administered under a Specification Required policy {{-iana-considerations-guide}}.

Template:

* Identifier: The two-byte identifier for the algorithm
* Algorithm: The name of the data structure
* Reference: Where the data structure is defined

Initial contents: Provided in {{verifiable-data-structure-values}}

### Verifiable Data Structure Proof Types {#verifiable-data-structure-proof-types-registry}

IANA will be asked to establish a registry of tree algorithm identifiers,
named "Verifiable Data Structures Proof Types" to be administered under a Specification Required policy {{-iana-considerations-guide}}.

Template:

* Identifier: The two-byte identifier for the algorithm
* Algorithm: The name of the proof type algorithm
* Reference: Where the algorithm is defined

Initial contents: Provided in {{verifiable-data-structure-proof-types-values}}

--- back

# Implementation Status

Note to RFC Editor: Please remove this section as well as references to {{BCP205}} before AUTH48.

This section records the status of known implementations of the protocol defined by this specification at the time of posting of this Internet-Draft, and is based on a proposal described in {{BCP205}}.
The description of implementations in this section is intended to assist the IETF in its decision processes in progressing drafts to RFCs.
Please note that the listing of any individual implementation here does not imply endorsement by the IETF.
Furthermore, no effort has been spent to verify the information presented here that was supplied by IETF contributors.
This is not intended as, and must not be construed to be, a catalog of available implementations or their features.
Readers are advised to note that other implementations may exist.

According to {{BCP205}},
"this will allow reviewers and working groups to assign due consideration to documents that have the benefit of running code, which may serve as evidence of valuable experimentation and feedback that have made the implemented protocols more mature.
It is up to the individual working groups to use this information as they see fit".

## Implementer

An open-source implementation was initiated and is maintained by the Transmute Industries Inc. - Transmute.

## Implementation Name

An application demonstrating the concepts is available at [https://scitt.xyz](https://scitt.xyz).

## Implementation URL

An open-source implementation is available at:

- https://github.com/transmute-industries/cose

## Maturity

The code's level of maturity is considered to be "prototype".

## Coverage and Version Compatibility

The current version ('main') implements the tree algorithm, inclusion proof and consistency proof concepts of this draft.

## License

The project and all corresponding code and data maintained on GitHub are provided under the Apache License, version 2.

## Implementation Dependencies

The implementation builds on concepts described in SCITT {{-scitt-architecture}} (https://scitt.io/).

The implementation uses the Concise Binary Object Representation {{-CBOR}} (https://cbor.io/).

The implementation uses the CBOR Object Signing and Encryption {{-COSE}}, maintained at:
- https://github.com/erdtman/cose-js

The implementation uses an implementation of {{-certificate-transparency-v2}},
maintained at:

- https://github.com/transmute-industries/rfc9162/tree/main/src/CoMETRE

## Contact

Orie Steele (orie@transmute.industries)
