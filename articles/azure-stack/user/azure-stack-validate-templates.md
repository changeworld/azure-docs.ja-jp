---
title: "テンプレート検証ツールを使用して Azure Stack のテンプレートをチェックする | Microsoft Docs"
description: "Azure Stack にデプロイするためのテンプレートをチェックする"
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.assetid: d9e6aee1-4cba-4df5-b5a3-6f38da9627a3
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/20/2018
ms.author: brenduns
ms.reviewer: jeffgo
ms.openlocfilehash: 6a77efb3ef4236048ff08b14346175b592493982
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/21/2018
---
# <a name="check-your-templates-for-azure-stack-with-template-validator"></a>テンプレート検証ツールで Azure Stack のテンプレートをチェックする

*適用先: Azure Stack 統合システムと Azure Stack Development Kit*

テンプレート検証ツールを使用して、Azure Stack 用に Azure Resource Manager [テンプレート](azure-stack-arm-templates.md) の準備ができているかどうかをチェックできます。 テンプレート検証ツールは Azure Stack ツールの一部として使用できます。 Azure Stack ツールをダウンロードするには、[GitHub からのツールのダウンロード](azure-stack-powershell-download.md)に関する記事に記載されている手順を使用します。 

テンプレートを検証するには、**TemplateValidator** フォルダーと **CloudCapabilities** フォルダーにある次の PowerShell モジュールを使用します。 

 - AzureRM.CloudCapabilities.psm1 は、Azure Stack のようなクラウド内のサービスとバージョンを表すクラウド機能 JSON ファイルを作成します。
 - AzureRM.TemplateValidator.psm1 は、クラウド機能 JSON ファイルを使用して、Azure Stack へのデプロイ用テンプレートをテストします。
 
この記事では、クラウド機能ファイルを構築し、検証ツールを実行します。

## <a name="build-cloud-capabilities-file"></a>クラウド機能ファイルを構築する
テンプレート検証ツールを使用する前に、AzureRM.CloudCapabilities PowerShell モジュールを実行して JSON ファイルを構築します。 統合システムを更新する場合や、新しいサービスまたは VM 拡張機能を追加する場合は、このモジュールの再実行も必要になります。

1.  Azure Stack への接続があることを確認してください。 これらの手順は Azure Stack Development Kit ホストから実行するか、または [VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn) を使用して、ワークステーションから接続できます。 
2.  AzureRM.CloudCapabilities PowerShell モジュールをインポートします。

    ```PowerShell
    Import-Module .\CloudCapabilities\AzureRM.CloudCapabilities.psm1
    ``` 

3.  Get-CloudCapabilities コマンドレットを使用して、サービスのバージョンを取得し、クラウド機能 JSON ファイルを作成します。 -OutputPath を指定しない場合は、ファイル AzureCloudCapabilities.Json は現在のディレクトリに作成されます。 実際の場所を使用してください。

    ```PowerShell
    Get-AzureRMCloudCapability -Location <your location> -Verbose
    ```             

## <a name="validate-templates"></a>テンプレートの検証
次の手順で、AzureRM.TemplateValidator PowerShell モジュールを使用してテンプレートを検証します。 独自のテンプレートを使用するか、または [Azure Stack クイックスタート テンプレート](https://github.com/Azure/AzureStack-QuickStart-Templates)を検証することができます。

1.  AzureRM.TemplateValidator.psm1 PowerShell モジュールをインポートします。
    
    ```PowerShell
    cd "c:\AzureStack-Tools-master\TemplateValidator"
    Import-Module .\AzureRM.TemplateValidator.psm1
    ```

2.  テンプレート検証ツールを実行します。

    ```PowerShell
    Test-AzureRMTemplate -TemplatePath <path to template.json or template folder> `
    -CapabilitiesPath <path to cloudcapabilities.json> `
    -Verbose
    ```

テンプレート検証の警告やエラーが、PowerShell コンソールとソース ディレクトリ内の HTML ファイルに記録されます。 検証レポートの例を次に示します。

![サンプル検証レポート](./media/azure-stack-validate-templates/image1.png)

### <a name="parameters"></a>parameters

| パラメーター | [説明] | 必須 |
| ----- | -----| ----- |
| TemplatePath | Azure Resource Manager テンプレートを再帰的に検索するパスを指定します | [はい] | 
| TemplatePattern | 照合するテンプレート ファイルの名前を指定します。 | いいえ  |
| CapabilitiesPath | クラウド機能 JSON ファイルのパスを指定します | [はい] | 
| IncludeComputeCapabilities | VM サイズや VM 拡張機能などの IaaS リソースの評価が含まれます | いいえ  |
| IncludeStorageCapabilities | SKU の種類などのストレージ リソースの評価が含まれます | いいえ  |
| レポート | 生成される HTML レポートの名前を指定します | いいえ  |
| 詳細 | エラーと警告をコンソールに記録します | いいえ |


### <a name="examples"></a>例
この例では、ローカルにダウンロードされたすべての [Azure Stack クイックスタート テンプレート](https://github.com/Azure/AzureStack-QuickStart-Templates)を検証し、Azure Stack Development Kit の機能に対して、VM のサイズと拡張機能も検証します。

```PowerShell
test-AzureRMTemplate -TemplatePath C:\AzureStack-Quickstart-Templates `
-CapabilitiesPath .\TemplateValidator\AzureStackCloudCapabilities_with_AddOns_20170627.json.json `
-TemplatePattern MyStandardTemplateName.json`
-IncludeComputeCapabilities`
-Report TemplateReport.html
```


## <a name="next-steps"></a>次の手順
 - [テンプレートを Azure Stack にデプロイする](azure-stack-arm-templates.md)
 - [Azure Stack のテンプレートの開発](azure-stack-develop-templates.md)

