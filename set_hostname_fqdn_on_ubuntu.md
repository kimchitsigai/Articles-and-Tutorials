# ~~~~~~~~~~ DRAFT ~~~~~~~~~~
*[Pull Requests](https://github.com/DigitalOcean-User-Projects/Articles-and-Tutorials/pulls) gladly accepted*

Setting Your Ubuntu Server's Hostname & Fully Qualified Domain Name (FQDN)
=

### Introduction

A hostname is a label or nickname that is assigned to a computer connected to a network and that is used to identify the machine in various forms of electronic communication such as the World Wide Web and/or email. Hostnames are important because they form part of a computer's Fully Qualified Domain Name (FQDN).

## Hostname Requirements

Internet standards for protocols mandate that component hostname labels may contain only the [ASCII](http://en.wikipedia.org/wiki/ASCII) letters `a` through `z` (in a case-insensitive manner), the digits `0` through `9`, and the hyphen (`-`). No other symbols, punctuation characters, or white space are permitted.

In addition to the above technical requirements, the only practical requirement of a server's hostname, for your environment(s), is that it should be something unique to the other servers within a particular domain.

### Restrictions on valid host names

Hostnames are composed of series of labels concatenated with dots, as are all domain names. For example, `mail.google.com` is a hostname. Each label must be between 1 and 63 characters long, and the entire hostname (including the delimiting dots) has a maximum of 255 characters.

## Setting the Hostname

# ~~~~~~~~~~ DRAFT ~~~~~~~~~~
*[Pull Requests](https://github.com/DigitalOcean-User-Projects/Articles-and-Tutorials/pulls) gladly accepted*