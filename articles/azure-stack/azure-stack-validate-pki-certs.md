---
title: Azure Stack 統合システム デプロイのための Azure Stack 公開キー インフラストラクチャ証明書を検証する | Microsoft Docs
description: Azure Stack 統合システムの Azure Stack PKI 証明書を検証する方法について説明します。 Azure Stack 証明書チェッカー ツールの使用方法について説明します。
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
ms.date: 04/11/2018
ms.author: mabrigg
ms.reviewer: ppacent
ms.openlocfilehash: cd917165804314f6ee4ee006e3f29263d8d4b4c5
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/16/2018
---
# <a name="validate-azure-stack-pki-certificates"></a>Azure Stack PKI 証明書の検証

この記事で説明されている Azure Stack 適合性チェッカー ツールは、[PowerShell ギャラリーから](https://aka.ms/AzsReadinessChecker)入手可能です。 デプロイ前にこのツールを使用して、[生成された PKI 証明書](azure-stack-get-pki-certs.md)が適切であるかどうかを検証できます。 テストまで十分な時間を確保して証明書を検証する必要があります。必要に応じて証明書を再発行します。

適合性チェッカー ツールでは、次の証明書の検証を実行します。

- **PFX の読み取り**  
    有効な PFX ファイルと正しいパスワードを確認し、パブリック情報がパスワードによって保護されていない場合は警告します。 
- **署名アルゴリズム**  
    署名アルゴリズムが SHA1 ではないことを確認します。
- **秘密キー**  
    秘密キーが存在し、ローカル コンピューター属性でエクスポートされることを確認します。 
- **証明書チェーン**  
    自己署名証明書の場合も含めて証明書チェーンが損なわれていないことを確認します。
- **DNS 名**  
    SAN に各エンドポイントの関連する DNS 名が含まれていること、またはサポートするワイルドカードが存在するかどうかを確認します。
- **キー使用法**  
    キー使用法にデジタル署名とキーの暗号化が含まれること、および拡張キー使用法にサーバー認証とクライアント認証が含まれることを確認します。
- **キーサイズ**  
    キー サイズが 2048 以上であることを確認します。
- **チェーンの順序**  
    他の証明書の順序を確認して、順序が正しいことを検証します。
- **他の証明書**  
    関連するリーフ証明書とそのチェーン以外の他の証明書が PFX にパッケージ化されていないことを確認します。

> [!IMPORTANT]  
> PKI 証明書が PFX ファイルです。パスワードは機密情報として扱われる必要があります。

## <a name="prerequisites"></a>前提条件

Azure Stack のデプロイに対して PKI 証明書を検証する前に、システムは次の前提条件を満たしている必要があります。

- Microsoft Azure Stack 適合性チェッカー
- [準備手順](azure-stack-prepare-pki-certs.md)に従ってエクスポートされた SSL 証明書
- DeploymentData.json
- Windows 10 または Windows Server 2016

## <a name="perform-certificate-validation"></a>証明書の検証を実行する

次の手順を使って、Azure Stack PKI 証明書を準備し、検証します。

1. 次のコマンドレットを実行して、PowerShell プロンプト (5.1 以上) から AzsReadinessChecker をインストールします。

    ````PowerShell  
        Install-Module Microsoft.AzureStack.ReadinessChecker 
    ````

2. 証明書ディレクトリ構造を作成します。 次の例では、`<c:\certificates>` を、選択した新規ディレクトリ パスに変更できます。

    ````PowerShell  
    New-Item C:\Certificates -ItemType Directory

    $directories = 'ACSBlob','ACSQueue','ACSTable','ADFS','Admin Portal','ARM Admin','ARM Public','Graph','KeyVault','KeyVaultInternal','Public Portal' 

    $destination = 'c:\certificates' 

    $directories | % { New-Item -Path (Join-Path $destination $PSITEM) -ItemType Directory -Force}  
    ````

 - 前の手順で作成された適切なディレクトリに証明書を配置します。 例:   
    - c:\certificates\ACSBlob\CustomerCertificate.pfx 
    - c:\certificates\Certs\Admin Portal\CustomerCertificate.pfx 
    - c:\certificates\Certs\ARM Admin\CustomerCertificate.pfx 
    - など 

3. PowerShell ウィンドウで、次を実行します。

    ````PowerShell  
    $pfxPassword = Read-Host -Prompt "Enter PFX Password" -AsSecureString

    Start-AzsReadinessChecker -CertificatePath c:\certificates -pfxPassword $pfxPassword -RegionName east -FQDN azurestack.contoso.com -IdentitySystem AAD
    ````

4. 出力ですべての証明書がテストに合格したことを確認します。 例: 

    ````PowerShell
    AzsReadinessChecker v1.1803.405.3 started
    Starting Certificate Validation

    Starting Azure Stack Certificate Validation 1.1803.405.3
    Testing: ARM Public\ssl.pfx
        Read PFX: OK
        Signature Algorithm: OK
        Private Key: OK
        Cert Chain: OK
        DNS Names: OK
        Key Usage: OK
        Key Size: OK
        Chain Order: OK
        Other Certificates: OK
    Testing: ACSBlob\ssl.pfx
        Read PFX: OK
        Signature Algorithm: OK
        Private Key: OK
        Cert Chain: OK
        DNS Names: OK
        Key Usage: OK
        Key Size: OK
        Chain Order: OK
        Other Certificates: OK
    Detailed log can be found C:\AzsReadinessChecker\CertificateValidation\CertChecker.log

    Finished Certificate Validation

    AzsReadinessChecker Log location: C:\AzsReadinessChecker\AzsReadinessChecker.log
    AzsReadinessChecker Report location (for OEM): C:\AzsReadinessChecker\AzsReadinessReport.json
    AzsReadinessChecker Completed
    ````

### <a name="known-issues"></a>既知の問題

**現象**: テストがスキップされる

**原因**: AzsReadinessChecker は、依存関係が満たされていない場合、特定のテストをスキップします。

 - 証明書チェーンに問題がある場合、他の証明書はスキップされます。

    ````PowerShell  
    Testing: ACSBlob\singlewildcard.pfx
        Read PFX: OK
        Signature Algorithm: OK
        Private Key: OK
        Cert Chain: OK
        DNS Names: Fail
        Key Usage: OK
        Key Size: OK
        Chain Order: OK
        Other Certificates: Skipped
    Details:
    The certificate records '*.east.azurestack.contoso.com' do not contain a record that is valid for '*.blob.east.azurestack.contoso.com'. Please refer to the documentation for how to create the required certificate file.
    The Other Certificates check was skipped because Cert Chain and/or DNS Names failed. Follow the guidance to remediate those issues and recheck. 
    Detailed log can be found C:\AzsReadinessChecker\CertificateValidation\CertChecker.log

    Finished Certificate Validation

    AzsReadinessChecker Log location: C:\AzsReadinessChecker\AzsReadinessChecker.log
    AzsReadinessChecker Report location (for OEM): C:\AzsReadinessChecker\AzsReadinessChecker.log
    AzsReadinessChecker Completed
    ````

**解決策**: ツール ガイダンスの詳細セクションにある各証明書テスト設定に従います。

## <a name="using-validated-certificates"></a>検証済み証明書の使用

証明書が AzsReadinessChecker によって検証されたら、Azure Stack デプロイまたは Azure Stack シークレット ローテーションで使用できます。 

 - デプロイの場合、[Azure Stack PKI の要件ドキュメント](azure-stack-pki-certs.md)で指定されているように、デプロイメント エンジニアが証明書を配置ホストにコピーできるように、セキュリティを保護して証明書をデプロイメント エンジニアに送付します。
 - シークレット ローテーションの場合、[Azure Stack シークレット ローテーションのドキュメント](azure-stack-rotate-secrets.md)の説明に従い、この証明書を使用して、お使いの Azure Stack 環境のパブリック インフラストラクチャ エンドポイントの古い証明書を更新できます。

## <a name="next-steps"></a>次の手順

[データセンターの ID の統合](azure-stack-integrate-identity.md)
