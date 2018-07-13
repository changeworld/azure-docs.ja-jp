---
title: クイック スタート - PowerShell を使用して Azure Analysis Services サーバーを作成する | Microsoft Docs
description: PowerShell を使って Azure Analysis Services サーバーを作成する方法について説明します。
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: quickstart
ms.date: 07/03/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 9149f15d0503b9a39ac67d9c3f6fc1ddde7e03bd
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/10/2018
ms.locfileid: "37952768"
---
# <a name="quickstart-create-a-server---powershell"></a>クイック スタート: サーバーを作成する - PowerShell

このクイック スタートでは、コマンド ラインから PowerShell を使って、Azure サブスクリプション内に Azure Analysis Services サーバーを作成する方法について説明します。

## <a name="prerequisites"></a>前提条件

- **Azure サブスクリプション**: [Azure 無料試用版](https://azure.microsoft.com/offers/ms-azr-0044p/)でアカウントを作成します。
- **Azure Active Directory**: サブスクリプションは、Azure Active Directory テナントに関連付けられている必要があります。また、そのディレクトリのアカウントが必要となります。 詳細については、「[認証とユーザーのアクセス許可](analysis-services-manage-users.md)」を参照してください。
- **Azure PowerShell モジュール バージョン 4.0 以降**. バージョンを確認するには、` Get-Module -ListAvailable AzureRM` を実行します。 インストールまたはアップグレードするには、[Azure PowerShell モジュールのインストール](/powershell/azure/install-azurerm-ps)に関するページを参照してください。

## <a name="import-azurermanalysisservices-module"></a>AzureRm.AnalysisServices モジュールのインポート

サブスクリプションにサーバーを作成するには、[AzureRM.AnalysisServices](https://www.powershellgallery.com/packages/AzureRM.AnalysisServices) コンポーネント モジュールを使います。 PowerShell セッションに AzureRm.AnalysisServices モジュールを読み込みます。

```powershell
Import-Module AzureRM.AnalysisServices
```

## <a name="log-in-to-azure"></a>Azure にログインする

[Connect-AzureRmAccount](/powershell/module/azurerm.profile/connect-azurermaccount) コマンドを使用して Azure サブスクリプションにログインします。 画面の指示に従います。

```powershell
Connect-AzureRmAccount
```

## <a name="create-a-resource-group"></a>リソース グループの作成

[Azure リソース グループ](../azure-resource-manager/resource-group-overview.md)とは、複数の Azure リソースをまとめてデプロイ、管理する際の論理コンテナーです。 サーバーを作成するときは、自分のサブスクリプション内のリソース グループを指定する必要があります。 リソース グループがまだない場合は、[New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) コマンドを使って新しく作成してください。 次の例は、米国西部リージョンに `myResourceGroup` という名前のリソース グループを作成します。

```powershell
New-AzureRmResourceGroup -Name "myResourceGroup" -Location "WestUS"
```

## <a name="create-a-server"></a>サーバーの作成

[New-AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/new-azurermanalysisservicesserver) コマンドを使って新しいサーバーを作成します。 次の例では、米国西部リージョンの myResourceGroup に myServer という D1 (無料) レベルのサーバーを作成し、philipc@adventureworks.com をサーバー管理者として指定します。

```powershell
New-AzureRmAnalysisServicesServer -ResourceGroupName "myResourceGroup" -Name "myserver" -Location WestUS -Sku D1 -Administrator "philipc@adventure-works.com"
```

## <a name="clean-up-resources"></a>リソースのクリーンアップ

サブスクリプションからサーバーを削除するには、[Remove-AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/new-azurermanalysisservicesserver) コマンドを使います。 続けてこのコレクション内の他のクイック スタートやチュートリアルに取り組む場合は、サーバーを削除しないでください。 次の例では、前の手順で作成したサーバーを削除します。


```powershell
Remove-AzureRmAnalysisServicesServer -Name "myserver" -ResourceGroupName "myResourceGroup"
```

## <a name="next-steps"></a>次の手順

このクイック スタートでは、PowerShell を使用して Azure サブスクリプションにサーバーを作成する方法について学習しました。 サーバーを作成したら、(必要に応じて) サーバー ファイアウォールを構成することによってサーバーをセキュリティで保護できます。 また、ポータルからサーバーにデータ モデルの基本的なサンプルを追加することもできます。 サンプル モデルがあると、モデル データベース ロールの構成とクライアント接続のテストについて理解するのに役立ちます。 詳しくは、サンプル モデルの追加のチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [クイック スタート: サーバーのファイアウォールの構成 - ポータル](analysis-services-qs-firewall.md)      
> [!div class="nextstepaction"]
> [チュートリアル: サーバーにサンプル モデルを追加する](analysis-services-create-sample-model.md)