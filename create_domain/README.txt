INTRODUCTION:
------------

This module contains script files to create a domain.


REQUIREMENTS:
------------

This module requires the following modules:

 * Python 3
   Libraries
  * requests
  * sys
  * json
  * time

 * The scripts must be run outside sddc-manager environment.

 * DNS resolution must be done for sddc-manager.


PREREQUSITES:
--------------

The following data is required

Name of the domain

vCenter details

	->Name of the vCenter

	->Network details

	->IP Address of the vCenter

	->FQDN of the vCenter

	->Gateway

	->Subnet mask

	->License key for the vCenter

	->Password for the root user (8-20 characters)

	->Name of the datacenter where vCenter needs to be deployed

List of clusters

	For each cluster

		->Name of the cluster

		->List of hosts

			->For each host

			->ID of the host (UUID)

			->License key for the host

			->List of VDS names to associate with host

			->ID of the vmNic host to be associated with VDS, once added to cluster

Datastore details

  NOTE
  Only one of "vsanDatastoreSpec" (For VSAN) or NFS "nfsDatastoreSpecs" (For NFS) must be specified.
  For VSAN

  Number of host failures to tolerate (can be 0, 1, or 2)

  License key for the vSAN datastore

  {
    "vsanDatastoreSpec" : {
      "failuresToTolerate" : 1,
      "licenseKey" : "XXXXX-XXXXX-XXXXX-XXXXX-XXXXX",
      "datastoreName" : "vSanDatastore"
    }
  }

For NFS

    List of NFS server names

    Shared directory path

    User tag used to annotate NFS share

    Boolean to identify if the mount directory should be read-only

    {
      "nfsDatastoreSpecs" : [ {
        "nasVolume" : {
          "serverName" : [ "10.0.0.250" ],
          "path" : "/nfs_mount/my_read_write_folder",
          "readOnly" : false
        },
        "datastoreName" : "NFSShare"
      } ]
    }
    Network Details

    List of VDS details

    For each VDS

    Port group names and the corresponding transport type

    DVS host Infrastructure traffic resource type

    Maximum allowed usage for a traffic class

    Amount of bandwidth to be reserved for the host infrastructure traffic class

  NSX cluster Details

    NOTE
    Only one of "nsxVClusterSpec" (For NSX-V) or "nsxTClusterSpec" (For NSX-T) must be specified.
    For NSX-V

    VLAN ID of the VXLAN

    License key for NSX

    VDS to be used for VXLAN traffic/port group. This should belong to one of the VDS being created for the cluster

    {
      "nsxVClusterSpec" : {
        "vlanId" : 3,
        "vdsNameForVxlanConfig" : "SDDC-Dswitch-Private1"
      }
    }
    For NSX-T

    VLAN ID of Geneve

    {
      "nsxTClusterSpec" : {
        "geneveVlanId" : 2
      }
    }
    NSX details

    NOTE
    Only one of "nsxVSpec" (For NSX-V) or "nsxTSpec" (For NSX-T) must be specified.
    For NSX-V

  NSX Manager virtual machine details

    Name of the NSX Manager virtual machine

    Network details

    IP address of the virtual machine

    Fully-qualified domain name

    Gateway

    Subnet mask

    NSX-V Controller Details

    Controller IP addresses (three IPs) without duplicates

    Controller password

    Controller gateway

    Controller subnet mask

    License key for NSX

    NSX Manager admin password (basic authorization and SSH)

    NSX Manager enable password

    {
      "nsxManagerSpec" : {
        "name" : "nsx-manager-2",
        "networkDetailsSpec" : {
          "ipAddress" : "10.0.0.44",
          "dnsName" : "nsx-manager-2.sfo01.rainpole.local",
          "gateway" : "10.0.0.250",
          "subnetMask" : "255.255.255.0"
        }
      },
      "nsxVControllerSpec" : {
        "nsxControllerIps" : [ "10.0.0.45", "10.0.0.46", "10.0.0.47" ],
        "nsxControllerPassword" : "Test123456$%",
        "nsxControllerGateway" : "10.0.0.250",
        "nsxControllerSubnetMask" : "255.255.255.0"
      },
      "licenseKey" : "XXXXX-XXXXX-XXXXX-XXXXX-XXXXX",
      "nsxManagerAdminPassword" : "Random0$",
      "nsxManagerEnablePassword" : "Random0$"
    }

  For NSX-T

    NSX Manager virtual machine details

    Name of the NSX Manager virtual machine

    Network details

    IP Address of the virtual machine

    Fully-qualified domain name

    Gateway

    Subnet mask

    Virtual IP address which would act as proxy/alias for NSX-T managers

    Fully-qualified domain name for VIP so that common SSL certificates can be installed across all managers

    License key for NSX

    NSX manager admin Password (basic authorization and SSH)

    {
      "nsxManagerSpecs" : [ {
        "name" : "nsx-manager-2",
        "networkDetailsSpec" : {
          "ipAddress" : "10.0.0.44",
          "dnsName" : "nsx-manager-2.sfo01.rainpole.local",
          "gateway" : "10.0.0.250",
          "subnetMask" : "255.255.255.0"
        }
      }, {
        "name" : "nsx-manager-3",
        "networkDetailsSpec" : {
          "ipAddress" : "10.0.0.44",
          "dnsName" : "nsx-manager-2.sfo01.rainpole.local",
          "gateway" : "10.0.0.250",
          "subnetMask" : "255.255.255.0"
        }
      }, {
        "name" : "nsx-manager-4",
        "networkDetailsSpec" : {
          "ipAddress" : "10.0.0.44",
          "dnsName" : "nsx-manager-2.sfo01.rainpole.local",
          "gateway" : "10.0.0.250",
          "subnetMask" : "255.255.255.0"
        }
      } ],
      "vip" : "10.0.0.166",
      "vipFqdn" : "vip-nsxmanager.sfo01.rainpole.local",
      "licenseKey" : "XXXXX-XXXXX-XXXXX-XXXXX-XXXXX",
      "nsxManagerAdminPassword" : "Random0$"
    }

  WARNING
  NSX details (i.e "nsxVSpec" or "nsxTSpec") must match NSX cluster details (i.e "nsxVClusterSpec" or "nsxTClusterSpec") in the input specification.
  Network pool should be configured.
  TIP
  Refer to Create a Network Pool
  Hosts should be commissioned.
  TIP
  Refer to Commission the Hosts
  A DHCP server must be configured on the VXLAN VLAN of the management domain. When NSX creates VXLAN VTEPs for the domain, they are assigned IP addresses from the DHCP server.

  Ensure that host configuration has a minimum of two active vmNics. There must be a free uplink on each host to be used for the domain.


USAGE:
-----

Sample specification file "domain_creation_spec.json" will be used for domain creation operation. So fill the required details and validate before executing the script.
For more information on the provided sample file, please refer to API reference documentation.

Usage:  python create_domain.py <hostname> <username> <password>
