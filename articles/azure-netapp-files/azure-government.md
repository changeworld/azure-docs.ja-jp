---
title: Azure Government 向け Azure NetApp Files | Microsoft Docs
description: Azure Government に接続して Azure NetApp Files を使用する方法と、Azure Government における Azure NetApp Files 機能の可用性について説明します。
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/14/2021
ms.author: b-juche
ms.openlocfilehash: 9e7986a0e040d0d8241fd33a2990d8bd82e51a4e
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130230237"
---
# <a name="azure-netapp-files-for-azure-government"></a>Azure Government 向け Azure NetApp Files 

[Microsoft Azure Government](../azure-government/documentation-government-welcome.md) は、政府機関とそのパートナーがミッションクリティカルなワークロードをクラウドに変換できる専用のクラウドを提供します。  

この記事では、Azure Government における Azure NetApp Files 機能の可用性について説明します。 また、Azure NetApp Files サービスに Azure Government 内でアクセスする方法も示します。

## <a name="feature-availability"></a>使用可能な機能

Azure NetApp Files でサポートされている Azure Government のリージョンについては、「 *[リージョン別の利用可能な製品](https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=usgov-non-regional,us-dod-central,us-dod-east,usgov-arizona,usgov-texas,usgov-virginia)* 」のページを参照してください。  

Azure パブリック クラウドで利用できるすべての [Azure NetApp Files](whats-new.md) の機能は、"***次の表に記載されている機能を除き***"、サポートされている Azure Government リージョンでも利用できます。 

| Azure NetApp Files の機能 | Azure パブリック クラウドの可用性 |  Azure Government の可用性 |
|:--- |:--- |:--- |
| Azure NetApp Files のリージョン間レプリケーション | 一般提供 (GA) | [制限がある](cross-region-replication-introduction.md#supported-region-pairs) |
| Azure NetApp Files バックアップ | パブリック プレビュー | いいえ |

## <a name="portal-access"></a>ポータル アクセス

Azure Government ユーザーは、ブラウザーを **portal.azure.us** にポイントすることで、Azure NetApp Files にアクセスできます。ポータル サイト名は **Microsoft Azure Government** です。詳細については、[ポータルを使用した Azure Government への接続](../azure-government/documentation-government-get-started-connect-with-portal.md)に関する記事をご確認ください。   

![URL として portal.azure.us が強調表示された Azure Government ポータルのスクリーンショット](../media/azure-netapp-files/azure-government.jpg)

Microsoft Azure Government ポータルから、Azure portal でアクセスするのと同じ方法で、Azure NetApp Files にアクセスできます。たとえば、Azure portal の [リソースを検索] ボックスに「**Azure NetApp Files**」と入力し、表示された一覧から **[Azure NetApp Files]** を選択します。  

サービスの使用の詳細については、[Azure NetApp Files](./index.yml) のドキュメントを参照してください。

## <a name="azure-cli-access"></a>Azure CLI アクセス

クラウド名を `AzureUSGovernment` に設定し、`az login` コマンドを使用して通常と同じ方法でログインし、Azure Government に接続できます。 ログイン コマンドを実行すると、ブラウザーが起動します。ここで、適切な Azure Government 資格情報を入力します。  

```azurecli 

az cloud set --name AzureUSGovernment 

``` 

クラウドが `AzureUSGovernment` に設定されていることを確認するには、次を実行します。 

```azurecli 

az cloud list --output table 

``` 

このコマンドを実行すると、Azure クラウドの場所を含むテーブルが生成されます。 `AzureUSGovernment` の `isActive` 列エントリは、`true` であるはずです。  

詳細は、「[Azure CLI を使用した Azure Government への接続](../azure-government/documentation-government-get-started-connect-with-cli.md)」を参照してください。

## <a name="rest-api-access"></a>REST API アクセス

Azure Government のエンドポイントは、商用 Azure エンドポイントとは異なります。 さまざまなエンドポイントの一覧については、Azure Government の「[開発者向ガイダンス](../azure-government/compare-azure-government-global-azure.md#guidance-for-developers)」を参照してください。

## <a name="powershell-access"></a>PowerShell アクセス

PowerShell を使用して Azure Government に接続する場合は、環境パラメーターを指定して、正しいエンドポイントに確実に接続する必要があります。 そこから、通常 PowerShell で行うのと同様に、Azure NetApp Files の使用に進めます。 

| 接続の種類 | コマンド | 
| --- | --- | 
| [Azure](/powershell/module/az.accounts/Connect-AzAccount) のコマンド |`Connect-AzAccount -EnvironmentName AzureUSGovernment` | 
| [Azure Active Directory](/powershell/module/azuread/connect-azuread) のコマンド |`Connect-AzureAD -AzureEnvironmentName AzureUSGovernment` | 
| [Azure (クラシック デプロイ モデル)](/powershell/module/servicemanagement/azure.service/add-azureaccount) のコマンド |`Add-AzureAccount -Environment AzureUSGovernment` | 
| [Azure Active Directory (クラシック デプロイ モデル)](/previous-versions/azure/jj151815(v=azure.100)) のコマンド |`Connect-MsolService -AzureEnvironment UsGovernment` | 

詳しくは、「[PowerShell を使用した Azure Government への接続](../azure-government/documentation-government-get-started-connect-with-ps.md)」を参照してください。

## <a name="next-steps"></a>次のステップ
* [Azure Government とは?](../azure-government/documentation-government-welcome.md)
* [Azure NetApp Files の新機能](whats-new.md)
* [商用 Azure Government とグローバル Azure の比較](../azure-government/compare-azure-government-global-azure.md)
* [Azure NetApp Files REST API](azure-netapp-files-develop-with-rest-api.md)
* [PowerShell を使用する Azure NetApp Files REST API](develop-rest-api-powershell.md)