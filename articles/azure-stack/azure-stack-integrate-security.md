---
title: Azure Stack の Syslog 転送
description: Syslog 転送を使用して、Azure Stack と監視ソリューションを統合する方法の詳細
services: azure-stack
author: PatAltimore
manager: femila
ms.service: azure-stack
ms.topic: article
ms.date: 08/14/2018
ms.author: patricka
ms.reviewer: fiseraci
keywords: ''
ms.openlocfilehash: 8e59f2e7e2fceda7f30e12571cd9e2a552f76231
ms.sourcegitcommit: 4ea0cea46d8b607acd7d128e1fd4a23454aa43ee
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/15/2018
ms.locfileid: "41947961"
---
# <a name="azure-stack-datacenter-integration---syslog-forwarding"></a>Azure Stack データセンターの統合 - Syslog 転送

この記事では、Syslog を使用して Azure Stack インフラストラクチャを、データセンターに既にデプロイされている外部セキュリティ ソリューションと統合する方法を説明します。 たとえば、セキュリティ情報イベント管理 (SIEM) システムなどです。 Syslog チャネルは、Azure Stack インフラストラクチャのすべてのコンポーネントからの監査、アラート、セキュリティ ログを公開します。 セキュリティ監視ソリューションと統合するため、および保存して保持できるようすべての監査、アラート、セキュリティ ログを取得するため、Syslog 転送を使用します。 

1805 更新プログラム以降、Azure Stack には統合された Syslog クライアントが装備され、このクライアントを構成すると、共通イベント形式 (CEF) のペイロードによる Syslog メッセージが送信されます。 

> [!IMPORTANT]
> Syslog 転送はプレビュー段階です。 運用環境では使用しないでください。 

次の図は、Syslog 統合に関与する主要なコンポーネントを示します。

![Syslog 転送の図](media/azure-stack-integrate-security/syslog-forwarding.png)

## <a name="configuring-syslog-forwarding"></a>Syslog 転送の構成

Azure Stack の Syslog クライアントは、次の構成をサポートしています。

1. **相互認証 (クライアントとサーバー) と TLS 1.2 暗号化を使用した TCP 経由の Syslog:** この構成では、Syslog サーバーと Syslog クライアントの両方が証明書を介して相互に ID を確認できます。 メッセージは、TLS 1.2 暗号化チャネル経由で送信されます。

2. **サーバー認証と TLS 1.2 暗号化を使用した TCP 経由の Syslog:** この構成では、Syslog クライアントは証明書を介して Syslog サーバーの ID を確認できます。 メッセージは、TLS 1.2 暗号化チャネル経由で送信されます。

3. **暗号化なしの TCP 経由の Syslog:** この構成では、Syslog クライアントも Syslog サーバーもお互いの ID を確認しません。 メッセージは、TCP を介してクリア テキストで送信されます。

4. **暗号化なしの UDP を介した Syslog:** この構成では、Syslog クライアントも Syslog サーバーもお互いの ID を確認しません。 メッセージは、UDP を介してクリア テキストで送信されます。

> [!IMPORTANT]
> Microsoft は、中間者攻撃やメッセージの傍受からの保護のため、運用環境では、認証と暗号化を使用して TCP を使用する (構成 1 か最低でも 2) よう強くお勧めします。

### <a name="cmdlets-to-configure-syslog-forwarding"></a>Syslog 転送を構成するためのコマンドレット
Syslog 転送を構成するには、特権エンドポイント (PEP) へのアクセスが必要です。 Syslog 転送を構成するために、次の 2 つの PowerShell コマンドレットが PEP に追加されています。


```powershell
### cmdlet to pass the syslog server information to the client and to configure the transport protocol, the encryption and the authentication between the client and the server

Set-SyslogServer [-ServerName <String>] [-NoEncryption] [-SkipCertificateCheck] [-SkipCNCheck] [-UseUDP] [-Remove]

### cmdlet to configure the certificate for the syslog client to authenticate with the server

Set-SyslogClient [-pfxBinary <Byte[]>] [-CertPassword <SecureString>] [-RemoveCertificate] 
```
#### <a name="cmdlets-parameters"></a>コマンドレットのパラメーター

*Set-SyslogServer* コマンドレットのパラメーター:

| パラメーター | 説明 | type |
|---------|---------| ---------|
| *ServerName* | Syslog サーバーの FQDN または IP アドレス | String |
|*NoEncryption*| クライアントに Syslog メッセージを強制的にクリア テキストで送信させます | フラグ | 
|*SkipCertificateCheck*| 初期 TLS ハンドシェイク時に Syslog サーバーによって提供された証明書の検証をスキップします | フラグ |
|*SkipCNCheck*| 初期 TLS ハンドシェイク時に Syslog サーバーによって提供された証明書の共通名値の検証をスキップします | フラグ |
|*UseUDP*| トランスポート プロトコルとして UDP を使用して、Syslog を使用します |フラグ |
|*Remove*| クライアントからサーバーの構成を削除し、Syslog 転送を停止します| フラグ |

*Set-SyslogClient* コマンドレットのパラメーター:
| パラメーター | 説明 | type |
|---------|---------| ---------|
| *pfxBinary* | Syslog サーバーに対する認証のためにクライアントが ID として使用する証明書が入った pfx ファイル  | Byte[] |
| *CertPassword* |  pfx ファイルに関連付けられている秘密キーをインポートするためのパスワード | SecureString |
|*RemoveCertificate* | クライアントから証明書を削除します | フラグ|

### <a name="configuring-syslog-forwarding-with-tcp-mutual-authentication-and-tls-12-encryption"></a>TCP、相互認証、TLS 1.2 暗号化を使用した Syslog 転送の構成

この構成では、Azure Stack の Syslog クライアントから、TLS 1.2 暗号化を使用して TCP 経由で Syslog サーバーにメッセージが転送されます。 初期ハンドシェイク時には、有効で信頼されている証明書がサーバーから提供されていることが、クライアントにより確認されます。同様に、クライアントからも、自分の ID の証明となる証明書がサーバーに提供されます。 この構成は、クライアントとサーバーの ID がどちらも完全に検証され、メッセージが暗号化されたチャネルで送信されるため、最も安全です。 

> [!IMPORTANT]
> Microsoft は、運用環境ではこの構成を使用するよう強くお勧めします。 

TCP、相互認証、TLS 1.2 暗号化を使用した Syslog 転送を構成するには、次の両方のコマンドレットを実行します。
```powershell
# Configure the server
Set-SyslogServer -ServerName <FQDN or ip address of syslog server>

# Provide certificate to the client to authenticate against the server
Set-SyslogClient -pfxBinary <Byte[] of pfx file> -CertPassword <SecureString, password for accessing the pfx file>
```
クライアント証明書には、Azure Stack のデプロイ中に提供されたものと同じルートが必要です。 これには秘密キーを含める必要もあります。

```powershell
##Example on how to set your syslog client with the ceritificate for mutual authentication. 
##Run these cmdlets from your hardware lifecycle host or privileged access workstation.

$ErcsNodeName = "<yourPEP>"
$password = ConvertTo-SecureString -String "<your cloudAdmin account password" -AsPlainText -Force
 
$cloudAdmin = "<your cloudAdmin account name>"
$CloudAdminCred = New-Object System.Management.Automation.PSCredential ($cloudAdmin, $password)
 
$certPassword = $password
$certContent = Get-Content -Path C:\cert\<yourClientCertificate>.pfx -Encoding Byte
 
$params = @{ 
    ComputerName = $ErcsNodeName 
    Credential = $CloudAdminCred 
    ConfigurationName = "PrivilegedEndpoint" 
}

$session = New-PSSession @params
 
$params = @{ 
    Session = $session 
    ArgumentList = @($certContent, $certPassword) 
}
Write-Verbose "Invoking cmdlet to set syslog client certificate..." -Verbose 
Invoke-Command @params -ScriptBlock { 
    param($CertContent, $CertPassword) 
    Set-SyslogClient -PfxBinary $CertContent -CertPassword $CertPassword 
```

### <a name="configuring-syslog-forwarding-with-tcp-server-authentication-and-tls-12-encryption"></a>TCP、サーバー認証、TLS 1.2 暗号化を使用した Syslog 転送の構成

この構成では、Azure Stack の Syslog クライアントから、TLS 1.2 暗号化を使用して TCP 経由で Syslog サーバーにメッセージが転送されます。 初期ハンドシェイク時には、有効で信頼された証明書がサーバーから提供されていることも、クライアントによって確認されます。 これにより、信頼されていない宛先にクライアントからメッセージを送信することができなくなります。
認証と暗号化を使用する TCP は既定の構成であり、Microsoft が運用環境にお勧めする最小限のセキュリティのレベルです。 

```powershell
Set-SyslogServer -ServerName <FQDN or ip address of syslog server>
```

Syslog サーバーと Azure Stack クライアントの統合をテストするときに、自己署名証明書や信頼されていない証明書を使用する場合は、次のフラグを使用して、初期ハンドシェイク時にクライアントによって実行されるサーバー検証をスキップできます。

```powershell
 #Skip validation of the Common Name value in the server certificate. Use this flag if you provide an IP address for your syslog server
 Set-SyslogServer -ServerName <FQDN or ip address of syslog server> -SkipCNCheck
 
 #Skip entirely the server certificate validation
 Set-SyslogServer -ServerName <FQDN or ip address of syslog server> -SkipCertificateCheck
```
> [!IMPORTANT]
> Microsoft は、運用環境には -SkipCertificateCheck フラグを使用しないようお勧めします。 


### <a name="configuring-syslog-forwarding-with-tcp-and-no-encryption"></a>TCP を使用した暗号化なしの Syslog 転送の構成

この構成では、Azure Stack の Syslog クライアントから、暗号化を使用せずに TCP 経由で Syslog サーバーにメッセージが転送されます。 クライアントによってサーバーの ID が確認されることはなく、クライアントから検証用に自分の ID がサーバーに提供されることもありません。 

```powershell
Set-SyslogServer -ServerName <FQDN or ip address of syslog server> -NoEncryption
```
> [!IMPORTANT]
> Microsoft は、運用環境ではこの構成を使用しないようお勧めします。 


### <a name="configuring-syslog-forwarding-with-udp-and-no-encryption"></a>UDP を使用した暗号化なしの Syslog 転送の構成

この構成では、Azure Stack の Syslog クライアントから、暗号化を使用せずに UDP 経由で Syslog サーバーにメッセージが転送されます。 クライアントによってサーバーの ID が確認されることはなく、クライアントから検証用に自分の ID がサーバーに提供されることもありません。 

```powershell
Set-SyslogServer -ServerName <FQDN or ip address of syslog server> -UseUDP
```
暗号化を使用しない UDP は構成が最も簡単ですが、中間者攻撃やメッセージの傍受に対する保護機能はありません。 

> [!IMPORTANT]
> Microsoft は、運用環境ではこの構成を使用しないようお勧めします。 


## <a name="removing-syslog-forwarding-configuration"></a>Syslog 転送構成の削除

Syslog サーバー構成を完全に削除し、Syslog 転送を停止するには、次を実行します。

**クライアントから Syslog サーバー構成を削除する**

```PowerShell  
Set-SyslogServer -Remove
```

**クライアントからクライアント証明書を削除する**

```PowerShell  
Set-SyslogClient -RemoveCertificate
```

## <a name="verifying-the-syslog-setup"></a>Syslog のセットアップの確認

Syslog クライアントを Syslog サーバーに接続することに成功したら、すぐにイベントの受信を開始しなければなりません。 イベントが何も表示されない場合は、次のコマンドレットを実行して、Syslog クライアントの構成を確認します。

**Syslog クライアントのサーバー構成を確認する**

```PowerShell  
Get-SyslogServer
```

**Syslog クライアントの証明書のセットアップを確認する**

```PowerShell  
Get-SyslogClient
```

## <a name="syslog-message-schema"></a>Syslog メッセージ スキーマ

Azure Stack インフラストラクチャの Syslog 転送では、共通イベント形式 (CEF) で書式設定されたメッセージが送信されます。
各 Syslog メッセージの構造は、次のスキーマに基づいています。 

```Syslog
<Time> <Host> <CEF payload>
```

CEF ペイロードは、以下の構造に基づいていますが、各フィールドのマッピングは、メッセージの種類 (Windows イベント、作成されたアラート、解決されたアラート) によって異なります。

```CEF
# Common Event Format schema
CEF: <Version>|<Device Vendor>|<Device Product>|<Device Version>|<Signature ID>|<Name>|<Severity>|<Extensions>
* Version: 0.0 
* Device Vendor: Microsoft
* Device Product: Microsoft Azure Stack
* Device Version: 1.0
```

### <a name="cef-mapping-for-windows-events"></a>Windows イベントの CEF マッピング
```
* Signature ID: ProviderName:EventID
* Name: TaskName
* Severity: Level (for details, see the severity table below)
* Extension: Custom Extension Name (for details, see the Custom Extension table below)
```

Windows イベントの重大度の表: 
| CEF 重大度の値 | Windows イベント レベル | 数値 |
|--------------------|---------------------| ----------------|
|0|Undefined|値: 0。 すべてのレベルのログを示します|
|10|重大|値: 1。 重大なアラートのログを示します|
|8|Error| 値： 2。 エラーのログを示します|
|5|警告|値: 3。 警告のログを示します|
|2|情報|値: 4。 情報メッセージのログを示します|
|0|詳細|値: 5。 すべてのレベルのログを示します|

Azure Stack における Windows イベントのカスタム拡張機能の表:
| カスタム拡張機能名 | Windows イベントの例 | 
|-----------------------|---------|
|MasChannel | システム|
|MasComputer | test.azurestack.contoso.com|
|MasCorrelationActivityID| C8F40D7C-3764-423B-A4FA-C994442238AF|
|MasCorrelationRelatedActivityID| C8F40D7C-3764-423B-A4FA-C994442238AF|
|MasEventData| svchost!!4132,G,0!!!!EseDiskFlushConsistency!!ESENT!!0x800000|
|MasEventDescription| ユーザーのグループ ポリシー設定は正しく処理されました。 前回グループ ポリシーが正しく処理されてからの変更は検出されませんでした。|
|MasEventID|1501|
|MasEventRecordID|26637|
|MasExecutionProcessID | 29380|
|MasExecutionThreadID |25480|
|MasKeywords |0x8000000000000000|
|MasKeywordName |成功の監査|
|MasLevel |4|
|MasOpcode |1|
|MasOpcodeName |info|
|MasProviderEventSourceName ||
|MasProviderGuid |AEA1B4FA-97D1-45F2-A64C-4D69FFFD92C9|
|MasProviderName |Microsoft-Windows-GroupPolicy|
|MasSecurityUserId |\<Windows SID\> |
|MasTask |0|
|MasTaskCategory| プロセス作成|
|MasUserData|KB4093112!!5112!!Installed!!0x0!!WindowsUpdateAgent Xpath: /Event/UserData/*|
|MasVersion|0|

### <a name="cef-mapping-for-alerts-created"></a>作成されたアラートの CEF マッピング
```
* Signature ID: Microsoft Azure Stack Alert Creation : FaultTypeId
* Name: FaultTypeId : AlertId
* Severity: Alert Severity (for details, see alerts severity table below)
* Extension: Custom Extension Name (for details, see the Custom Extension table below)
```
アラートの重大度の表:
| severity | Level |
|----------|-------|
|0|Undefined|
|10|重大|
|5|警告|

Azure Stack における作成されたアラートのカスタム拡張機能の表:
| カスタム拡張機能名 | 例 | 
|-----------------------|---------|
|MasEventDescription|説明: ユーザー アカウント \<TestUser\> が \<TestDomain\> に対して作成されました。 これは潜在的なセキュリティ リスクです。 -- 解決策: サポートにお問い合わせください。 この問題を解決するにはカスタマー サポートが必要です。 サポートを受けずに、この問題を解決しようとしないでください。 サポート要求を開く前に、https://aka.ms/azurestacklogfiles のガイダンスを使用してログ ファイルの収集プロセスを開始してください |

### <a name="cef-mapping-for-alerts-closed"></a>解決されたアラートの CEF マッピング
```
* Signature ID: Microsoft Azure Stack Alert Creation : FaultTypeId
* Name: FaultTypeId : AlertId
* Severity: Information
```

次の例は、CEF ペイロードによる Syslog メッセージを示しています。
```
2018:05:17:-23:59:28 -07:00 TestHost CEF:0.0|Microsoft|Microsoft Azure Stack|1.0|3|TITLE: User Account Created -- DESCRIPTION: A user account \<TestUser\> was created for \<TestDomain\>. It's a potential security risk. -- REMEDIATION: Please contact Support. Customer Assistance is required to resolve this issue. Do not try to resolve this issue without their assistance. Before you open a support request, start the log file collection process using the guidance from https://aka.ms/azurestacklogfiles|10
```
## <a name="next-steps"></a>次の手順

[サービス ポリシー](azure-stack-servicing-policy.md)