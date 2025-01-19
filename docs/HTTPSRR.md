<!--
Copyright (C) Daniel Stenberg, <daniel@haxx.se>, et al.

SPDX-License-Identifier: curl
-->

# HTTPS RR

[RFC 9460](https://www.rfc-editor.org/rfc/rfc9460.html) documents the HTTPS
DNS Resource Record.

curl features **experimental** support for HTTPS RR.

- The ALPN list from the retrieved HTTPS record is parsed.
- The port number from the HTTPS RR is not used
- The IP addresses from the HTTPS RR are not used

## build

    ./configure --enable-httpsrr

## ALPN

The list of ALPNs is parsed but may not be completely respected because of
what the HTTP version preference is set to, which is a problem we are working
on. Also, getting an `HTTP/1.1` ALPN in the HTTPS RR field for an HTTP://
transfer should imply switching to HTTPS, HSTS style. Which curl currently
does not.

## DoH

The DoH code asks for an HTTPS record in addition to the A and AAA records,
and if an HTTPS RR answer is returned, curl parses it and stores the retrieved
information.

## Non-DoH

If DoH is not used for name resolving, we must provide the ability using the
regular resolver backends. We use the c-ares DNS library for the HTTPS lookup.
Version 1.28.0 or later.

### c-ares

If curl is built to use the c-ares library for name resolves, it makes a
request for the HTTPS record in addition to the regular lookup.

### Threaded resolver

When built to use the threaded resolver, which is the default, it still needs
a c-ares installation provided so that a separate request for the HTTPS record
can be done in parallel to the regular getaddrinfo() call.

Because the HTTPS record is then done separately from the A/AAAA record
retrieval, there is a risk for discrepancies.

## Options

Because curl is a lowlevel transfer tool for which users sometimes want
detailed control, we need to offer options to control HTTPS RR use.
