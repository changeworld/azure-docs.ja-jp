---
title: PowerShell を使用して Azure Germany に接続する | Microsoft Docs
description: PowerShell を使用して Azure Germany でサブスクリプションを管理する方法についての情報
ms.topic: article
ms.date: 10/16/2020
author: gitralf
ms.author: ralfwi
ms.service: germany
ms.custom: bfdocs, devx-track-azurepowershell
ms.openlocfilehash: 3f5caa51b992d348fa077a0dbb96e210aa18f4de
ms.sourcegitcommit: 20acb9ad4700559ca0d98c7c622770a0499dd7ba
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/29/2021
ms.locfileid: "117029127"
---
# <a name="connect-to-azure-germany-by-using-powershell"></a>PowerShell を使用して Azure Germany に接続する

[!INCLUDE [closureinfo](../../includes/germany-closure-info.md)]

Azure Germany で Azure PowerShell を使用するには、グローバル Azure ではなく Azure Germany に接続する必要があります。 Azure PowerShell を使用すると、スクリプトで大規模なサブスクリプションを管理したり、現在 Azure portal にはない機能を利用したりすることができます。 グローバル Azure で PowerShell を使用したことがある場合は、操作の大部分は同じです。 Azure Germany で異なるのは次の点です。

* アカウントへの接続方法
* リージョン名

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

> [!NOTE]
> PowerShell を初めて使用する場合は、[Azure PowerShell の概要](/powershell/azure/)に関するページを確認してください。

PowerShell を開始する際、Azure Germany に接続するよう Azure PowerShell に指示するために、環境パラメーターを指定する必要があります。 パラメーターを指定することで、PowerShell は適切なエンドポイントに接続できます。 エンドポイントのコレクションはアカウントに接続する際に決定されます。 API ごとに必要な環境スイッチのバージョンが異なります。

| 接続の種類 | command |
| --- | --- |
| [Azure (クラシック デプロイ モデル)](/powershell/azure) のコマンド |`Add-AzureAccount -Environment AzureGermanCloud` |
| [Azure (Resource Manager デプロイ モデル)](/powershell/azure) のコマンド |`Connect-AzAccount -EnvironmentName AzureGermanCloud` |
| [Azure Active Directory (クラシック デプロイ モデル)](/previous-versions/azure/jj151815(v=azure.100)) のコマンド |`Connect-MsolService -AzureEnvironment AzureGermanyCloud` |
| [Azure Active Directory (Resource Manager デプロイ モデル)](../azure-resource-manager/management/deployment-models.md) のコマンド |`Connect-AzureAD -AzureEnvironmentName AzureGermanyCloud` |

`New-AzStorageContext` を使用してストレージ アカウントに接続するときに `Environment` スイッチを使用して、`AzureGermanCloud` を指定することもできます。

## <a name="determining-region"></a>リージョンの特定
接続した後に、相違点がもう 1 つあります。サービスの対象にする際に使用するリージョンです。 Azure クラウド サービスごとに異なるリージョンが用意されています。 リージョンの詳細は、利用可能なサービスに関するページで確認できます。 通常、コマンドの `Location` パラメーターには、このリージョンを使用します。


| 共通名 | 表示名 | 場所の名前 |
| --- | --- | --- |
| ドイツ中部 |`Germany Central` | `germanycentral` |
| ドイツ北東部 |`Germany Northeast` | `germanynortheast` |


> [!NOTE]
> グローバル Azure で PowerShell を使用する場合と同様に、`Location` パラメーターに表示名または場所名を使用できます。
>
>

Azure Germany で利用可能なリージョンを確認したい場合は、次のコマンドを実行し、最新のリストを印刷してください。 クラシック デプロイでは、最初のコマンドを使用します。 Resource Manager デプロイでは、2 つ目のコマンドを使用します。

```azurepowershell
Get-AzureLocation
Get-AzLocation
```

Azure で利用可能な環境を調べる場合は、次のコマンドを実行してください。

```azurepowershell
Get-AzureEnvironment
Get-AzEnvironment
```

## <a name="next-steps"></a>次のステップ
Azure Germany への接続の詳細については、以下のリソースを参照してください。

* [Azure CLI を使用して Azure Germany に接続する](./germany-get-started-connect-with-cli.md)
* [Visual Studio を使用して Azure Germany に接続する](./germany-get-started-connect-with-vs.md)
* [Azure portal を使用して Azure Germany に接続する](./germany-get-started-connect-with-portal.md)
