# Disclaimer
**Disclaimer: The information presented in this article is for educational/general information purposes only. Any scripts or examples are presented where-is, as-is, and are unsupported by Oracle Support and Development. Always refer to the official oracle documentation and other necessary documentation for offical steps. Steps here are not actively maintain, refer to commit dates in git**


# Use OCI LB to front OIC

The below is some high level steps to front oic using oci load balanacer

The example will showing my oic custom url will be bjoic.limboonjian.com instead of the the generated url

https://oiccustom-apacaseanset01-px.integration.us-phoenix-1.ocp.oraclecloud.com/ic/api/integration/v1/flows/rest/TEST/1.0/test?id=hello

https://bjoic.limboonjian.com/ic/api/integration/v1/flows/rest/TEST/1.0/test?id=hello


**You will not be able to access these url as i have block everything when the steps are working.**

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
















