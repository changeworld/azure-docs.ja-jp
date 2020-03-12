---
title: TLS 1.2 をサポートしていないアプリケーションによって発生するイシューのトラブルシューティング | Microsoft Docs
description: TLS 1.2 をサポートしていないアプリケーションによって発生するイシューのトラブルシューティング
services: cloud-services
documentationcenter: ''
author: mimckitt
manager: vashan
editor: ''
tags: top-support-issue
ms.assetid: ''
ms.service: cloud-services
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: ''
ms.date: 01/17/2020
ms.author: tagore
ms.openlocfilehash: a9d15a94421694583562f433c20413fcc84697c7
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2020
ms.locfileid: "78270863"
---
# <a name="troubleshooting-applications-that-dont-support-tls-12"></a>TLS 1.2 をサポートしていないアプリケーションのトラブルシューティング
この記事では、古い TLS プロトコル (TLS 1.0 および 1.1) を有効にする方法と、Windows Server 2019 クラウド サービスの Web ロールと worker ロールで追加のプロトコルをサポートするためのレガシの暗号スイートの適用方法について説明します。 

TLS 1.0 と TLS 1.1 を非推奨にするステップを実行しますが、お客様が廃止を計画するまで、古いプロトコルと暗号スイートをサポートする必要があることを理解しています。  これらのレガシの値を再度有効にすることはお勧めしませんが、Microsoft はお客様を支援するためのガイダンスを提供しています。 この記事に記載されている変更を実装する前に、回帰のリスクを評価することをお勧めします。 

> [!NOTE]
> ゲスト OS ファミリ 6 リリースでは、1.0 または 1.1 の暗号を無効にすることにより、TLS 1.2 が適用されます。 


## <a name="dropping-support-for-tls-10-tls-11-and-older-cipher-suites"></a>TLS 1.0、TLS 1.1、および古い暗号スイートのサポートの終了 
Microsoft は、クラス最高レベルの暗号化を使用するというコミットメントに対応するため、2017 年 6 月に TLS 1.0 および 1.1 からの移行を開始する計画を発表しました。   この最初の発表以来、2020 年の上半期には Microsoft Edge および Internet Explorer 11 のサポートされているバージョンで、既定でトランスポート層セキュリティ (TLS) 1.0 と 1.1 を無効にすることを発表しています。  Apple、Google、および Mozilla の同様の発表からも、業界の方向性がわかります。   

## <a name="tls-configuration"></a>TLS の構成  
Windows Server 2019 クラウド サーバー イメージは、TLS 1.0 と TLS 1.1 を使用して構成されていますが、これらはレジストリ レベルで無効になっています。 つまり、このバージョンの Windows に展開され、TLS ネゴシエーションに Windows スタックを使用しているアプリケーションは、TLS 1.0 および TLS 1.1 の通信を許可しません。   

サーバーには、次のような暗号スイートの制限されたセットも付属します。 

```
    TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256 
    TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384 
    TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 
    TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384 
    TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256 
    TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384 
    TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256 
    TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384 
```

## <a name="step-1-create-the-powershell-script-to-enable-tls-10-and-tls-11"></a>手順 1:TLS 1.0 および TLS 1.1 を有効にする PowerShell スクリプトを作成する 

次のコードを例として使用し、古いプロトコルと暗号スイートを有効にするスクリプトを作成します。 このドキュメントでは、このスクリプトの名前は次のようになります: **TLSsettings.ps1**。 このスクリプトは、後の手順で簡単にアクセスできるように、ローカル デスクトップに保存します。 


```Powershell
#******************* FUNCTION THAT ACTUALLY UPDATES KEYS; WILL RETURN REBOOT FLAG IF CHANGES *********************** 
 
Function Set-CryptoSetting {  
    param (  
        $regKeyName,  
        $value,  
        $valuedata,  
        $valuetype       
    )  
    
    $restart = $false 
  
    # Check for existence of registry key, and create if it does not exist  
    If (!(Test-Path -Path $regKeyName)) {  
        New-Item $regKeyName | Out-Null  
    }  
 
    # Get data of registry value, or null if it does not exist  
    $val = (Get-ItemProperty -Path $regKeyName -Name $value -ErrorAction SilentlyContinue).$value  
 
    If ($val -eq $null) {  
        # Value does not exist - create and set to desired value  
        New-ItemProperty -Path $regKeyName -Name $value -Value $valuedata -PropertyType $valuetype | Out-Null  
        $restart = $true 
    } 
 
    Else {  
        # Value does exist - if not equal to desired value, change it  
        If ($val -ne $valuedata) {  
            Set-ItemProperty -Path $regKeyName -Name $value -Value $valuedata  
            $restart = $true  
        }  
    }  
 
    $restart  
}  
 
#*************************************************************************************************************** 
 
#****************************** CIPHERSUITES FOR OS VERSIONS WINDOWS 10 AND ABOVE ****************************** 
 
function Get-BaseCipherSuitesWin10Above() 
{ 
        $cipherorder += "TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256," 
        $cipherorder += "TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384," 
        $cipherorder += "TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256," 
        $cipherorder += "TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384," 
        $cipherorder += "TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256," 
        $cipherorder += "TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384," 
        $cipherorder += "TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256," 
        $cipherorder += "TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384," 
 
# Legacy cipher suites 
        $cipherorder += "TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA_P256," 
        $cipherorder += "TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA_P256," 
        $cipherorder += "TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA_P256," 
        $cipherorder += "TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA_P256," 
        $cipherorder += "TLS_RSA_WITH_AES_256_GCM_SHA384,"  
        $cipherorder += "TLS_RSA_WITH_AES_128_GCM_SHA256,"  
        $cipherorder += "TLS_RSA_WITH_AES_256_CBC_SHA256,"  
        $cipherorder += "TLS_RSA_WITH_AES_128_CBC_SHA256,"  
        $cipherorder += "TLS_RSA_WITH_AES_256_CBC_SHA," 
        $cipherorder += "TLS_RSA_WITH_AES_128_CBC_SHA" 
 
 return $cipherorder 
} 
 

#*************************************************************************************************************** 
 
 
#********************************************** REGISTRY KEYS **************************************************** 
 
 
function Get-RegKeyPathToEnable() 
{ 
    $regKeyPath = @( 
        "HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2",         
        "HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client",  
        "HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server" , 
        "HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1",  
        "HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Client", 
        "HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Server" , 
        "HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0",  
        "HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Client",  
        "HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Server" 
    ) 
    return $regKeyPath 
} 
 
#*************************************************************************************************************** 
 
$localRegistryPath = @() 
 
# Enable TLS 1.2, TLS 1.1 and TLS 1.0 
$localRegistryPath += Get-RegKeyPathToEnable 
 
#******************* CREATE THE REGISTRY KEYS IF THEY DON'T EXIST******************************** 
 
# Check for existence of the registry keys, and create if they do not exist  
For ($i = 0; $i -lt $localRegistryPath.Length; $i = $i + 1) {  
   Write-Host "Checking for existing of key: $($localRegistryPath[$i]) Severity Level: Information"
   If (!(Test-Path -Path $localRegistryPath[$i])) {
        New-Item $localRegistryPath [$i] | Out-Null
    Write-Host "Creating key: $($localRegistryPath[$i]) Severity Level: Information"
    }
}  
 
#********************************* EXPLICITLY Enable TLS12,  TLS11 and TLS10********************************* 
 
For ($i = 0; $i -lt $localRegistryPath.Length; $i = $i + 1) { 
    if ($localRegistryPath[$i].Contains("Client") -Or $localRegistryPath[$i].Contains("Server")) { 
      Write-Host "Enabling this key: $($localRegistryPath[$i]) Severity: Information "
        $result = Set-CryptoSetting $localRegistryPath[$i].ToString() Enabled 1 DWord   
        $result = Set-CryptoSetting $localRegistryPath[$i].ToString() DisabledByDefault 0 DWord  
        $reboot = $reboot -or $result 
    } 
} 
 
#**************************************** SET THE CIPHER SUITE ORDER******************************** 
 
$cipherlist = @() 
 
# Set cipher suite order 
$cipherlist += Get-BaseCipherSuitesWin10Above 
$CipherSuiteRegKey = "HKLM:\SOFTWARE\Policies\Microsoft\Cryptography\Configuration\SSL\00010002"  
 
if (!(Test-Path -Path $CipherSuiteRegKey))  
{  
    New-Item $CipherSuiteRegKey | Out-Null  
    $reboot = $True  
    Write-Host "Creating key: $($CipherSuiteRegKey) Severity: Information "
}  
 
#Set-ItemProperty -Path $CipherSuiteRegKey -Name Functions -Value $cipherorder  
Set-ItemProperty -Path $CipherSuiteRegKey -Name Functions -Value $cipherlist  
#********************************************* REBOOT ******************************************* 
 
Write-Host "A reboot is required in order for changes to effect"  
Write-Host "Rebooting now..."  
shutdown.exe /r /t 5 /c "Crypto settings changed" /f /d p:2:4
```

## <a name="step-2-create-a-command-file"></a>手順 2:コマンド ファイルを作成する 

以下を使用して **RunTLSSettings.cmd** という名前の CMD ファイルを作成します。 このスクリプトは、後の手順で簡単にアクセスできるように、ローカル デスクトップに保存します。 

```cmd
PowerShell -ExecutionPolicy Unrestricted %~dp0TLSsettings.ps1
REM This line is required to ensure the startup tasks does not block the role from starting in case of error.  DO NOT REMOVE!!!! 
EXIT /B 0
```

## <a name="step-3-add-the-startup-task-to-the-roles-service-definition-csdef"></a>手順 3:ロールのサービス定義 (csdef) にスタートアップ タスクを追加する 

次のスニペットを既存のサービス定義ファイルに追加します。 


```
    <Startup> 
        <Task executionContext="elevated" taskType="simple" commandLine="RunTLSSettings.cmd"> 
        </Task> 
    </Startup> 
```

worker ロールと Web ロールの両方を示す例を次に示します。 

```
<?xmlversion="1.0"encoding="utf-8"?> 
<ServiceDefinitionname="CloudServiceName"xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition"schemaVersion="2015-04.2.6"> 
    <WebRolename="WebRole1"vmsize="Standard_D1_v2"> 
        <Sites> 
            <Sitename="Web"> 
                <Bindings> 
                    <Bindingname="Endpoint1"endpointName="Endpoint1"/> 
                </Bindings> 
            </Site> 
        </Sites> 
        <Startup> 
            <Taske xecutionContext="elevated" taskType="simple" commandLine="RunTLSSettings.cmd"> 
            </Task> 
        </Startup> 
        <Endpoints> 
            <InputEndpointname="Endpoint1"protocol="http"port="80"/> 
        </Endpoints> 
    </WebRole> 
<WorkerRolename="WorkerRole1"vmsize="Standard_D1_v2"> 
    <Startup> 
        <Task executionContext="elevated" taskType="simple" commandLine="RunTLSSettings.cmd"> 
        </Task> 
    </Startup> 
</WorkerRole> 
</ServiceDefinition> 
```

## <a name="step-5-add-the-scripts-to-your-cloud-service"></a>手順 5:クラウド サービスにスクリプトを追加する 

1) Visual Studio で、該当する WebRole を右クリックします。
2) **[追加]** を選択します。
3) **[既存の項目]** を選択します。
4) エクスプローラーで、**TLSsettings.ps1** および **RunTLSSettings.cmd** ファイルを保存したデスクトップに移動します。 
5) 2 つのファイルを選択し、Cloud Services プロジェクトに追加します。

## <a name="step-6-enable-copy-to-output-directory"></a>手順 6:出力ディレクトリへのコピーを有効にする

Visual Studio からプッシュされた更新プログラムと共にスクリプトが確実にアップロードされるようにするには、 *[出力ディレクトリにコピー]* を *[常にコピーする]* に設定する必要があります。

1) 該当する WebRole で、RunTLSSettings.cmd を右クリックします。
2) **[プロパティ]** を選択します。
3) [プロパティ] タブの *[出力ディレクトリにコピー]* を *[常にコピーする]* に変更します。
4) **TLSsettings.ps1** の手順を繰り返します。

## <a name="step-7-publish--validate"></a>手順 7:発行と検証

これで上記の手順が完了したので、既存のクラウド サービスに更新プログラムを発行します。 

[SSLLabs](https://www.ssllabs.com/) を使用して、エンドポイントの TLS の状態を検証することができます 

 
