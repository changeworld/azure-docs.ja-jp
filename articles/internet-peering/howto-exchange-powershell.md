---
title: PowerShell を使用して Exchange ピアリングを作成または変更する
titleSuffix: Azure
description: PowerShell を使用して Exchange ピアリングを作成または変更する
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 4fd7d345e5efbe6e4e86e5bb410e2df4dd917047
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2020
ms.locfileid: "75773698"
---
# <a name="create-or-modify-an-exchange-peering-using-powershell"></a>PowerShell を使用して Exchange ピアリングを作成または変更する

この記事では、PowerShell コマンドレットと Resource Manager デプロイ モデルを使用して、 Microsoft Exchange ピアリングを作成する方法について説明します。 この記事では、リソースの状態確認、更新、または削除およびプロビジョニング解除の方法も示します。

必要に応じて、[ポータル](howto-exchange-portal.md)を使用してこのガイドを完了することもできます。

## <a name="before-you-begin"></a>開始する前に
* 構成を開始する前に、[前提条件](prerequisites.md)に関するページと「[Exchange ピアリングのチュートリアル](walkthrough-exchange-all.md)」を確認してください。
* Microsoft との Exchange ピアリングが既にあり、Azure リソースに変換されていない場合は、「[ポータルを使用してレガシの Exchange ピアリングを Azure リソースに変換する](howto-legacy-exchange-powershell.md)」を参照してください。

### <a name="working-with-azure-powershell"></a>Azure PowerShell を使用する
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="create-and-provision-an-exchange-peering"></a>Exchange ピアリングを作成およびプロビジョニングする

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Azure アカウントにサインインしてサブスクリプションを選択する
[!INCLUDE [Account](./includes/account-powershell.md)]

### <a name="get-the-list-of-supported-peering-locations-for-exchange-peering"></a><a name=exchange-location></a>Exchange ピアリングでサポートされているピアリングの場所の一覧を取得する
[!INCLUDE [exchange-location](./includes/exchange-powershell-create-location.md)]

### <a name="create-an-exchange-peering"></a><a name=create></a>Exchange ピアリングを作成する
[!INCLUDE [exchange-peering](./includes/exchange-powershell-create-connection.md)]

### <a name="get-exchange-peering"></a><a name=get></a>Exchange ピアリングを取得する
[!INCLUDE [peering-exchange-get](./includes/exchange-powershell-get.md)]

## <a name="modify-an-exchange-peering"></a><a name="modify"></a>Exchange ピアリングを変更する
[!INCLUDE [peering-exchange-modify](./includes/exchange-powershell-modify.md)]

## <a name="deprovision-an-exchange-peering"></a><a name=delete></a>Exchange ピアリングをプロビジョニング解除する

[!INCLUDE [peering-exchange-delete](./includes/delete.md)]

## <a name="next-steps"></a>次のステップ

* [PowerShell を使用して、 Direct ピアリングを作成または変更する](howto-direct-powershell.md)
* [PowerShell を使用して、レガシの Direct ピアリングを Azure リソースに変換する](howto-legacy-direct-powershell.md)

## <a name="additional-resources"></a>その他のリソース
次のコマンドを実行することで、すべてのパラメーターの詳細な説明を取得できます。

```powershell
Get-Help Get-AzPeering -detailed
```

詳細については、「[インターネット ピアリングのよくあるご質問](faqs.md)」を参照してください。
