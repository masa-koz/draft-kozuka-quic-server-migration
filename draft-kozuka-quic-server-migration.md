---
title: "QUIC Extension for Server-Initiated Connection Migration"
abbrev: "QUIC Server Migration"
category: std

docname: draft-kozuka-quic-server-migration-latest
submissiontype: IETF  # also: "independent", "editorial", "IAB", or "IRTF"
number:
date:
consensus: true
v: 3
area: "Web and Internet Transport"
workgroup: "QUIC"
keyword:
 - next generation
 - unicorn
 - sparkling distributed ledger
venue:
  group: "QUIC"
  type: "Working Group"
  mail: "quic@ietf.org"
  arch: "https://mailarchive.ietf.org/arch/browse/quic/"
  github: "masa-koz/draft-kozuka-quic-server-migration"
  latest: "https://masa-koz.github.io/draft-kozuka-quic-server-migration/draft-kozuka-quic-server-migration.html"

author:
 -
    fullname: "Masahiro Kozuka"
    organization: Okayama University
    email: "kozuka@okayama-u.ac.jp"

normative:

normative:

  QUIC-TRANSPORT:
    title: "QUIC: A UDP-Based Multiplexed and Secure Transport"
    date: 2021-05
    seriesinfo:
      RFC: 9000
      DOI: 10.17487/RFC9000
    author:
      -
        ins: J. Iyengar
        name: Jana Iyengar
        org: Fastly
        role: editor
      -
        ins: M. Thomson
        name: Martin Thomson
        org: Mozilla
        role: editor

informative:

--- abstract

This document specifies an extension that reverses this role: the server initiates migration.


--- middle

# Introduction

RFC 9000 defines connection migration as a mechanism initiated by the client to change its IP
address or network path without disrupting the QUIC connection. This mechanism is based on the
assumption that a client may be behind a NAT and a server has a public address. However,
there is a reversed situation where a client has a public address and a server is behind a NAT.
This document specifies an extension that reverses this role: the server initiates migration.

# Conventions and Definitions

{::boilerplate bcp14-tagged}

# Motivation

A key scenario is:

- The server is initially behind a NAT and cannot accept direct connections.
- The client has a public IP address.
- The server first establishes the QUIC connection via a relay server.
- Immediately after the handshake, the server migrates the connection to the client’s public address, reducing latency.

This specification does not define how the server obtains the public address of the client.
Possible methods include application-level signaling or external mechanisms, but these are out
of scope.

# Scope

- Reverse migration roles defined in RFC 9000.
- Maintain QUIC security guarantees.
- No new NAT traversal mechanism.

# Negotiating Extension Use

allow_server_migration (0x3e764478):

Clients advertise their support of this extension by sending the allow_server_migration
(0x3e764478) transport parameter ({{Section 7.4 of QUIC-TRANSPORT}}) with an empty value.
Sending this transport parameter signals to the server that the client will accept the
server-initiated migration.

Servers also send this parameter with an empty value. The server informs the client that
it will initiate the connection migration by sending this parameter.

When this extension is negotiated, the server-initiated migration is only permitted and
the client-initiated migration is prohibited.

An implementation that understands this transport parameter MUST treat the receipt of a
non-empty value as a connection error of type TRANSPORT_PARAMETER_ERROR.

Endpoints MUST NOT remember the value of this extension for 0-RTT.

# Connection Migration Procedure

The connection migration procedure is the same as ({{Section 9 of QUIC-TRANSPORT}}) except
that the roles between the client and the server are reversed.

# Security Considerations

TODO Security


# IANA Considerations

## QUIC Transport Parameter

This document registers the allow_server_migration transport parameter in the "QUIC
Transport Parameters" registry established in {{Section 22.3 of QUIC-TRANSPORT}}.
The following fields are registered:

Value:
: 0x3e764478

Parameter Name:
: allow_server_migration

Status:
: Provisional

Specification:
: This document

Change Controller:
: IETF (iesg@ietf.org)

Contact:
: Masahiro Kozuka (kozuka@okayama-u.ac.jp)



--- back

# Acknowledgments
{:numbered="false"}

TODO acknowledge.

# Questions
{:numbered="false"}

- Sould we extend this extension to allow both clients and servers to initiate connection migration?

- Any new security conserations from allowing servers to initiate connection migration?
