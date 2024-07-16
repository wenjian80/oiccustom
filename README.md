# Disclaimer
**Disclaimer: The information presented in this article is for educational/general information purposes only. Any scripts or examples are presented where-is, as-is, and are unsupported by Oracle Support and Development. Always refer to the official oracle documentation and other necessary documentation for offical steps. Steps here are not actively maintain, refer to commit dates in git**


# Use OCI LB to front OIC

The below is some high level steps to front oic using oci load balanacer

The example will showing my oic custom url will be bjoic.limboonjian.com instead of the the generated url

https://oiccustom-apacaseanset01-px.integration.us-phoenix-1.ocp.oraclecloud.com/ic/api/integration/v1/flows/rest/TEST/1.0/test?id=hello

https://bjoic.limboonjian.com/ic/api/integration/v1/flows/rest/TEST/1.0/test?id=hello


** You will not be able to access these url as i have block everything when the steps are working.

# Create domain

The tutorial will assume you already have a domain name. In here my domain name is limboonjian.com and is already registered in oci dns zone.

HOw to managed dns is not cover in this instructions.

# Create a cert

I am using https://sslforweb.com/ to create a free cert for my domain bjoic.limboonjian.com

Follow the steps there to register the txt record etc.


After which download the necessary cert which we going to use later.

![enter image description here](https://github.com/wenjian80/oiccustom/blob/main/customssl.JPG)

# Download oic certificates.

Access and login to the oic console  and download the certs using firefox, we will be using it later.

![enter image description here](https://github.com/wenjian80/oiccustom/blob/main/downloadcer1.JPG)

![enter image description here](https://github.com/wenjian80/oiccustom/blob/main/downloadcer2.JPG)


# VCN routing rules

Before we create the load balancer make sure that the routing rules is added.

The below show that it route to oic public ip using the nat.

![enter image description here](https://github.com/wenjian80/oiccustom/blob/main/nat.JPG)

# Load balancer set up

Set up the hostname of the custom url.

![enter image description here](https://github.com/wenjian80/oiccustom/blob/main/hostname.JPG)

We need to set up 2 custom ssl.

In customssl, that is the ssl for bjoic.limboonjian.com, you will upload the cert, ca, and private key in eariler steps

In oicbackend, that is the ssl for the back oic url, you will upload the cert and ca in eariler steps

![enter image description here](https://github.com/wenjian80/oiccustom/blob/main/addssl.JPG)

Create a backend set, and you need to tick using ssl.

![enter image description here](https://github.com/wenjian80/oiccustom/blob/main/backendset1.JPG)

Configure the backend to the oic, make sure it is healthy

![enter image description here](https://github.com/wenjian80/oiccustom/blob/main/check1.JPG)

![enter image description here](https://github.com/wenjian80/oiccustom/blob/main/check2.JPG)

Create the listerner and use the custom host name and cert that was created eariler.

![enter image description here](https://github.com/wenjian80/oiccustom/blob/main/listerner.JPG)

# Update oci cutom end point

Update oic custom endpoint

![enter image description here](https://github.com/wenjian80/oiccustom/blob/main/customendpoint.JPG)


# Update oci dns zone and point the domain to my load balancer ip

![enter image description here](https://github.com/wenjian80/oiccustom/blob/main/dnszone.JPG)


# Test using post man using custom name

![enter image description here](https://github.com/wenjian80/oiccustom/blob/main/testikng.JPG)



















Some public doc/reference to refer to

https://learn.microsoft.com/en-us/azure/vpn-gateway/tutorial-site-to-site-portal

https://learn.microsoft.com/en-us/azure/vpn-gateway/vpn-gateway-about-compliance-crypto

https://docs.oracle.com/en-us/iaas/Content/Network/Tasks/managingIPsec.htm

https://docs.oracle.com/en-us/iaas/Content/Network/Reference/supportedIPsecparams.htm#Supported_IPSec_Parameters

https://docs.oracle.com/en-us/iaas/Content/Network/Tasks/vpn_to_azure.htm


BGP ipsec

https://videohub.oracle.com/media/VPN+Connectivity+between+Azure+and+OCI/1_bmtogfh2


If latency/bandiwidth is a concern, then use megaport/equinix etc to establish a dedicated connection using oci fast connect and azure fast route. Both azure and oci are connect via megaport/equinix etc router Steps are not documented in here. Refer to below documentation

https://learn.microsoft.com/en-us/azure/virtual-wan/virtual-wan-expressroute-portal

https://docs.oracle.com/en-us/iaas/Content/Network/Concepts/fastconnect.htm

# Ipsec Topology set up with vnet
![enter image description here](https://github.com/wenjian80/azurevnetociipsec/blob/main/ipsec.jpg)


# fastconnect Topology set up
![enter image description here](https://github.com/wenjian80/azurevnetociipsec/blob/main/fastconnect.JPG)

# Steps

# High level steps

1. vng create at azure side [azure] [around 15-30 min]
2. create a cpe device in oci from step 1 [oci] [depend on step 1] [around 1-5 min]
3. create ip sec in oci using step 2 cpe device [oci] [depend on step 2]  [around 1-5 min]
4. create lng connection from oci ipsec tunnel ip from step 3 [azure] [depend on step 3]  [around 15-30 min]
5. change ike algo settings in azure [azure] [around 1-5 min] 
6. change ike algo settings in oci [oci] [around 1-5 min] 
7. check status [azure,oci] [around 1-5 min] 
8. configure oci and azure routing rules accordingly [oci,azure] 



## 1. Create vng with no bgp enable

Vng is already create in azure side.

![enter image description here](https://github.com/wenjian80/azurevnetociipsec/blob/main/staticvng1.JPG)

![enter image description here](https://github.com/wenjian80/azurevnetociipsec/blob/main/staticvng2.JPG)


## 2. Get vpn gateway ip

Click on vng overview

In this case this is 20.59.110.12

![enter image description here](https://github.com/wenjian80/azurevnetociipsec/blob/main/staticvng1.JPG)


## 3. Create cpe device on oci.

Using the vpn gateway azure ip that is step 2, create a cpe device in oci.

![enter image description here](https://github.com/wenjian80/azurevnetociipsec/blob/main/cpe.JPG)


## 4. OCI ip sec creation

Create a ipsec in oci.

We put in the route of azure cidr 10.0.0.0/16 for example to azure vnet cidr

We choose static route and choose ikev2. The rest we leave it as default

![enter image description here](https://github.com/wenjian80/azurevnetociipsec/blob/main/ipsec1.JPG)

![enter image description here](https://github.com/wenjian80/azurevnetociipsec/blob/main/ipsec2.JPG)

![enter image description here](https://github.com/wenjian80/azurevnetociipsec/blob/main/ipsec3.JPG)


## 5. Get ipsec tunnel 1 ip

After the ipsec is created jot down the tunnel 1 ip which is 129.146.231.27. We are going to create in azure lng for this.

![enter image description here](https://github.com/wenjian80/azurevnetociipsec/blob/main/t1ip.JPG)


## 6. Create a local network gateway 

Create an lng which point to step 5 tunnel 1 ip. And the address space is oci cidr range for example 10.42.0.0/16

![enter image description here](https://github.com/wenjian80/azurevnetociipsec/blob/main/lng.JPG)

![enter image description here](https://github.com/wenjian80/azurevnetociipsec/blob/main/lng2.JPG)

## 7. Create a connection in azure to tied to the lng and vng

Create a connection and tied to lng and vng.

![enter image description here](https://github.com/wenjian80/azurevnetociipsec/blob/main/connection1.JPG)

![enter image description here](https://github.com/wenjian80/azurevnetociipsec/blob/main/connection2.JPG)

## 7. Change ike custom algo in azure

Change ike alog in azure

![enter image description here](https://github.com/wenjian80/azurevnetociipsec/blob/main/algoazure.JPG)


## 8. Change oci tunnel information

Go to oci tunnel 1 and change the custom algo as well.

![enter image description here](https://github.com/wenjian80/azurevnetociipsec/blob/main/algo1.JPG)

![enter image description here](https://github.com/wenjian80/azurevnetociipsec/blob/main/algo2.JPG)


## 9. Check status

Wait for a while and communicnation will be up

![enter image description here](https://github.com/wenjian80/azurevnetociipsec/blob/main/up1.JPG)

![enter image description here](https://github.com/wenjian80/azurevnetociipsec/blob/main/up2.JPG)


## 10. Confiugre route in azure and oci accordingly

Confiugre route in azure accordingly.

configure oci vcn route table and your security list accordingly.

Test the communication.

