---
title: Azure Stack の Azure Graph 統合を検証する
description: Azure Stack 適合性チェッカーを使用して、Azure Stack の Graph 統合を検証します。
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
ms.openlocfilehash: e1c1ba0a065a20874bf51d7464cbcfdfa13a571d
ms.sourcegitcommit: 9e179a577533ab3b2c0c7a4899ae13a7a0d5252b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/23/2018
ms.locfileid: "49946878"
---
# <a name="validate-graph-integration-for-azure-stack"></a>Azure Stack の Graph 統合を検証する

Azure Stack 適合性チェッカー ツール (AzsReadinessChecker) を使用して、対象の環境で Azure Stack と Graph を統合する準備が整っていることを検証します。 データ センターの統合を開始したり、Azure Stack をデプロイしたりする前に、Graph 統合を検証する必要があります。

適合性チェッカーは以下を検証します。

* Graph 統合用に作成したサービス アカウントの資格情報に、Active Directory に対してクエリを実行するための適切な権限が含まれていること。
* "*グローバル カタログ*" を解決できること。また、グローバル カタログに接続できること。
* KDC を解決できること。また、KDC に接続できること。
* 必要なネットワーク接続が存在すること。

Azure Stack とデータ センターの統合の詳細については、「[Azure Stack とデータセンターの統合 - ID](azure-stack-integrate-identity.md)」を参照してください

## <a name="get-the-readiness-checker-tool"></a>適合性チェッカー ツールを取得する

最新バージョンの Azure Stack 適合性チェッカー ツール (AzsReadinessChecker) を [PSGallery](https://aka.ms/AzsReadinessChecker) からダウンロードします。

## <a name="prerequisites"></a>前提条件

次の前提条件を満たす必要があります。

**ツールを実行するコンピューター:**

* ドメインに接続された Windows 10 または Windows Server 2016。
* PowerShell 5.1 以降。 お使いのバージョンを確認するには、次の PowerShell コマンドを実行し、"*メジャー*" バージョンと "*マイナー*" バージョンを確かめます。  
   > `$PSVersionTable.PSVersion`
* Active Directory PowerShell モジュール
* 最新バージョンの [Microsoft Azure Stack 適合性チェッカー](https://aka.ms/AzsReadinessChecker) ツール

**Active Directory の環境:**

* 既存の Active Directory で Graph サービスのアカウントのユーザー名とパスワードを特定できる
* Active Directory フォレストの ルート FQDN を特定できる

## <a name="validate-graph"></a>Graph を検証する

1. 前提条件を満たしているコンピューターで、管理 PowerShell プロンプトを開き、次のコマンドを実行して、AzsReadinessChecker をインストールします。

     `Install-Module Microsoft.AzureStack.ReadinessChecker -Force`

1. PowerShell プロンプトから次を実行して、*$graphCredential* 変数を Graph アカウントに設定します。 `domain\username` の形式を使用して `contoso\graphservice` をご自身のアカウントで置き換えます。

    `$graphCredential = Get-Credential contoso\graphservice -Message "Enter Credentials for the Graph Service Account"`

1. PowerShell プロンプトから次を実行して、Graph の検証を開始します。 次のように、**-ForestFQDN** の値をフォレスト ルートの FQDN として指定します。

     `Invoke-AzsGraphValidation -ForestFQDN contoso.com -Credential $graphCredential`

1. ツールの実行後、出力を確認します。 Graph 統合の要件について、状態が OK であることを確認します。 検証が成功した場合は、次のように表示されます。

    ```
    Testing Graph Integration (v1.0)
            Test Forest Root:            OK
            Test Graph Credential:       OK
            Test Global Catalog:         OK
            Test KDC:                    OK
            Test LDAP Search:            OK
            Test Network Connectivity:   OK

    Details:

    [-] In standalone mode, some tests should not be considered fully indicative of connectivity or readiness the Azure Stack Stamp requires prior to Data Center Integration.

    Additional help URL: https://aka.ms/AzsGraphIntegration

    AzsReadinessChecker Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log

    AzsReadinessChecker Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json

    Invoke-AzsGraphValidation Completed
    ```

運用環境では、オペレーターのワークステーションからのネットワーク接続をテストしても、その接続を Azure Stack で使用できることを完全に示しているとは見なされません。 Azure Stack スタンプのパブリック VIP ネットワークでは、ID の統合を実行する LDAP トラフィック用の接続が必要です。

## <a name="report-and-log-file"></a>レポートとログ ファイル

検証を実行するたびに、結果のログが **AzsReadinessChecker.log** と **AzsReadinessCheckerReport.json** に出力されます。 これらのファイルの場所は、PowerShell に検証結果と共に表示されます。

これらの検証ファイルは、Azure Stack をデプロイする前、または検証に関する問題を調査する前に、状態を共有するときに役立ちます。 両方のファイルに、以降の各検証チェックの結果が保持されます。 デプロイ チームはこのレポートを使用して ID 構成を確認できます。 デプロイ チームやサポート チームは、検証の問題を調査する際に、このログ ファイルを役立たせることができます。

既定では、両方のファイルが `C:\Users\<username>\AppData\Local\Temp\AzsReadinessChecker\` に書き込まれます

次のコマンドを使用します。

* 別のレポートの場所を指定するには、実行コマンド ラインの末尾に **-OutputPath** "*パス*" パラメーターを使用します。
* 前のレポート情報から *AzsReadinessCheckerReport.json* をクリアするには、実行コマンドの末尾に **-CleanReport** を使用します。 詳細については、「[Azure Stack 検証レポート](azure-stack-validation-report.md)」を参照してください。

## <a name="validation-failures"></a>検証エラー

検証チェックに失敗した場合は、エラーの詳細が PowerShell ウィンドウに表示されます。 また、ツールによって、*AzsGraphIntegration.log* にログ情報が記録されます。

## <a name="next-steps"></a>次の手順

[対応状況レポートを表示する](azure-stack-validation-report.md)  
[Azure Stack の統合に関する一般的な考慮事項](azure-stack-datacenter-integration.md)  
