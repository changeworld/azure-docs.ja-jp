---
title: インクルード ファイル
description: インクルード ファイル
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/12/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: feaf72de1d2c578d2b2d0df9e86ec0fbe0b49445
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "79371754"
---
デバイス トンネルを正常に確立するには、次の要件を満たす必要があります。

* デバイスが、Windows 10 Enterprise または Education バージョン 1809 以降を実行しているドメイン参加済みのコンピューターであること。
* トンネルが、Windows の組み込み VPN ソリューションに対してのみ構成可能で、IKEv2 とコンピューター証明書認証を使用して確立されていること。
* 構成できるデバイス トンネルは、デバイスごとに 1 つのみ。

1. [ポイント対サイト VPN クライアント](../articles/vpn-gateway/point-to-site-how-to-vpn-client-install-azure-cert.md)に関する記事を使用して、Windows 10 クライアントにクライアント証明書をインストールします。 この証明書は、ローカル コンピューターのストアに格納されている必要があります。
1. [この手順](https://docs.microsoft.com/windows-server/remote/remote-access/vpn/vpn-device-tunnel-config#vpn-device-tunnel-configuration)を使用して、VPN プロファイルを作成し、ローカル システム アカウントのコンテキストでデバイス トンネルを構成します。

### <a name="configuration-example-for-device-tunnel"></a>デバイス トンネルの構成例

仮想ネットワーク ゲートウェイを構成し、Windows 10 クライアントのローカル コンピューター ストアにクライアント証明書をインストールしたら、次の例を使用してクライアント デバイス トンネルを構成します。

1. 次のテキストをコピーし、***devicecert.ps1*** として保存します。

   ```
   Param(
   [string]$xmlFilePath,
   [string]$ProfileName
   )

   $a = Test-Path $xmlFilePath
   echo $a

   $ProfileXML = Get-Content $xmlFilePath

   echo $XML

   $ProfileNameEscaped = $ProfileName -replace ' ', '%20'

   $Version = 201606090004

   $ProfileXML = $ProfileXML -replace '<', '&lt;'
   $ProfileXML = $ProfileXML -replace '>', '&gt;'
   $ProfileXML = $ProfileXML -replace '"', '&quot;'

   $nodeCSPURI = './Vendor/MSFT/VPNv2'
   $namespaceName = "root\cimv2\mdm\dmmap"
   $className = "MDM_VPNv2_01"

   $session = New-CimSession

   try
   {
   $newInstance = New-Object Microsoft.Management.Infrastructure.CimInstance $className, $namespaceName
   $property = [Microsoft.Management.Infrastructure.CimProperty]::Create("ParentID", "$nodeCSPURI", 'String', 'Key')
   $newInstance.CimInstanceProperties.Add($property)
   $property = [Microsoft.Management.Infrastructure.CimProperty]::Create("InstanceID", "$ProfileNameEscaped", 'String', 'Key')
   $newInstance.CimInstanceProperties.Add($property)
   $property = [Microsoft.Management.Infrastructure.CimProperty]::Create("ProfileXML", "$ProfileXML", 'String', 'Property')
   $newInstance.CimInstanceProperties.Add($property)

   $session.CreateInstance($namespaceName, $newInstance)
   $Message = "Created $ProfileName profile."
   Write-Host "$Message"
   }
   catch [Exception]
   {
   $Message = "Unable to create $ProfileName profile: $_"
   Write-Host "$Message"
   exit
   }
   $Message = "Complete."
   Write-Host "$Message"
   ```
1. 次のテキストをコピーし、***VPNProfile.xml*** として **devicecert.ps1** と同じフォルダーに保存します。 お使いの環境に合わせて次のテキストを編集します。

   * `<Servers>azuregateway-1234-56-78dc.cloudapp.net</Servers> <= Can be found in the VpnSettings.xml in the downloaded profile zip file`
   * `<Address>192.168.3.5</Address> <= IP of resource in the vnet or the vnet address space`
   * `<Address>192.168.3.4</Address> <= IP of resource in the vnet or the vnet address space`

   ```
   <VPNProfile>  
     <NativeProfile>  
   <Servers>azuregateway-1234-56-78dc.cloudapp.net</Servers>  
   <NativeProtocolType>IKEv2</NativeProtocolType>  
   <Authentication>  
     <MachineMethod>Certificate</MachineMethod>  
   </Authentication>  
   <RoutingPolicyType>SplitTunnel</RoutingPolicyType>  
    <!-- disable the addition of a class based route for the assigned IP address on the VPN interface -->
   <DisableClassBasedDefaultRoute>true</DisableClassBasedDefaultRoute>  
     </NativeProfile> 
     <!-- use host routes(/32) to prevent routing conflicts -->  
     <Route>  
   <Address>192.168.3.5</Address>  
   <PrefixSize>32</PrefixSize>  
     </Route>  
     <Route>  
   <Address>192.168.3.4</Address>  
   <PrefixSize>32</PrefixSize>  
     </Route>  
   <!-- need to specify always on = true --> 
     <AlwaysOn>true</AlwaysOn> 
   <!-- new node to specify that this is a device tunnel -->  
    <DeviceTunnel>true</DeviceTunnel>
   <!--new node to register client IP address in DNS to enable manage out -->
   <RegisterDNS>true</RegisterDNS>
   </VPNProfile>
   ```
1. [Sysinternals](https://docs.microsoft.com/sysinternals/downloads/psexec) から **PsExec** をダウンロードして、**C:\PSTools** にファイルを抽出します。
1. 管理者コマンド プロンプトから、次を実行して PowerShell を起動します。

   ```
   PsExec.exe Powershell for 32-bit Windows
   PsExec64.exe Powershell for 64-bit Windows
   ```

   ![powershell](./media/vpn-gateway-vwan-always-on-device/powershell.png)
1. PowerShell で、**devicecert.ps1** と **VPNProfile.xml** が配置されているフォルダーに切り替えて、次のコマンドを実行します。

   ```powershell
   .\devicecert.ps1 .\VPNProfile.xml MachineCertTest
   ```
   
   ![MachineCertTest](./media/vpn-gateway-vwan-always-on-device/machinecerttest.png)
1. **rasphone** を実行します。

   ![rasphone](./media/vpn-gateway-vwan-always-on-device/rasphone.png)
1. **MachineCertTest** エントリを探して、 **[接続]** をクリックします。

   ![接続する](./media/vpn-gateway-vwan-always-on-device/connect.png)
1. 接続に成功したら、コンピューターを再起動します。 トンネルが自動的に接続されます。
