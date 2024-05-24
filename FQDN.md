Fully Qualified Domain Names.

This was documented in the DNS specification, [RFC 1034](http://www.ietf.org/rfc/rfc1034.txt), way back in 1987:
Since a complete domain name ends with the root label, this leads to a
printed form which ends in a dot.  We use this property to distinguish between:
- a character string which represents a complete domain name often called "absolute").  For example, "poneria.ISI.EDU."
- a character string that represents the starting labels of a domain name which is incomplete, and should be completed by local software using knowledge of the local domain (often called "relative").  For example, "poneria" used in the ISI.EDU domain.

## Reference
[Trailing Dots in Domain Names](http://www.dns-sd.org/trailingdotsindomainnames.html)