---
title: Azure Stack でシークレットをローテーションする | Microsoft Docs
description: Azure Stack でシークレットをローテーションする方法について説明します。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/15/2018
ms.author: mabrigg
ms.reviewer: ppacent
ms.openlocfilehash: 8ac151a70a81f78dab5ed1f30df51a1121a42cbd
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/27/2018
ms.locfileid: "37029018"
---
# <a name="rotate-secrets-in-azure-stack"></a>Azure Stack でシークレットをローテーションする

*この記事の説明は、Azure Stack 統合システム バージョン 1803 以降に対してのみ適用されます。1802 以前のバージョンの Azure Stack ではシークレットのローテーションを試みないでください。*

Azure Stack は、さまざまなシークレットを使って、Azure Stack インフラストラクチャのリソースとサービス間のセキュリティ保護された通信を維持します。

- **内部シークレット**  
Azure Stack オペレーターの介入なしに Azure Stack インフラストラクチャによって使われるすべての証明書、パスワード、セキュリティで保護された文字列、およびキー。 

- **外部シークレット**  
Azure Stack オペレーターによって提供される、外部に接続されるサービス用のインフラストラクチャ サービス証明書。 これには、次のサービスの証明書が含まれます。 
    - 管理者ポータル 
    - パブリック ポータル 
    - 管理者の Azure Resource Manager 
    - グローバルな Azure Resource Manager 
    - 管理者 KeyVault 
    - KeyVault 
    - ACS (Blob、Table、Queue Storage を含みます) 
    - ADFS<sup>*</sup>
    - Graph<sup>*</sup>

    > <sup>*</sup> 環境の ID プロバイダーが Active Directory フェデレーション サービス (AD FS) の場合にのみ適用されます。

> [!NOTE]
> BMC やスイッチ パスワードなどの他のすべてのセキュリティで保護されたキーと文字列、ユーザーおよび管理者アカウントのパスワードは、まだ管理者が手動で更新します。 

Azure Stack インフラストラクチャの整合性を維持するため、オペレーターは、組織のセキュリティ要件と一致する頻度でインフラストラクチャのシークレットを定期的にローテーションできる必要があります。

### <a name="rotating-secrets-with-external-certificates-from-a-new-certificate-authority"></a>新しい証明書機関からの外部証明書を使用してシークレットをローテーションする

Azure Stack では、次のようなコンテキストで、新しい証明書機関 (CA) からの外部証明書を使用したシークレットのローテーションをサポートしています。

|インストール済みの証明書 CA|ローテーション先の CA|サポートされています|サポートされる Azure Stack のバージョン|
|-----|-----|-----|-----|-----|
|自己署名済みから|Enterprise へ|サポートされていません||
|自己署名済みから|自己署名済みへ|サポートされていません||
|自己署名済みから|公開へ<sup>*</sup>|サポートされています|1803 以降|
|Enterprise から|Enterprise へ|顧客がデプロイ時に使用したのと同じエンタープライズ CA を使用している限りサポートされる|1803 以降|
|Enterprise から|自己署名済みへ|サポートされていません||
|Enterprise から|公開へ<sup>*</sup>|サポートされています|1803 以降|
|公開から<sup>*</sup>|Enterprise へ|サポートされていません|1803 以降|
|公開から<sup>*</sup>|自己署名済みへ|サポートされていません||
|公開から<sup>*</sup>|公開へ<sup>*</sup>|サポートされています|1803 以降|

<sup>*</sup> ここでの公開証明機関は、Windows の信頼されたルート プログラムに含まれているものです。 [Microsoft の信頼されたルート証明書プログラム: 参加者 (2017 年 6 月 27 日時点)](https://gallery.technet.microsoft.com/Trusted-Root-Certificate-123665ca) に完全な一覧があります。

## <a name="alert-remediation"></a>アラートの修復

シークレットの有効期限まで 30 日以内になると、管理者ポータルで次のアラートが生成されます。 

- 保留中のサービス アカウントのパスワードの有効期限 
- 保留中の内部証明書の有効期限 
- 保留中の外部証明書の有効期限 

以下の手順を使ってシークレットのローテーションを実行すると、これらのアラートは修復されます。

## <a name="pre-steps-for-secret-rotation"></a>シークレット ローテーションの事前手順

1.  メンテナンス操作をユーザーに通知します。 可能な限り非営業時間中に、通常のメンテナンス期間をスケジュールします。 メンテナンス操作は、ユーザーのワークロードとポータル操作の両方に影響を及ぼす可能性があります。

    > [!note]  
    > 次の手順は、Azure Stack 外部シークレットのローテーションに対してのみ適用されます。

2. シークレット ローテーションが過去 1 か月以内に環境内で正常に実行されていないことを確認します。 現在、Azure Stack でサポートされているシークレット ローテーションは、1 か月につき 1 回のみです。 
3. 代わりの外部証明書の新しいセットを準備します。 新しいセットは、「[Azure Stack 公開キー インフラストラクチャ証明書の要件](https://docs.microsoft.com/azure/azure-stack/azure-stack-pki-certs)」で説明されている証明書の仕様と一致します。
4.  ローテーションに使われる証明書のバックアップを安全なバックアップ場所に格納します。 ローテーションを実行して失敗した場合は、ローテーションを再実行する前に、ファイル共有内の証明書をバックアップ コピーに置き換えます。 バックアップ コピーはセキュリティで保護されたバックアップ場所に保存するよう注意してください。
5.  ERCS VM からアクセスできるファイル共有を作成します。 ファイル共有は、**CloudAdmin** ID で読み書きできる必要があります。
6.  ファイル共有にアクセスできるコンピューターから PowerShell ISE コンソールを開きます。 ファイル共有に移動します。 
7.  **[CertDirectoryMaker.ps1](http://www.aka.ms/azssecretrotationhelper)** を実行して、外部証明書に必要なディレクトリを作成します。

## <a name="rotating-external-and-internal-secrets"></a>外部および内部シークレットのローテーション

外部と内部両方のシークレットのローテーションを行うには:

1. 前の手順で新しく作成した **/Certificates** ディレクトリに、「[Azure Stack 公開キー インフラストラクチャ証明書の要件](https://docs.microsoft.com/azure/azure-stack/azure-stack-pki-certs#mandatory-certificates)」の「必須の証明書」セクションに記載されている形式に従ったディレクトリ構造で、交換用の外部証明書の新しいセットを配置します。
2. **CloudAdmin** アカウント使って[特権エンドポイント](https://docs.microsoft.com/azure/azure-stack/azure-stack-privileged-endpoint)で PowerShell セッションを作成し、セッションを変数として格納します。 次の手順で、この変数をパラメーターとして使います。

    > [!IMPORTANT]  
    > セッションには入らず、セッションを変数として格納してください。
    
3. **[invoke-command](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/invoke-command?view=powershell-5.1)** を実行します。 特権エンドポイントの PowerShell セッション変数を **Session** パラメーターとして渡します。 
4. 次のパラメーターで **Start-SecretRotation** を実行します。
    - **PfxFilesPath**  
    前に作成した証明書ディレクトリへのネットワーク パスを指定します。  
    - **PathAccessCredential**  
    共有に対する資格情報の PSCredential オブジェクトです。 
    - **CertificatePassword**  
    作成されるすべての pfx 証明書ファイルに使われるパスワードのセキュリティで保護された文字列です。
4. シークレットのローテーションが済むまで待ちます。  
シークレットのローテーションが正常に完了すると、コンソールに **[Overall action status: Success]\(全体的なアクションの状態: 成功\)** と表示されます。 
5. シークレットのローテーションが正常に完了した後、事前手順で作成した共有から証明書を削除し、セキュリティで保護されたバックアップ場所に保存します。 

## <a name="walkthrough-of-secret-rotation"></a>シークレットのローテーションのチュートリアル

次の PowerShell の例では、シークレットのローテーションを行うために実行するコマンドレットとパラメーターを示します。

```powershell
#Create a PEP Session
winrm s winrm/config/client '@{TrustedHosts= "<IPofERCSMachine>"}'
$PEPCreds = Get-Credential 
$PEPsession = New-PSSession -computername <IPofERCSMachine> -Credential $PEPCreds -ConfigurationName PrivilegedEndpoint 

#Run Secret Rotation
$CertPassword = ConvertTo-SecureString "Certpasswordhere" -AsPlainText -Force
$CertShareCred = Get-Credential 
$CertSharePath = "<NetworkPathofCertShare>"
Invoke-Command -session $PEPsession -ScriptBlock { 
Start-SecretRotation -PfxFilesPath $using:CertSharePath -PathAccessCredential $using:CertShareCred -CertificatePassword $using:CertPassword }
Remove-PSSession -Session $PEPSession
```
## <a name="rotating-only-internal-secrets"></a>内部シークレットのみのローテーション

Azure Stack の内部シークレットのローテーションを行うには:

1. [特権エンドポイント](https://docs.microsoft.com/azure/azure-stack/azure-stack-privileged-endpoint)で PowerShell セッションを作成します。
2. 特権エンドポイント セッションで、引数を指定せずに **Start-SecretRotation** を実行します。

## <a name="start-secretrotation-reference"></a>Start-SecretRotation のリファレンス

Azure Stack システムのシークレットのローテーションを行います。 Azure Stack の特権エンドポイントに対してのみ実行されます。

### <a name="syntax"></a>構文

パス (既定値)

```powershell
Start-SecretRotation [-PfxFilesPath <string>] [-PathAccessCredential] <PSCredential> [-CertificatePassword <SecureString>]  
```

### <a name="description"></a>説明

Start-SecretRotation コマンドレットは、Azure Stack システムのインフラストラクチャ シークレットのローテーションを行います。 既定では内部インフラストラクチャ ネットワークに公開されているすべてのシークレットのローテーションが行われ、ユーザーが入力するとすべての外部ネットワーク インフラストラクチャ エンドポイントの証明書のローテーションも行われます。 外部ネットワーク インフラストラクチャ エンドポイントのローテーションを行う場合は、Invoke-Command スクリプト ブロックで Start-SecretRotation を実行し、Azure Stack 環境の特権エンドポイント セッションを session パラメーターで渡す必要があります。
 
### <a name="parameters"></a>parameters

| パラメーター | type | 必須 | 位置 | 既定値 | 説明 |
| -- | -- | -- | -- | -- | -- |
| PfxFilesPath | String  | False  | named  | なし  | すべての外部ネットワーク エンドポイント証明書を含む **\Certificates** ディレクトリへのファイル共有パスです。 内部と外部のシークレットのローテーションを行う場合にのみ必要です。 最後のディレクトリは **\Certificates** にする必要があります。 |
| CertificatePassword | SecureString | False  | named  | なし  | -PfXFilesPath で提供されているすべての証明書のパスワード。 内部と外部両方のシークレットのローテーションを行うときに PfxFilesPath を指定する場合は、必須の値です。 |
|

### <a name="examples"></a>例
 
**内部インフラストラクチャ シークレットのみのローテーション**

```powershell  
PS C:\> Start-SecretRotation  
```

このコマンドは、Azure Stack 内部ネットワークに公開されているすべてのインフラストラクチャ シークレットのローテーションを行います。 Start-SecretRotation は、スタックで生成されたすべてのシークレットのローテーションを行いますが、証明書が指定されていないため、外部エンドポイント証明書のローテーションは行われません。  

**内部と外部のインフラストラクチャ シークレットのローテーション**
  
```powershell
PS C:\> Invoke-Command -session $PEPSession -ScriptBlock { 
Start-SecretRotation -PfxFilesPath $using:CertSharePath -PathAccessCredential $using:CertShareCred -CertificatePassword $using:CertPassword } 
Remove-PSSession -Session $PEPSession
```

このコマンドは、Azure Stack 内部ネットワークに公開されているすべてのインフラストラクチャ シークレットと、Azure Stack の外部ネットワーク インフラストラクチャ エンドポイントに使われる TLS 証明書の、ローテーションを行います。 Start-SecretRotation は、スタックで生成されたすべてのシークレットのローテーションを行い、証明書が指定されているため、外部エンドポイント証明書のローテーションも行われます。  


## <a name="update-the-baseboard-management-controller-bmc-password"></a>ベースボード管理コントローラー (BMC) のパスワードを更新する

ベースボード管理コントローラー (BMC) は、サーバーの物理的な状態を監視します。 BMC のパスワードの更新に関する仕様や手順は、ご利用の OEM (Original Equipment Manufacturer) ハードウェア ベンダーによって異なります。 Azure Stack コンポーネントのパスワードを定期的に更新する必要があります。

1. OEM の指示に従って、Azure Stack の物理サーバー上で BMC を更新します。 環境内の各 BMC のパスワードは同じである必要があります。
2. Azure Stack セッションで特権エンドポイントを開きます。 手順については、「[Azure Stack での特権エンドポイントの使用](azure-stack-privileged-endpoint.md)」を参照してください。
3. PowerShell プロンプトが環境に応じて **[IP address or ERCS VM name]: PS>** または **[azs-ercs01]: PS>** に変化したら、`invoke-command` を実行することによって `Set-BmcPassword` を実行します。 パラメーターとして特権エンドポイントのセッション変数を渡します。 例: 

    ```powershell
    # Interactive Version
    $PEip = "<Privileged Endpoint IP or Name>" # You can also use the machine name instead of IP here.
    $PECred = Get-Credential "<Domain>\CloudAdmin" -Message "PE Credentials" 
    $NewBMCpwd = Read-Host -Prompt "Enter New BMC password" -AsSecureString 

    $PEPSession = New-PSSession -ComputerName $PEip -Credential $PECred -ConfigurationName "PrivilegedEndpoint" 

    Invoke-Command -Session $PEPSession -ScriptBlock {
        Set-Bmcpassword -bmcpassword $using:NewBMCpwd
    }
    Remove-PSSession -Session $PEPSession
    ```
    
    パスワードをコード行として含む静的な PowerShell バージョンを使用することもできます。
    
    ```powershell
    # Static Version
    $PEip = "<Privileged Endpoint IP or Name>" # You can also use the machine name instead of IP here.
    $PEUser = "<Privileged Endpoint user for exmaple Domain\CloudAdmin>"
    $PEpwd = ConvertTo-SecureString "<Privileged Endpoint Password>" -AsPlainText -Force
    $PECred = New-Object System.Management.Automation.PSCredential ($PEUser, $PEpwd) 
    $NewBMCpwd = ConvertTo-SecureString "<New BMC Password>" -AsPlainText -Force 

    $PEPSession = New-PSSession -ComputerName $PEip -Credential $PECred -ConfigurationName "PrivilegedEndpoint" 

    Invoke-Command -Session $PEPSession -ScriptBlock {
        Set-Bmcpassword -bmcpassword $using:NewBMCpwd
    }
    Remove-PSSession -Session $PEPSession
    ```

## <a name="next-steps"></a>次の手順

[Azure Stack のセキュリティについて詳しく学習する](azure-stack-security-foundations.md)
