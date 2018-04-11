---
title: Azure Stack 統合システム デプロイのための Azure Stack 公開キー インフラストラクチャ証明書を検証する | Microsoft Docs
description: Azure Stack 統合システムの Azure Stack PKI 証明書を検証する方法について説明します。
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
ms.date: 03/22/2018
ms.author: mabrigg
ms.reviewer: ppacent
ms.openlocfilehash: 1f234c2529213dfa7f42d6ee11b840236488065d
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/03/2018
---
# <a name="validate-azure-stack-pki-certificates"></a>Azure Stack PKI 証明書の検証

この記事で説明する Azure Stack 証明書チェッカー ツールは、deploymentdata.json ファイルに含まれる OEM によって提供されており、[生成された PKI 証明書](azure-stack-get-pki-certs.md)が展開前に適していることを検証します。 証明書は、十分な時間をかけてテストして検証し、必要な場合は再発行します。

証明書チェッカー ツール (Certchecker) は、次のチェックを実行します。

- **PFX の読み取り**。 有効な PFX ファイルと正しいパスワードを確認し、パブリック情報がパスワードによって保護されていない場合は警告します。 
- **署名アルゴリズム**。 署名アルゴリズムが SHA1 ではないことを確認します。
- **秘密キー**。 秘密キーが存在し、ローカル コンピューター属性でエクスポートされることを確認します。 
- **証明書チェーン**。 自己署名証明書の場合も含めて証明書チェーンが損なわれていないことを確認します。 
- **DNS 名**。 SAN に各エンドポイントの関連する DNS 名が含まれていること、またはサポートするワイルドカードが存在するかどうかを確認します。 
- **キー使用法**。 キー使用法にデジタル署名とキーの暗号化が含まれること、および拡張キー使用法にサーバー認証とクライアント認証が含まれることを確認します。
- **キー サイズ**。 キー サイズが 2048 以上であることを確認します。
- **チェーンの順序**。 チェーンを構成している他の証明書の順序が正しいことを確認します。
- **他の証明書**。 関連するリーフ証明書とそのチェーン以外の他の証明書が PFX にパッケージ化されていないことを確認します。
- **プロファイルなし**。 証明書サービスの間に gMSA アカウントの動作を模倣して、ユーザー プロファイルが読み込まれていなくても新しいユーザーが PFX データを読み込めることを確認します。

> [!IMPORTANT]  
> PKI 証明書の PFX ファイルとパスワードは、機密情報として扱われる必要があります。

## <a name="prerequisites"></a>前提条件
Azure Stack 展開に対して PKI 証明書を検証する前に、システムは次の前提条件を満たしている必要があります。
- CertChecker (**\utils\certchecker**  下の **PartnerToolKit**)
- [準備手順](prepare-pki-certs.md)に従ってエクスポートされた SSL 証明書
- DeploymentData.json
- Windows 10 または Windows Server 2016

## <a name="perform-certificate-validation"></a>証明書の検証を実行する

次の手順を使って、Azure Stack PKI 証明書を準備し、検証します。 

1. <partnerToolkit>\utils\certchecker の内容を新しいディレクトリ (**c:\certchecker** など) に抽出します。

2. 管理者として PowerShell を開き、certchecker フォルダーにディレクトリを変更します。

  ```powershell
  cd c:\certchecker
  ```
 
3. 次の PowerShell コマンドを実行して、証明書のディレクトリ構造を作成します。

  ```powershell 
  $directories = "ACS","ADFS","Admin Portal","ARM Admin","ARM Public","Graph","KeyVault","KeyVaultInternal","Public Portal" 
  $destination = '.\certs' 
  $directories | % { New-Item -Path (Join-Path $destination $PSITEM) -ItemType Directory -Force}  
  ```

  >  [!NOTE]
  >  Azure Stack 展開の ID プロバイダーが Azure AD の場合は、**ADFS** および **Graph** ディレクトリを削除します。 

4. 次の例のような、前の手順で作成された適切なディレクトリに証明書を配置します。 
  - c:\certchecker\Certs\ACS\CustomerCertificate.pfx,  
  - c:\certchecker\Certs\Admin Portal\CustomerCertificate.pfx  
  - c:\certchecker\Certs\ARM Admin\CustomerCertificate.pfx  
  - など 

5. **deploymentdata.json** を **c:\certchecker** ディレクトリにコピーします。

6. PowerShell ウィンドウで、次のコマンドを実行します。 

  ```powershell
  $password = Read-Host -Prompt "Enter PFX Password" -AsSecureString 
  .\CertChecker.ps1 -CertificatePath .\Certs\ -pfxPassword $password -deploymentDataJSONPath .\DeploymentData.json  
  ```

7. 出力では、チェックしたすべての証明書とすべての属性が OK になっている必要があります。 

  ```powershell
  Starting Azure Stack Certificate Validation 1.1802.221.1
  Testing: ADFS\ContosoSSL.pfx
    Read PFX: OK
    Signature Algorithm: OK
    Private Key: OK
    Cert Chain: OK
    DNS Names: OK
    Key Usage: OK
    Key Size: OK
    Chain Order: OK
    Other Certificates: OK
    No Profile: OK
  Testing: KeyVaultInternal\ContosoSSL.pfx
    Read PFX: OK
    Signature Algorithm: OK
    Private Key: OK
    Cert Chain: OK
    DNS Names: OK
    Key Usage: OK
    Key Size: OK
    Chain Order: OK
    Other Certificates: OK
    No Profile: OK
  Testing: ACS\ContosoSSL.pfx
  WARNING: Pre-1803 certificate structure. The folder structure for Azure Stack 1803 and above is: ACSBlob, ACSQueue, ACSTable instead of ACS folder. Refer to deployment documentation for further informat
  ion.
    Read PFX: OK
    Signature Algorithm: OK
    Private Key: OK
    Cert Chain: OK
    DNS Names: OK
    Key Usage: OK
    Key Size: OK
    Chain Order: OK
    Other Certificates: OK
    No Profile: OK
  Detailed log can be found C:\CertChecker\CertChecker.log 
  ```

### <a name="known-issues"></a>既知の問題 
**現象**: Certchecker が途中で終了し、次のエラーを受け取る 
> 失敗

> Detail: This command cannot be run due to the error: The directory name is invalid. (詳細: このコマンドはエラーのため実行できません: ディレクトリ名が無効です。) 

**原因**: 制限の厳しいフォルダー (例: c:\temp、%temp%) から certchecker.ps1 を実行しています 

**解決策**: certchecker ツールを新しいディレクトリ (例: C:\CertChecker) に移動します 


**現象**: Certchecker で Pre-1803 の使用に関する警告が発生する (上のステップ 7 の例を参照)

> [!WARNING]
> Pre-1803 certificate structure. The folder structure for Azure Stack 1803 and above is: ACSBlob, ACSQueue, ACSTable instead of ACS folder. For more information see the deployment documentation. (Pre-1803 証明書の構造。Azure Stack 1803 以降のフォルダー構造は ACSBlob、ACSQueue、ACSTable であり、ACS フォルダーではありません。詳細については、展開のドキュメントを参照してください。)

**原因**: Certchecker は単一の ACS フォルダーの使用を検出しましたが、これは 1803 より前の展開で正しいものです。 Azure Stack バージョン 1803 以降の展開では、フォルダー構造は ACSTable、ACSQueue、ACSBlob に変わっています。 Certchecker は、この機能をサポートするように既に更新されています。

**解決策**: 1802 を展開する場合は、対処は必要ありません。 1803 以降を展開する場合は、ACS を ACSTable、ACSQueue、ACSBlob に置き換え、ACS 証明書をこれらのフォルダーにコピーします。

**現象**: テストがスキップされる

**原因**: Certchecker は、依存関係が満たされていない場合、特定のテストをスキップします。
- 証明書チェーンに問題がある場合、他の証明書はスキップされます。
- 次の場合、プロファイルはスキップされません。
  - 一時的なユーザーを作成し、そのユーザーとして PowerShell を実行する機能を制限するセキュリティ ポリシーがある。
  - 秘密キーのチェックが失敗する。

**解決策**: ツール ガイダンスの詳細セクションの各証明書テスト設定に従います。


## <a name="prepare-deployment-script-certificates"></a>展開スクリプト証明書を準備する 
最後のステップとして、準備したすべての証明書を、展開ホストの適切なディレクトリに配置する必要があります。 展開ホストで、 Certificates** という名前のフォルダーを作成し、エクスポートした証明書ファイルを、「[必須の証明書](https://docs.microsoft.com/azure/azure-stack/azure-stack-pki-certs#mandatory-certificates)」セクションで指定されている対応するサブフォルダーに配置します。

```
\Certificates
\ACS\ssl.pfx
\Admin Portal\ssl.pfx
\ARM Admin\ssl.pfx
\ARM Public\ssl.pfx
\KeyVault\ssl.pfx
\KeyVaultInternal\ssl.pfx
\Public Portal\ssl.pfx
\ADFS\ssl.pfx*
\Graph\ssl.pfx*
```

<sup>*</sup> アスタリスク * の付いた証明書は、ID ストアとして AD FS が使われている場合にのみ必要です。


## <a name="next-steps"></a>次の手順
[データセンターの ID の統合](azure-stack-integrate-identity.md)