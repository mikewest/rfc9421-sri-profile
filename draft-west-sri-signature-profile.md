---
###
# Internet-Draft Markdown Template
#
# Rename this file from draft-todo-yourname-protocol.md to get started.
# Draft name format is "draft-<yourname>-<workgroup>-<name>.md".
#
# For initial setup, you only need to edit the first block of fields.
# Only "title" needs to be changed; delete "abbrev" if your title is short.
# Any other content can be edited, but be careful not to introduce errors.
# Some fields will be set automatically during setup if they are unchanged.
#
# Don't include "-00" or "-latest" in the filename.
# Labels in the form draft-<yourname>-<workgroup>-<name>-latest are used by
# the tools to refer to the current version; see "docname" for example.
#
# This template uses kramdown-rfc: https://github.com/cabo/kramdown-rfc
# You can replace the entire file if you prefer a different format.
# Change the file extension to match the format (.xml for XML, etc...)
#
###
title: "An HTTP Message Signature Profile for Client-Side Verification"
abbrev: "SRI Signature Profile"
category: info

docname: draft-west-sri-signature-profile
submissiontype: IETF
number:
date:
consensus: true
v: 3
area: "Applications"
workgroup: "HyperText Transfer Protocol"
keyword:
 - message signature profile
 - subresource integrity
venue:
  group: "HyperText Transfer Protocol"
  type: "Working Group"
  mail: "http-wg@hplb.hp.com"
  arch: "https://www.ics.uci.edu/pub/ietf/http/hypermail"
  github: "mikewest/rfc9421-sri-profile"
  latest: "https://mikewest.github.io/rfc9421-sri-profile/draft-west-sri-signature-profile.html"

author:
 -
    fullname: Mike West
    organization: Google, LLC.
    email: mkwst@google.com

normative:
  RFC9421:

informative:
  I-D.pardue-http-identity-digest:
  RFC4648:
  STRUCTURED-FIELDS: RFC9651
  FETCH:
    title: Fetch Standard
    author:
      -
        name: Anne van Kesteren
        org: Apple
    target: https://fetch.spec.whatwg.org/
  SIGSRI:
    title: Signature-based SRI
    author:
      -
        ins: M. West
        name: Mike West
        org: Google, LLC

--- abstract

An HTTP Message Signature profile that specifies the requirements for signatures
intended as proofs of integrity/provenance that can be enforced upon by clients
without any pre-existing relationship to the server which delivered them.


--- middle

# Introduction

This document defines an HTTP Message Signature profile that specifies the
requirements for signatures intended as proofs of integrity/provenance that can
be enforced upon by clients without any pre-existing relationship to the server
which delivered them. This requires locking down the components and properties
of the signature itself, as well as some of the decision points
available during the generation of the signature base (Section 2.5 of
{{RFC9421}}).

In short: this profile supports only Ed25519 signatures, requires that the
public key portion of the verification key material be included in the
signature's input, and specifies the ordering of the components and properties
to remove potential ambiguity about the signature's construction.


# Conventions and Definitions

{::boilerplate bcp14-tagged}

# The SRI Profile

The following requirements define a profile of HTTP Message Signatures that
enables clients to verify a response's signature without any additional
information, following the guidelines laid out in Section 1.4 of {{RFC9421}}:

## Components and Parameters:

The signature's input MUST:

1.  Include the following component identifiers (Section 2 of {{RFC9421}}) with
    their associated constraints:

    *   `identity-digest`, which MUST include the `sf` parameter (Section 2.1
         of RFC9421}}) and no other parameters. 

2.  Include the following parameters (Section 2.3 of {{RFC9421}}) with their
    associated constraints:

    *   `alg`, whose value MUST be the string `ed25519`
    *   `keyid`, whose value MUST be a string containing an base64-encoding of
        the public key portion of the signature's verification key material.
    *   `tag`, whose value MUST be the string `sri` (or something more specific? `enforce-ed25519-provenance`?)

3.  Order the signature's parameters alphabetically.

The signature's input MAY:

2.  Include the following parameters, with their associated constraints:

    *   `created`, an integer whose value MUST represent a time in the past.
    *   `expires`, an integer whose value MUST represent a time in the future.


## Structured Field Types

The `identity-digest` component references the `Identity-Digest` header defined in
{{I-D.pardue-http-identity-digest}}. It is expected to be a Dictionary Structured
Field (Section 3.2 of {{STRUCTURED-FIELDS}}).


## Retrieving the Key Material

The public key of the verification key material can be directly extracted from
the signature input's `keyid` parameter, where it's represented as a
base64-encoded string (Section 4 of {{RFC4648}}).


## Signature Algorithms

The only signature algorithm allowed is `ed25519`.


## Determine Key/Algorithm Appropriateness

Since the only accepted algorithm is `ed25519`, it is appropriate for any
context in which this profile will be used, and we require it to be specified
as the `alg` parameter to the signature's input.


## Derivation Context

The context for derivation of message components from an HTTP message and its
application context is the HTTP message itself, encompassing the response with
which the signature was delivered, and the request to which it responds.


## Error Reporting from Verifier to Signer

No error reporting is required.

Clients MUST represent verification failures as network errors, consistent with
{{FETCH}}'s handling of other server-specified constraints on the usage of
response data.


# Security Considerations

This profile has no security considerations beyond those specified in
{{RFC9421}} itself, and in {{SIGSRI}} for which it serves as foundation.


# IANA Considerations

This document has no IANA actions.


--- back

# Acknowledgments
{:numbered="false"}

TODO acknowledge.
