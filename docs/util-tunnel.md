---
id: util-tunnel
title: XINA Tunnel
---

> Under Construction

XINA Tunnel is a Java application which provides a bridge between application(s) using the XINA API and the XINA server.

To access the XINA server, the user must first launch the XINA Tunnel application, which will prompt the user to log in
to the XINA server. Once this step is completed XINA Tunnel launches a local server, which provides a tunnel to the XINA
server. Applications implementing the XINA API may then open a standard TCP connection to XINA Tunnel and begin
communicating with the XINA server.
