BGP Configuration: Recommendations for setting up BGP correctly
Registering in IRR and RPKI Databases
The Internet Routing Registry (IRR) is a globally distributed collection of databases used to register routing information and publish routing policies. Network operators register their BGP announcements in IRR databases that are used by other networks to create BGP filters and make informed routing decisions.

Because there are Network Operators that specifically rely on IRR to create filters (more details under "Route Filtering" below), enterprises that don't register resources in IRR may fail to establish desired BGP peerings. Also, consider that if a network operator fails to register their Internet resources or updates them incorrectly, it can result in BGP misconfigurations and disruptions to Internet connectivity.

The Resource Public Key Infrastructure (RPKI) is a system for verifying the authenticity of Internet routing information. By registering Internet resources in RPKI, network operators can create cryptographic signatures that validate the legitimacy of their routing information.

Routers receiving BGP updates can use these signatures to validate the routing information's authenticity and prevent malicious route hijacks. Registering Internet resources in RPKI is crucial to prevent accidental misconfigurations and deliberate attacks on BGP routing, improving the security and stability of the Internet routing system.
[![Uploading image.png…]()](https://labs.ripe.net/media/images/RPKI_IRR_REGISTRATION.width-1536.png)

RPKI_IRR_REGISTRATION
Registering Internet resources in IRR and RPKI databases is fundamental in helping to ensure that BGP routes can be authenticated!

BGP timers
BGP default timers are: Keepalive (60s), Hold Time (180s) ( 3 * Keepalive Timer)

The Keepalive timer determines how frequently BGP speakers send keepalive messages to each other to ensure the BGP session remains active.

The Hold Time timer determines how long a BGP speaker will wait for a keepalive message from its peer before considering the session to be down.

TIMERS
Tuning these timers can affect BGP operability in several ways. Setting a shorter keepalive and hold timer can improve BGP convergence time and reduce the impact of network outages, but may increase BGP traffic on the network. Setting a longer keepalive and hold timer can reduce BGP traffic, but may lead to longer convergence times and slower detection of network outages.

Tuning BGP timers should be done carefully based on the specific network requirements and conditions!

BGP MRAI and RFD
MRAI (Minimum Route Advertisement Interval RFC7747) is a mechanism used in BGP to limit the number of route update messages sent between BGP speakers.

MRAI timers are used to delay the sending of route update messages to reduce the amount of BGP traffic on the network and improve BGP stability. The MRAI timer starts when a route update message is sent, and subsequent updates for the same prefix are delayed until the timer expires.

MRAI
The timer value is typically configured based on the expected frequency of updates for a given prefix. While MRAI can help improve BGP stability and reduce network congestion, it can also lead to longer convergence times in some cases, so it should be configured carefully based on network requirements.

RFD (Route Flap Damping RFC2439) is a mechanism used to reduce the impact of unstable or flapping routes on the BGP routing system. When a BGP route is unstable - i.e., it frequently changes its status - RFD can be used to dampen the updates for that route, reducing the amount of BGP traffic on the network. RFD works by assigning a penalty to the route each time it flaps, and suppressing updates for the route if the penalty exceeds a certain threshold.

RFD
While RFD can help improve BGP stability, it can also lead to longer convergence times and suboptimal routing in some cases, so it should be used with care.

BGP Neighbour Fail Detection with BFD
BFD (Bidirectional Forwarding Detection RFC5880) is a protocol used to detect failures in the forwarding path between two network devices.

BFD1
Without BFD, BGP relies on default timers to detect if a BGP peer is no longer available. This timer can take several minutes to detect a failure, during which time the router may continue to send traffic to the failed peer, causing delays and potential loss of data.

With BFD, the detection of a BGP peer failure can be almost immediate, typically within a few milliseconds. This allows the router to quickly remove the failed peer from the forwarding table and start routing traffic to an alternative path, minimising the impact of the failure.

BGP Graceful Restart
Graceful Restart for BGP (RFC4724) feature that allows BGP speakers to continue forwarding traffic during a BGP restart. When a BGP speaker restarts, it may need to rebuild its BGP routing table and reestablish BGP peering sessions with its neighbours, which can cause a temporary disruption in traffic forwarding.

With Graceful Restart, a restarting BGP speaker can inform its neighbours that it is restarting and request them to keep forwarding traffic for the BGP routes that they were previously using.

Graceful_Restart
The neighbours can continue forwarding traffic for these routes until the restarting BGP speaker has reestablished its BGP sessions and updated its routing table. Graceful Restart can help reduce the impact of BGP restarts on network traffic and minimise the disruption to end users. However, it requires support from both the restarting BGP speaker and its neighbours, and should be carefully configured and tested to ensure proper operation.

BGP Next-Hop Tracking
Next-Hop Tracking is a feature in BGP that helps to maintain a consistent forwarding path in a network. It allows a router to track the reachability of the next-hop IP address for a particular route and update the BGP routing table accordingly.

Next_hop_tracking
Next-Hop Tracking is particularly useful in multi-homed deployments where a router has multiple paths to reach a particular destination and wants to ensure that the traffic is forwarded through a specific next-hop. By using Next-Hop Tracking, BGP can ensure that the traffic is always forwarded through the best available next-hop, even in the event of a link or node failure.

BGP Security I: Techniques for protecting BGP against attacks
BGP Authentication - TCP MD5 Signature Option
BGP TCP MD5 Signature Option (RFC2385) is a mechanism used to provide a level of security for BGP sessions by verifying the authenticity of BGP messages exchanged between peers.

When BGP MD5 authentication is enabled, each BGP message is hashed using the MD5 algorithm and a shared secret key. The resulting hash value is included in the message and sent to the receiving BGP peer. The receiving peer also calculates the hash value of the received message using the same shared secret key and MD5 algorithm. If the calculated hash value matches the hash value included in the message, the message is considered valid and accepted.

MD5_AUTH
A better authentication over TCP MD5 is BGP TCP Authentication Option. BGP AO supports more robust authentication algorithms and provides improved key management. Whenever supported by your hardware, it is recommended to configure BGP AO.

TTL Security
TTL Security (RFC5082) is a mechanism used to prevent unauthorised access to BGP routing information by limiting the distance that BGP updates can travel through the network. When TTL Security is enabled, BGP updates that exceed a certain Time-to-Live (TTL) value are dropped by the receiving BGP peer. This TTL value is configurable and determines the maximum number of hops that a BGP update can travel through the network.

TTL_Security
This mechanism is particularly useful in one-hop eBGP peerings where the TTL value for BGP messages is set to 255. This prevents attacks such as BGP hijacking from being initiated farther than one hop away.

Protecting the Data Plane - ACLs
Using ACLs (Access Control Lists) to permit or block the well-known TCP port 179 for specific BGP peers is a useful security measure that can help protect the BGP protocol from unauthorised access or attacks.

ACL
Protecting the Control Plate - CoPP
CoPP (Control Plane Protection RFC6192) is a security feature that is designed to protect the control plane of a network device. CoPP works by prioritising and regulating traffic that is destined for the control plane of the device. It applies Quality of Service (QoS) policies to ensure that only legitimate traffic reaches the control plane.

COPP
In the context of BGP, CoPP can help protect against DDoS attacks and other types of malicious traffic from spoofed IP addresses that could potentially overwhelm the control plane of the device.

Route Filtering
BGP Route Filtering (best described in RFC7454) is an essential tool for maintaining BGP security. By using filters, network operators control the routing information that enters or exits their AS.

There are several types of BGP filters, including prefix filters, AS path filters, and community filters. Prefix filters can be used to allow or block specific prefixes from being advertised, while AS path filters can be used to limit the ASNs that are allowed to advertise a specific prefix. Community filters can be used to control how specific BGP communities are handled, such as preventing certain communities from being propagated to specific peers.

ROUTE_FILTER
bgpq4, IRRToolSet, and Rpsltool - are some of the open-source tools for automatic Filters creation. These software query IRRs by using the Whois protocol to automate router configurations for different vendor environments.

Route Filters are essential for a correct BGP configuration. If implemented properly, Route Filtering ensures that only valid routing information is propagated throughout the network, prevents misconfigurations and malicious attacks, and can ultimately improve the overall security and stability of the BGP protocol.

ROV
ROV (RPKI Origin Validation RFC8481) is a security mechanism that enhances the security of BGP routing by providing a way to cryptographically validate the origin of BGP route announcements. To implement ROV, network operators must first register their Internet resources into the RPKI system and create a Route Origin Authorization (ROA) record for each prefix they advertise to the Internet. The ROA record contains information about the prefix and the autonomous system (AS) that is authorized to originate it.

ROV
Free software for ROV implementation includes: Routinator, OctoRPKI and FORT Validator

Once the ROA records are created, BGP routers can be configured to validate the origin of received BGP routes against the ROA records. The router downloads RPKI cache information from the Validator server and marks the received routes as RPKI Valid or RPKI Invalid. By implementing ROV, network operators can improve the security and resilience of their BGP routing by preventing the propagation of invalid or malicious routes, and reducing the risk of BGP hijacks and route leaks.

BGP Monitoring: Detecting incidents from within and outside the AS
BMP
BMP (BGP Monitoring Protocol RFC7854) is a client-server protocol that helps monitor and troubleshoot BGP routing. BMP can be used to collect and analyse BGP messages exchanged between routers, as well as to monitor the state of BGP peering sessions and the routes advertised and received by BGP speakers.

BMP
BMP can also be used to store BGP routing information for historical analysis and trend analysis, which can help identify patterns and make more informed decisions about network routing.

Integrating BGP into an NMS
Integrating BGP into a Network Monitoring System is important because it allows network administrators to monitor the performance and health of BGP sessions in real time.

By conducting ICMP probes and collecting SNMP information, an NMS will detect issues as well as provide visibility into network traffic and routing changes in real-time. Integrating BGP into a Network Monitoring System is important because it allows network administrators to monitor the performance and health of BGP sessions in real-time.

Screenshot 2023-01-10 110909
Open-source software like Zabbix provides ready-to-use BGP Discovery templates which connect all your BGP infrastructure to centralised monitoring in seconds.

Screenshot 2023-03-04 012146
After integrating the BGP infrastructure into Zabbix, network operators have the option to receive live notifications of BGP operations by Email, SMS or communicating platforms like Microsoft Teams.

sFlow-RT
BGP sFlow is a technology that provides real-time visibility into network traffic by collecting and analysing samples of network packets. It allows network administrators to monitor traffic patterns and identify potential issues, including security threats such as DDoS attacks.

SFLOW

sFlow is implemented by creating an agent on a BGP running network device that generates and exports telemetry data to a collector for analysis. By leveraging sFlow, network administrators can gain greater visibility into their network, improve troubleshooting and optimisation, and enhance network security.

BGPalerter
BGPalerter tool is an open-source software to detect and report BGP hijacks, leaks, and other anomalous behaviour in real-time. BGPalerter connects to public repositories to retrieve the routing information and then detect deviations. It also provides flexible and customisable alerting mechanisms, such as email notifications, webhooks, and integration with popular chat platforms like Slack and MS Teams.

222546405-5d7e3fab-1494-4115-b8da-c30a670f7250
If your infrastructure is already monitored by Zabbix, BGPalerter can be easily integrated by following the following BGPalerter with Zabbix project.

RPKI Dashboard
RPKI Dashboard is the place you are creating your ROAs and also the place to check your resources status. By enabling RPKI Dashboard alerts you will receive information about attempts to hijack your address space and possible misconfigured ROAs.

SUBSCRIBE
After subscribing to the alerts they will be sent by email daily or weekly in case there are deviations.

BGP Security II: Techniques for protecting against attacks with BGP
uRPF
uRPF (Unicast Reverse Path Forwarding described in RFC3704) is a mechanism used by network devices to help prevent IP address spoofing. uRPF works by checking the source IP address of an incoming packet against the routing table of the device. If the source IP address is not present in the routing table (Loose Mode) or the interface that the packet arrived on is not the same interface that would be used to forward traffic to the source IP address(Strict Mode), the packet is dropped.

URPF
In the context of BGP security, uRPF can be used to prevent BGP route injection attacks, where a malicious actor injects false BGP routing information with spoofed source IP addresses. By implementing uRPF, BGP speakers can drop these packets and prevent the routing of false information. uRPF helps increase the overall security and stability of BGP routing.

RTBH
RTBH (Remote Triggered Black Hole described in RFC6666 and RFC7999) and is a method used to mitigate DDoS attacks in BGP networks. With RTBH, traffic is diverted to a black hole, which is essentially a null interface or a virtual discard interface that drops all traffic destined for a specific IP address or prefix.

RTBH
To implement RTBH, the network operator creates a route to the black hole with a higher administrative distance than the legitimate route. When a DDoS attack is detected, the operator uses a BGP community to advertise the black hole route to the network, causing all traffic destined for the affected IP address or prefix to be dropped at the black hole instead of being forwarded to the target. This effectively stops the attack by denying the attacker's traffic from reaching the target.

RTBH can be implemented either at the edge of the network, closer to the source of the attack, or deeper within the network, closer to the target. RTBH should be implemented rigorously as it can potentially drop legitimate traffic!

Screenshot 2022-12-15 224011
Ban2BGP - is an easy tool for generating blackhole routes to a company's AS border routers, or to an Internet Service Provider's border routers if negotiated as a service.

Advanced DDoS Protection
The combination of RTBH and sFlow can provide a powerful defense against DDoS attacks.As mentioned earlier, sFlow is a network protocol that enables the collection of traffic data from network devices. By analyzing this data, network administrators can identify anomalous traffic patterns and detect potential DDoS attacks. By combining these two techniques, network administrators can use sFlow to detect a DDoS attack and trigger RTBH to drop the attack traffic at the edge of the network. This can be done in near-real time, effectively mitigating the attack before it can cause significant damage to the network.

DDOS_PROTECT
DDoS Protect is an open source denial of service mitigation tool that uses industry standard sFlow telemetry from routers to detect attacks and automatically deploy BGP remotely triggered blackhole (RTBH) and BGP Flowspec filters to block attacks within seconds.
