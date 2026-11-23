---
para: resource
tags: [security]
---
Network authentication protocol: a KDC (Key Distribution Center) issues tickets so clients prove identity without sending passwords; Active Directory uses it.

# Start KDC server

%% TODO: extract custom kerberos startup example %%

## SPNEGO

HTTP "Negotiate" auth (RFC 4559) that carries Kerberos tickets so browsers get [[SSO]] against [[Active Directory]].
