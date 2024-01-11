# OPNSense & Mikrotik: Migrating single trunk port to LACP (lagg)

```
- Document Status: Published
- Scope: Network
- Interface: WebUI
- Impact: Potentially Disruptive
- Maintenance Window: Required
- Approval by: VP of Neteng
```

---

## Objective

The purpose of this guide is to walk users through the steps of upgrading the trunk link between OPSense and a downstream Mikrotik switch from a single port configuration, to a 2-port link aggreggation configuration using LACP.
This quide only applies to the migration of existing, OPNSense VLAN interfaces to a newly-created, parent LAGG. For creating new VLAN interfaces on an existsing LAGG interface, please see the appropriate documentation. 



> ### Prequisites
>
> * The reader has appropriate access to modify OPNSense Configurations
> * The reader has appropriate access to modify Mikrotik Switch configurations
> * All requisite change management processes have been followed prior to beginning this upgrade
> * OPNSense host has 2 physical ports available
> * Downstream switch has 2 physical ports available


## Summary

If you are running into link saturation or network congestion issues when multiple clients are pushing high-throughput workloads, it may be beneficial to migrate those client VLAN interfaces from a single parent interface to a link aggreggation interface. 

Doing so will allow the traffic from those clients to traverse different physical ports, enabling individual clients to fully utilize their own network interfaces without fully saturating an upstream interface on the switch or router. 

In this guide we will break down the steps for creating a new LAGG interface on OPNSense, and moving existing VLANs to from their current parent interface to the new LAGG, which will act as the trunk to the downstream Mikrotik switch.

## Preparing the Switch

The Mikrotik switch will automatically detect an aggregated link configuration from an upstream device, so we only need to make sure that the ports we will be using are configured as trunk ports to carry our VLAN traffic.

Each member port must be configured individually - there will not be a separate, abstracted LAGG interface in the switch's webUI. 
In this example, we will be using `VLAN 666` to represent the default VLAN for unused switch ports.  


### 1. Verify the tranceivers 
Before proceeding, we need to make sure the trancievers are detected by the switch.

1. Using HTTP ONLY, Navigate to the web UI of the switch
2. Authenticate using the appropriate credentials 
3. Navigate to the **SFP** Tab
4. Verify the tranceivers are present in the desired ports

### 2. Disable the Switch Ports
To mitigate the potential for a switching loop, we need to disable the ports that our new LAGG will live on. 

1. In the Mikrotik webUI, Navigate to the **Link** Tab
2. Set the target ports to `Disabled`


### 3. 
