---
title: Always-On VPN ユーザー トンネルの構成
titleSuffix: Azure VPN Gateway
description: この記事では、VPN ゲートウェイに対して Always On VPN ユーザー トンネルを構成する方法について説明します。
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 10/02/2019
ms.author: cherylmc
ms.openlocfilehash: bff2ed48a78bfbae984dea5e5474971817023bc6
ms.sourcegitcommit: c32050b936e0ac9db136b05d4d696e92fefdf068
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/08/2020
ms.locfileid: "75729323"
---
# <a name="configure-an-always-on-vpn-user-tunnel"></a>Always On VPN ユーザー トンネルの構成

Windows 10 VPN クライアントの新機能である Always On は、VPN 接続を維持する機能です。 Always On を使用すると、アクティブな VPN プロファイルは、ユーザーのサインイン、ネットワーク状態の変更、またはデバイス画面のアクティブ化などのトリガーに基づいて、自動的に接続し、接続を維持することができます。

Azure 仮想ネットワーク ゲートウェイを Windows 10 Always On と共に使用することで、Azure への永続的なユーザー トンネルとデバイス トンネルを確立できます。 この記事は、Always On VPN ユーザー トンネルの構成に役立ちます。

Always On VPN 接続には、次の 2 種類のトンネルのいずれかが含まれます。

* **デバイス トンネル**:ユーザーがデバイスにサインインする前に、指定した VPN サーバーに接続します。 サインイン前の接続シナリオとデバイス管理ではデバイス トンネルが使用されます。

* **ユーザー トンネル**:ユーザーがデバイスにサインインした後にのみ接続します。 ユーザー トンネルを使用すると、ユーザーは VPN サーバー経由で組織のリソースにアクセスできます。

デバイス トンネルとユーザー トンネルは、VPN プロファイルとは関係なく動作します。 これらは同時に接続でき、必要に応じてさまざまな認証方法と他の VPN の構成設定を使用できます。

次のセクションでは、VPN ゲートウェイとユーザー トンネルを構成します。

## <a name="step-1-configure-a-vpn-gateway"></a>手順 1:VPN ゲートウェイの構成

IKEv2 および証明書ベースの認証を使用するように VPN ゲートウェイを構成するには、こちらの[ポイント対サイト](vpn-gateway-howto-point-to-site-resource-manager-portal.md)に関する記事の説明に従ってください。

## <a name="step-2-configure-a-user-tunnel"></a>手順 2:ユーザー トンネルを構成する

1. こちらの[ポイント対サイト VPN クライアント](point-to-site-how-to-vpn-client-install-azure-cert.md)に関する記事に示されているように、Windows 10 クライアントにクライアント証明書をインストールします。 証明書は、現在のユーザー ストアに存在する必要があります。

1. PowerShell、System Center Configuration Manager、または Intune を使用して Always On VPN を構成するには、「[Windows 10 クライアントの Always On VPN 接続を構成する](https://docs.microsoft.com/windows-server/remote/remote-access/vpn/always-on-vpn/deploy/vpn-deploy-client-vpn-connections)」の手順に従ってください。

### <a name="example-configuration-for-the-user-tunnel"></a>ユーザー トンネルの構成例

仮想ネットワーク ゲートウェイを構成し、Windows 10 クライアントのローカル コンピューター ストアにクライアント証明書をインストールしたら、次の例を使用してクライアント デバイス トンネルを構成します。

1. 次のテキストをコピーし、*usercert.ps1* として保存します。

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
1. 次のテキストをコピーし、*VPNProfile.xml* として *usercert.ps1* と同じフォルダーに保存します。 お使いの環境に合わせて次のテキストを編集します。

   * `<Servers>azuregateway-1234-56-78dc.cloudapp.net</Servers>  <= Can be found in the VpnSettings.xml in the downloaded profile zip file`
   * `<Address>192.168.3.5</Address>  <= IP of resource in the vnet or the vnet address space`
   * `<Address>192.168.3.4</Address>  <= IP of resource in the vnet or the vnet address space`
   * `<PrefixSize>32</PrefixSize>     <= Subnet mask`

   ```
    <VPNProfile>  
      <NativeProfile>  
    <Servers>azuregateway-b115055e-0882-49bc-a9b9-7de45cba12c0-8e6946892333.vpn.azure.com</Servers>  
    <NativeProtocolType>IKEv2</NativeProtocolType>  
    <Authentication>  
    <UserMethod>Eap</UserMethod>
    <Eap>
    <Configuration>
    <EapHostConfig xmlns="http://www.microsoft.com/provisioning/EapHostConfig"><EapMethod><Type xmlns="http://www.microsoft.com/provisioning/EapCommon">13</Type><VendorId xmlns="http://www.microsoft.com/provisioning/EapCommon">0</VendorId><VendorType xmlns="http://www.microsoft.com/provisioning/EapCommon">0</VendorType><AuthorId xmlns="http://www.microsoft.com/provisioning/EapCommon">0</AuthorId></EapMethod><Config xmlns="http://www.microsoft.com/provisioning/EapHostConfig"><Eap xmlns="http://www.microsoft.com/provisioning/BaseEapConnectionPropertiesV1"><Type>13</Type><EapType xmlns="http://www.microsoft.com/provisioning/EapTlsConnectionPropertiesV1"><CredentialsSource><CertificateStore><SimpleCertSelection>true</SimpleCertSelection></CertificateStore></CredentialsSource><ServerValidation><DisableUserPromptForServerValidation>false</DisableUserPromptForServerValidation><ServerNames></ServerNames></ServerValidation><DifferentUsername>false</DifferentUsername><PerformServerValidation xmlns="http://www.microsoft.com/provisioning/EapTlsConnectionPropertiesV2">false</PerformServerValidation><AcceptServerName xmlns="http://www.microsoft.com/provisioning/EapTlsConnectionPropertiesV2">false</AcceptServerName></EapType></Eap></Config></EapHostConfig>
    </Configuration>
    </Eap>
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
    <!-- traffic filters for the routes specified above so that only this traffic can go over the device tunnel --> 
      <TrafficFilter>  
    <RemoteAddressRanges>192.168.3.4, 192.168.3.5</RemoteAddressRanges>  
      </TrafficFilter>
    <!-- need to specify always on = true --> 
    <AlwaysOn>true</AlwaysOn>
    <RememberCredentials>true</RememberCredentials>
    <!--new node to register client IP address in DNS to enable manage out -->
    <RegisterDNS>true</RegisterDNS>
    </VPNProfile>
   ```
1. PowerShell を管理者として実行します。

1. PowerShell で、*usercert.ps1* と *VPNProfile.xml* が配置されているフォルダーに切り替えて、次のコマンドを実行します。

   ```powershell
   C:\> .\usercert.ps1 .\VPNProfile.xml UserTest
   ```
   
   ![MachineCertTest](./media/vpn-gateway-howto-always-on-user-tunnel/p2s2.jpg)
1. **[VPN 設定]** で、**UserTest** エントリを探して、 **[接続]** を選択します。

1. 接続に成功した場合は、Always On ユーザー トンネルは正常に構成されています。

## <a name="clean-up-your-resources"></a>リソースをクリーンアップする

プロファイルを削除するには、次を実行します。

1. 次のコマンドを実行します。

   ```powershell
   C:\> Remove-VpnConnection UserTest  
   ```

1. 接続を切断し、 **[Connect automatically]\(自動的に接続する\)** チェック ボックスをオフにします。

![クリーンアップ](./media/vpn-gateway-howto-always-on-user-tunnel/p2s4..jpg)

## <a name="next-steps"></a>次のステップ

発生する可能性のある接続の問題をトラブルシューティングするには、「[Azure ポイント対サイト接続の問題](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md)」を参照してください。
