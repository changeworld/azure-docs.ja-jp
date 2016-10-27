<properties
    pageTitle="Cloud Services FAQ | Microsoft Azure"
    description="Frequently asked questions about Cloud Services."
    services="cloud-services"
    documentationCenter=""
    authors="Thraka"
    manager="timlt"
    editor=""/>

<tags
    ms.service="cloud-services"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/19/2016"
    ms.author="adegeo"/>


# <a name="cloud-services-faq"></a>Cloud Services FAQ
This article answers some frequently asked questions about Microsoft Azure Cloud Services. You can also visit the [Azure Support FAQ](http://go.microsoft.com/fwlink/?LinkID=185083) for general Azure pricing and support information. You can also consult the [Cloud Services VM Size page](cloud-services-sizes-specs.md) for size information.

## <a name="certificates"></a>Certificates

### <a name="where-should-i-install-my-certificate?"></a>Where should I install my certificate?

- **My**  
Application Certificate with private key (\*.pfx, \*.p12).

- **CA**  
All your intermediate certificates go in this store (Policy and Sub CAs).

- **ROOT**  
The root CA store, so your main root CA cert should go here.

### <a name="i-can't-remove-expired-certificate"></a>I can't remove expired certificate

Azure prevents you from removing a certificate while it is in use. You need to either delete the deployment that uses the certificate, or update the deployment with a different or renewed certificate.

### <a name="delete-an-expired-certificate"></a>Delete an expired certificate

As long as the certificate is not in use, you can use the [Remove-AzureCertificate](https://msdn.microsoft.com/library/azure/mt589145.aspx) PowerShell cmdlet to remove a certificate.

### <a name="i-have-expired-certificates-named-windows-azure-service-management-for-extensions"></a>I have expired certificates named Windows Azure Service Management for Extensions

These certificates are created whenever an extension is added to the cloud service such as the Remote Desktop extension. These certificates are only used for encrypting and decrypting the private configuration of the extension. It does not matter if these certificates expire. The expiration date is not checked.

### <a name="certificates-i-have-deleted-keep-reappearing"></a>Certificates I have deleted keep reappearing

These keep reappearing most likely because of a tool you're using, such as Visual Studio. Whenever you reconnect with a tool that is using a certificate, it will again be uploaded to Azure.

### <a name="my-certificates-keep-disappearing"></a>My certificates keep disappearing

When the virtual machine instance recycles, all local changes are lost. Use a [startup task](cloud-services-startup-tasks.md) to install certificates to the virtual machine each time the role starts.

### <a name="i-cannot-find-my-management-certificates-in-the-portal"></a>I cannot find my management certificates in the portal

[Management certificates](..\azure-api-management-certs.md) are only avialable in the Azure Classic Portal. The current Azure portal does not use management certificates. 

### <a name="how-can-i-disable-a-management-certificate?"></a>How can I disable a management certificate?

[Management certificates](..\azure-api-management-certs.md) cannot be disabled. You delete them through the Azure Classic Portal when you do not want them to be used anymore.

### <a name="how-do-i-create-an-ssl-certificate-for-a-specific-ip-address?"></a>How do I create an SSL certificate for a specific IP address?

Follow the directions in the [create a certificate tutorial](cloud-services-certs-create.md). Use the IP address as the DNS Name.

## <a name="troubleshooting"></a>Troubleshooting

### <a name="i-can't-reserve-an-ip-in-a-multi-vip-cloud-service"></a>I can't reserve an IP in a multi-VIP cloud service

First, make sure that the virtual machine instance that you're trying to reserve the IP for is turned on. Second, make sure that you're using Reserved IPs for bother the staging and production deployments. **Do not** change the settings while the deployment is upgrading.




<!--HONumber=Oct16_HO2-->


