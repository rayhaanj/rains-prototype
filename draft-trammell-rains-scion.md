---
title: RAINS Parameters for SCION
abbrev: RAINS
docname: draft-trammell-rains-scion-00
date: 
category: exp

ipr: trust200902
keyword: Internet-Draft

stand_alone: yes
pi: [toc, sortrefs, symrefs]

author:
 -
    ins: B. Trammell
    name: Brian Trammell
    organization: ETH Zurich
    street: Universitaetstrasse 6
    city: Zurich
    code: 8092
    country: Switzerland
    email: ietf@trammell.ch

normative:
    I-D.trammell-inip-pins:
    I-D.trammell-rains-protocol:

informative:
    SCION:
      title: SCION Five Years Later - Revisiting Scalability, Control, and Isolation Next-Generation Networks (arXiv:1508.01651v1)
      author: 
        -
          ins: D. Barrera 
        - 
          ins: R. Reischuk
        - 
          ins: P. Szalachowski
        - 
          ins: A. Perrig
      date: 2015-08


--- abstract

This document defines additional functionality atop the RAINS naming service
specific to the SCION Internet architecture.

--- middle

# Introduction

RAINS, Another Internet Naming Service (RAINS) {{I-D.trammell-rains-protocol}}
is a clean-slate Internet naming service defined to meet a set of identified
properties of an ideal Internet naming service {{I-D.trammell-inip-pins}}. RAINS
was defined in the context of the SCION {{SCION}} architecture. This document updates the RAINS protocol specification with SCION-specific extstions.

# SCION Address Objects

SCION addresses are based on IPv6 addresses, adding an Isolation Domain (ISD)
and Autonomous System (AS) number. These two additional address components are
used to select paths between a source and destination of traffic. RAINS for
SCION adds an additional object data type to those listed in section 5.12 of
{{I-D.trammell-rains-protocol}} to support SCION addresses.

| Code  | Name         | Description                             |
|------:|--------------|-----------------------------------------|
| 23    | scion6-addr  | SCION IPv6 address of subject           |
| 22    | scion4-addr  | SCION IPv4 address of subject           |

A scion6-addr (23) object contains a SCION address associated with a name.  It
is represented as a four element array.  The second element is an ISD number as
an integer less than or equal to 2^16-1. The third element is an AS number as an
integer less than or equal to 2^32-1. The fourth element is a byte array of length
16 containing an IPv6 address in network byte order.

A scion4-addr (22) object contains a SCION address associated with a name.  It
is represented as a four element array.  The second element is an ISD number as
an integer less than or equal to 2^16-1. The third element is an AS number as an
integer less than or equal to 2^32-1. The fourth element is a byte array of
length 4 containing an IPv4 address in network byte order.

 ## Address to name mappings for SCION addresses

Since the IP address part of a SCION address is scoped to the global IP address
space, reverse lookups for SCION addresses work exactly as reverse lookups for
IP addresses of the equivalent address family. The ISD and AS number are not
used in the address space delegation tree or in the query lookup procedure.
Despite this fact, the delegation trees for lookup of addresses in the IPv6 and
SCION address families is kept separate, as an organization may want a name to
be bound only to their SCION or to their IP space, respectively.

When the subject-addr (5) key for an address assertion contains object
type scion6-addr (23), the value is a three element CBOR array.  The
first element of the array is the address family encoded as an object type, here
23. The second element is the prefix length encoded as an integer, 0-128. The
third element is the IPv6 part of the address, encoded as a byte array of length
16 in network byte order. A subject address with prefix length 128 is a subject
host address, and is nameable; otherwise it is a subject network address, and is
delegatable.

When the subject-addr (5) key for an address assertion contains object
type scion6-addr (22), the value is a three element CBOR array.  The
first element of the array is the address family encoded as an object type, here
22. The second element is the prefix length encoded as an integer, 0-32. The
third element is the IPv4 part of the address, encoded as a byte array of length
4 in network byte order. A subject address with prefix length 32 is a subject
host address, and is nameable; otherwise it is a subject network address, and is
delegatable.

# ARPKI certificates in RAINS Certificate objects

[EDITOR'S NOTE: write me, discuss which types we need here. At least two: a PBC
for a domain, or a proof of absence of a PBC.)]

{: #tabcertusage title="Additional certificate information usage values for SCION"}

| Code | Certificate usage          |
|-----:|----------------------------|
|   22 | Policy absence proof       |
|   23 | Policy binding certificate |

# ARPKI keyspace

[EDITOR'S NOTE: write me, define how to do an SCP validation]

{: #tabkeyspace title="Additional keyspace for SCION"}

| Keyspace ID | Name  | Signature Verification Algorithm                 |
|------------:|-------|--------------------------------------------------|
| 1           | arpki | ARPKI SCP signature; see {{arpki-keyspace}}      |

# RAINS servers over native SCION multipath transport

[EDITOR'S NOTE: write me, once SSP is defined to the point we have something to cite.]
