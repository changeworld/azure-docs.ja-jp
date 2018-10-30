---
title: Azure Stack の ADFS 統合を検証する
description: Azure Stack 適合性チェッカーを使用して、Azure Stack の ADFS 統合を検証します。
services: azure-stack
documentationcenter: ''
author: PatAltimore
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/22/2018
ms.author: patricka
ms.reviewer: jerskine
ms.openlocfilehash: 0aa13f2fced9122163d5278b5bb50cc1e11a0379
ms.sourcegitcommit: 9e179a577533ab3b2c0c7a4899ae13a7a0d5252b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/23/2018
ms.locfileid: "49946879"
---
# <a name="validate-adfs-integration-for-azure-stack"></a>Azure Stack の ADFS 統合を検証する

Azure Stack 適合性チェッカー ツール (AzsReadinessChecker) を使用して、対象の環境で Azure Stack と ADFS を統合する準備が整っていることを検証します。 データ センターの統合を開始したり、Azure Stack をデプロイしたりする前に、ADFS 統合を検証する必要があります。

適合性チェッカーは以下を検証します。

* "*フェデレーション メタデータ*" にフェデレーションに有効な XML 要素が含まれている。
* "*ADFS SSL 証明書*" を取得し、信頼チェーンを構築できる。 スタンプで、ADFS は SSL 証明書チェーンを信頼する必要があります。 証明書は、Azure Stack デプロイ証明書と同じ "*証明機関*" か、信頼されたルート証明機関パートナーによって署名されている必要があります。 信頼されたルート証明機関パートナーの完全な一覧については、 https://gallery.technet.microsoft.com/Trusted-Root-Certificate-123665ca を参照してください。
* "*ADFS 署名証明書*" が信頼され、有効期限が迫っていない。

Azure Stack とデータ センターの統合の詳細については、「[Azure Stack とデータセンターの統合 - ID](azure-stack-integrate-identity.md)」を参照してください。

## <a name="get-the-readiness-checker-tool"></a>適合性チェッカー ツールを取得する

最新バージョンの Azure Stack 適合性チェッカー ツール (AzsReadinessChecker) を [PSGallery](https://aka.ms/AzsReadinessChecker) からダウンロードします。  

## <a name="prerequisites"></a>前提条件

次の前提条件を満たす必要があります。

**ツールを実行するコンピューター:**

* ドメインに接続された Windows 10 または Windows Server 2016。
* PowerShell 5.1 以降。 お使いのバージョンを確認するには、次の PowerShell コマンドを実行し、"*メジャー*" バージョンと "*マイナー*" バージョンを確かめます。  
   > `$PSVersionTable.PSVersion`
* 最新バージョンの [Microsoft Azure Stack 適合性チェッカー](https://aka.ms/AzsReadinessChecker) ツール。

**Active Directory フェデレーション サービス (AD FS) 環境:**

少なくとも次のいずれかの形式のメタデータが必要です。

* ADFS フェデレーション メタデータの URL。 たとえば、`https://adfs.contoso.com/FederationMetadata/2007-06/FederationMetadata.xml` のように指定します。
* フェデレーション メタデータ XML ファイル。 FederationMetadata.xml など

## <a name="validate-adfs-integration"></a>ADFS 統合の検証

1. 前提条件を満たしているコンピューターで、管理 PowerShell プロンプトを開き、次のコマンドを実行して、AzsReadinessChecker をインストールします。

     `Install-Module Microsoft.AzureStack.ReadinessChecker -Force`

1. PowerShell プロンプトから次を実行して検証を開始します。 フェデレーション メタデータの URI として、**-CustomADFSFederationMetadataEndpointUri** の値を指定します。

     `Invoke-AzsADFSValidation -CustomADFSFederationMetadataEndpointUri https://adfs.contoso.com/FederationMetadata/2007-06/FederationMetadata.xml`

1. ツールの実行後、出力を確認します。 ADFS 統合の要件について、状態が OK であることを確認します。 検証が成功した場合は、次のように表示されます。

    ```
    Invoke-AzsADFSValidation v1.1809.1001.1 started.

    Testing ADFS Endpoint https://sts.contoso.com/FederationMetadata/2007-06/FederationMetadata.xml

            Read Metadata:                         OK
            Test Metadata Elements:                OK
            Test SSL ADFS Certificate:             OK
            Test Certificate Chain:                OK
            Test Certificate Expiry:               OK

    Details:
    [-] In standalone mode, some tests should not be considered fully indicative of connectivity or readiness the Azure Stack Stamp requires prior to Data Center Integration.
    Additional help URL: https://aka.ms/AzsADFSIntegration

    Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
    Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json

    Invoke-AzsADFSValidation Completed
    ```

運用環境では、オペレーターのワークステーションからの信頼の証明書チェーンをテストしても、Azure Stack インフラストラクチャにおける PKI の信頼できる状態を完全に示しているとは見なされません。 Azure Stack スタンプのパブリック VIP ネットワークでは、PKI インフラストラクチャ用の CRL への接続が必要です。

## <a name="report-and-log-file"></a>レポートとログ ファイル

検証を実行するたびに、結果のログが **AzsReadinessChecker.log** と **AzsReadinessCheckerReport.json** に出力されます。 これらのファイルの場所は、PowerShell に検証結果と共に表示されます。

これらの検証ファイルは、Azure Stack をデプロイする前、または検証に関する問題を調査する前に、状態を共有するときに役立ちます。 両方のファイルに、以降の各検証チェックの結果が保持されます。 デプロイ チームはこのレポートを使用して ID 構成を確認できます。 デプロイ チームやサポート チームは、検証の問題を調査する際に、このログ ファイルを役立たせることができます。

既定では、両方のファイルが `C:\Users\<username>\AppData\Local\Temp\AzsReadinessChecker\` に書き込まれます

次のコマンドを使用します。

* 別のレポートの場所を指定するには、実行コマンド ラインの末尾に **-OutputPath** "*パス*" パラメーターを使用します。
* 前のレポート情報から *AzsReadinessCheckerReport.json* をクリアするには、実行コマンドの末尾に **-CleanReport** パラメーターを使用します。 詳細については、「[Azure Stack 検証レポート](azure-stack-validation-report.md)」を参照してください。

## <a name="validation-failures"></a>検証エラー

検証チェックに失敗した場合は、エラーの詳細が PowerShell ウィンドウに表示されます。 また、ツールによって、*AzsReadinessChecker.log* にログ情報が記録されます。

次の例は、一般的な検証エラーに関するガイダンスです。

### <a name="command-not-found"></a>コマンドが見つからない

`Invoke-AzsADFSValidation : The term 'Invoke-AzsADFSValidation' is not recognized as the name of a cmdlet, function, script file, or operable program. Check the spelling of the name, or if a path was included, verify that the path is correct and try again.`

**原因** - PowerShell Autoload で、適合性チェッカー モジュールを正しく読み込めませんでした。

**解決方法** – 適合性チェッカー モジュールを明示的にインポートします。 下のコードをコピーして、PowerShell に貼り付けて、\<version\> を現在インストールされているバージョンのバージョン番号に更新します。

`Import-Module "c:\Program Files\WindowsPowerShell\Modules\Microsoft.AzureStack.ReadinessChecker\<version>\Microsoft.AzureStack.ReadinessChecker.psd1" -Force`

## <a name="next-steps"></a>次の手順

[対応状況レポートを表示する](azure-stack-validation-report.md)  
[Azure Stack の統合に関する一般的な考慮事項](azure-stack-datacenter-integration.md)  
