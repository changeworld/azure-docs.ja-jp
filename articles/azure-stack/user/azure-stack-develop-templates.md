---
title: "Azure Stack のテンプレートの開発 | Microsoft Docs"
description: "Azure Stack テンプレートのベスト プラクティスを説明します"
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.assetid: 8a5bc713-6f51-49c8-aeed-6ced0145e07b
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/20/2018
ms.author: brenduns
ms.reviewer: jeffgo
ms.openlocfilehash: bbd6cc68f1c16d48380cf498d6b089abe923e95a
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/22/2018
---
# <a name="azure-resource-manager-template-considerations"></a>Azure Resource Manager テンプレートに関する考慮事項

*適用先: Azure Stack 統合システムと Azure Stack Development Kit*

アプリケーションを開発するときは、Azure および Azure Stack 間のテンプレートの移植性を確保する必要があります。 この記事では、Azure Resource Manager [テンプレート](http://download.microsoft.com/download/E/A/4/EA4017B5-F2ED-449A-897E-BD92E42479CE/Getting_Started_With_Azure_Resource_Manager_white_paper_EN_US.pdf)を開発するための考慮事項を説明して、アプリケーションのプロトタイプ作成とデプロイのテストを、Azure Stack 環境にアクセスせずに Azure で実行できるようにします。

## <a name="resource-provider-availability"></a>リソース プロバイダーの可用性
デプロイする予定のテンプレートは、既に利用できるか、Azure Stack でプレビュー中の Microsoft Azure サービスのみを使用する必要があります。

## <a name="public-namespaces"></a>パブリック名前空間
Azure Stack はデータセンターでホストされるため、そのサービス エンドポイントの名前空間は、Azure パブリック クラウドとは異なります。 その結果、Azure Resource Manager テンプレートにハードコーディングされたパブリック エンドポイントでは、Azure Stack へのデプロイは失敗します。 代わりに、"*参照*" 関数と "*連結*" 関数を使用して、デプロイ時にリソース プロバイダーから取得した値に基づいて、サービス エンドポイントを動的に作成できます。 たとえば、テンプレートに *blob.core.windows.net*と指定する代わりに、[primaryEndpoints.blob](https://github.com/Azure/AzureStack-QuickStart-Templates/blob/master/101-simple-windows-vm/azuredeploy.json#L201) を取得して *osDisk.URI* エンドポイントを動的に設定します。

     "osDisk": {"name": "osdisk","vhd": {"uri": 
     "[concat(reference(concat('Microsoft.Storage/storageAccounts/', variables('storageAccountName')), '2015-06-15').primaryEndpoints.blob, variables('vmStorageAccountContainerName'),
      '/',variables('OSDiskName'),'.vhd')]"}}

## <a name="api-versioning"></a>API のバージョン管理
Azure のサービス バージョンが Azure と Azure Stack で異なる場合があります。 各リソースには、提供される機能を定義する apiVersion 属性が必要です。 Azure Stack での API のバージョンの互換性を確保するための各リソースプロバイダーで有効な API のバージョンは次のとおりです。

| リソース プロバイダー | apiVersion |
| --- | --- |
| コンピューティング |`'2015-06-15'` |
| ネットワーク |`'2015-06-15'`、`'2015-05-01-preview'` |
| Storage |`'2016-01-01'`、`'2015-06-15'`、`'2015-05-01-preview'` |
| KeyVault | `'2015-06-01'` |
| App Service |`'2015-08-01'` |

## <a name="template-functions"></a>テンプレート関数
Azure Resource Manager の[関数](../../azure-resource-manager/resource-group-template-functions.md)は、動的テンプレートを作成するために必要な機能を提供します。 たとえば、次のようなタスクのために関数を使用できます。

* 文字列の連結やトリミング 
* その他のリソースからの値の参照
* 複数のインスタンスをデプロイするためのリソースの反復処理 

次の関数は、Azure Stack では使用できません。

* Skip
* Take

## <a name="resource-location"></a>リソースの場所
Azure Resource Manager テンプレートは、デプロイ時にリソースを配置するのに location 属性を使用します。 Azure では、場所は、米国西部や南アメリカなどのリージョンを指します。 Azure Stack では、Azure Stack はユーザーのデータセンター内にあるため、場所の意味が異なります。 Azure と Azure Stack 間でテンプレートを確実に転送するには、個々のリソースをデプロイするときにリソース グループの場所を参照する必要があります。 そのためには、`[resourceGroup().Location]` を使用して、すべてのリソースがリソース グループの場所を継承するようにします。 次の抜粋は、ストレージ アカウントのデプロイ中にこの関数を使用する例を示しています。

    "resources": [
    {
      "name": "[variables('storageAccountName')]",
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "[variables('apiVersionStorage')]",
      "location": "[resourceGroup().location]",
      "comments": "This storage account is used to store the VM disks",
      "properties": {
      "accountType": "Standard_GRS"
      }
    }
    ]

## <a name="next-steps"></a>次の手順
* [PowerShell を使用したテンプレートのデプロイ](azure-stack-deploy-template-powershell.md)
* [Azure CLI を使用したテンプレートのデプロイ](azure-stack-deploy-template-command-line.md)
* [Visual Studio を使用したテンプレートのデプロイ](azure-stack-deploy-template-visual-studio.md)

