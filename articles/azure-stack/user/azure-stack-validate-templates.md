---
title: テンプレート検証ツールを使用して Azure Stack のテンプレートをチェックする | Microsoft Docs
description: Azure Stack にデプロイするためのテンプレートをチェックする
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: d9e6aee1-4cba-4df5-b5a3-6f38da9627a3
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2018
ms.author: sethm
ms.reviewer: jeffgo
ms.openlocfilehash: 73a0766baee8da782f0192fbc17fb2898a8360ac
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/18/2018
ms.locfileid: "41954859"
---
# <a name="check-your-templates-for-azure-stack-with-the-template-validation-tool"></a>テンプレート検証ツールを使用して Azure Stack のテンプレートをチェックする

*適用先: Azure Stack 統合システムと Azure Stack 開発キット*

テンプレート検証ツールを使用して、Azure Stack に Azure Resource Manager [テンプレート](azure-stack-arm-templates.md) をデプロイする準備ができているかどうかをチェックできます。 テンプレート検証ツールは Azure Stack ツールの一部として使用できます。 Azure Stack ツールをダウンロードするには、[GitHub からのツールのダウンロード](azure-stack-powershell-download.md)に関する記事に記載されている手順を使用します。

## <a name="overview"></a>概要

テンプレートを検証するには、最初にクラウド機能ファイルをビルドしてから検証ツールを実行します。 Azure Stack ツールから、次の PowerShell モジュールを使用します。

- **CloudCapabilities** フォルダー内:<br>         AzureRM.CloudCapabilities.psm1 は、Azure Stack クラウド内のサービスとバージョンを表すクラウド機能 JSON ファイルを作成します。
- **TemplateValidator** フォルダー内:<br>
AzureRM.TemplateValidator.psm1 は、クラウド機能 JSON ファイルを使用して、Azure Stack へのデプロイ用テンプレートをテストします。

## <a name="build-the-cloud-capabilities-file"></a>クラウド機能ファイルをビルドする

テンプレート検証ツールを使用する前に、AzureRM.CloudCapabilities PowerShell モジュールを実行して JSON ファイルを構築します。

>[!NOTE]
>統合システムを更新する場合や、新しいサービスまたは仮想拡張機能を追加する場合は、このモジュールの再実行が必要になります。

1. Azure Stack への接続があることを確認してください。 これらの手順は Azure Stack Development Kit ホストから実行するか、または [VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn) を使用して、ワークステーションから接続できます。
2. AzureRM.CloudCapabilities PowerShell モジュールをインポートします。

    ```PowerShell
    Import-Module .\CloudCapabilities\AzureRM.CloudCapabilities.psm1
    ```

3. Get-CloudCapabilities コマンドレットを使用して、サービスのバージョンを取得し、クラウド機能 JSON ファイルを作成します。 **-OutputPath** を指定しない場合は、ファイル AzureCloudCapabilities.Json は現在のディレクトリに作成されます。 実際の場所を使用してください。

    ```PowerShell
    Get-AzureRMCloudCapability -Location <your location> -Verbose
    ```

## <a name="validate-templates"></a>テンプレートの検証

次の手順を使用して、AzureRM.TemplateValidator PowerShell モジュールを使用してテンプレートを検証します。 独自のテンプレートを使用するか、または [Azure Stack クイックスタート テンプレート](https://github.com/Azure/AzureStack-QuickStart-Templates)を検証することができます。

1. AzureRM.TemplateValidator.psm1 PowerShell モジュールをインポートします。

    ```PowerShell
    cd "c:\AzureStack-Tools-master\TemplateValidator"
    Import-Module .\AzureRM.TemplateValidator.psm1
    ```

2. テンプレート検証ツールを実行します。

    ```PowerShell
    Test-AzureRMTemplate -TemplatePath <path to template.json or template folder> `
    -CapabilitiesPath <path to cloudcapabilities.json> `
    -Verbose
    ```

テンプレート検証の警告やエラーが、PowerShell コンソールとソース ディレクトリ内の HTML ファイルに記録されます。 次の画面キャプチャは、検証レポートの例を示しています。

![テンプレートの検証レポート](./media/azure-stack-validate-templates/image1.png)

### <a name="parameters"></a>parameters

テンプレートの検証では、次のパラメーターがサポートされています。

| パラメーター | 説明 | 必須 |
| ----- | -----| ----- |
| TemplatePath | Azure Resource Manager テンプレートを再帰的に検索するパスを指定します | [はい] | 
| TemplatePattern | 照合するテンプレート ファイルの名前を指定します。 | いいえ  |
| CapabilitiesPath | クラウド機能 JSON ファイルのパスを指定します | [はい] | 
| IncludeComputeCapabilities | VM サイズや VM 拡張機能などの IaaS リソースの評価が含まれます | いいえ  |
| IncludeStorageCapabilities | SKU の種類などのストレージ リソースの評価が含まれます | いいえ  |
| レポート | 生成される HTML レポートの名前を指定します | いいえ  |
| 詳細 | エラーと警告をコンソールに記録します | いいえ |

### <a name="examples"></a>例

この例では、ローカル ストレージにダウンロードした [Azure Stack クイック スタート テンプレート](https://github.com/Azure/AzureStack-QuickStart-Templates)をすべて検証します。 例では、仮想マシンのサイズと Azure Stack Development Kit 機能に対する拡張機能も検証します。

```PowerShell
test-AzureRMTemplate -TemplatePath C:\AzureStack-Quickstart-Templates `
-CapabilitiesPath .\TemplateValidator\AzureStackCloudCapabilities_with_AddOns_20170627.json `
-TemplatePattern MyStandardTemplateName.json`
-IncludeComputeCapabilities`
-Report TemplateReport.html
```

## <a name="next-steps"></a>次の手順

- [テンプレートを Azure Stack にデプロイする](azure-stack-arm-templates.md)
- [Azure Stack のテンプレートの開発](azure-stack-develop-templates.md)
