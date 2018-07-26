---
title: テンプレートを使用して Azure Firewall をデプロイする
description: テンプレートを使用して Azure Firewall をデプロイする
services: firewall
author: vhorne
manager: jpconnock
ms.service: firewall
ms.topic: article
ms.date: 7/11/2018
ms.author: victorh
ms.openlocfilehash: 1a732e22d72c36afe11030e42bae529baa35df1a
ms.sourcegitcommit: df50934d52b0b227d7d796e2522f1fd7c6393478
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/12/2018
ms.locfileid: "38992512"
---
# <a name="deploy-azure-firewall-using-a-template"></a>テンプレートを使用して Azure Firewall をデプロイする

[!INCLUDE [firewall-preview-notice](../../includes/firewall-preview-notice.md)]

Azure Firewall の記事の例では、Azure Firewall パブリック プレビューを既に有効にしていることを前提としています。 詳細については、「[Enable the Azure Firewall public preview](public-preview.md)」 (Azure Firewall パブリック プレビューを有効にする) を参照してください。

このテンプレートは、ファイアウォールとテスト ネットワーク環境を作成します。 ネットワークには、*AzureFirewallSubnet*、*ServersSubnet*、および *JumpboxSubnet* の 3 つのサブネットを含む 1 つの VNet があります。 ServersSubnet と JumpboxSubnet には、それぞれ 1 つの 2 コア Windows Server があります。

ファイアウォールは AzureFirewallSubnet にあり、www.microsoft.com へのアクセスを許可する単一のルールを含むアプリケーション ルール コレクションが構成されています。

ユーザー定義のルートが作成されます。このルートは、ファイアウォール規則が適用されるファイアウォールを経由する ServersSubnet からのネットワーク トラフィックを指します。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="template-location"></a>テンプレートの場所

テンプレートは、以下に配置されています。

[https://github.com/Azure/azure-quickstart-templates/tree/master/101-azurefirewall-sandbox](https://github.com/Azure/azure-quickstart-templates/tree/master/101-azurefirewall-sandbox)

概要を読み、デプロイする準備ができたら、**[Azure へのデプロイ]** をクリックします。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

まずファイアウォールによって作成されたリソースを調べ、必要がなくなったら、[Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) コマンドを使用して、リソース グループ、ファイアウォール、およびすべての関連リソースを削除できます。

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup
```
## <a name="next-steps"></a>次の手順

次に、Azure Firewall のログを監視することができます。

- [チュートリアル: Azure Firewall のログを監視する](./tutorial-diagnostics.md)

