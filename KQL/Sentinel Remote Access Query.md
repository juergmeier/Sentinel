# Sentinel Remote Access Query
Sentinel Remote Access Query

## Description
Check remote access to Azure Systems / Portals
Credits to Contributor: Juerg, Thanks Alex for the idea with his KQL Scritps. 

```kusto
## Query
// check for network events originating from these IPs
//let publicips =
let bvLocalPort ="3389";
let bvRemoteIPType = "Public"; // Loopback, Unspecified, Private, Public
let bvActionType = "LogonFailed"; // LogonAttempted, LogonSuccess, LogonFailed
DeviceLogonEvents
| where ActionType == bvActionType
| where RemoteIPType == bvRemoteIPType
| summarize any(DeviceName, ActionType, LogonType, FailureReason, Protocol,RemoteIP) by RemoteIP
| join kind=leftouter (DeviceNetworkEvents
) on $left.RemoteIP == $right.RemoteIP
| where LocalPort == bvLocalPort
| where RemoteIPType == bvRemoteIPType
| project DeviceName, RemoteIP, RemotePort, LocalPort, InitiatingProcessCommandLine, ActionType, Timestamp
