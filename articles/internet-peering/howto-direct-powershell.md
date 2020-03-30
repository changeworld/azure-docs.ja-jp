---
title: PowerShell を使用して Direct ピアリングを作成または変更する
titleSuffix: Azure
description: PowerShell を使用して Direct ピアリングを作成または変更する
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 69031550bdab1535213c78f81426fa76e8ea62ad
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2020
ms.locfileid: "75773646"
---
# <a name="create-or-modify-a-direct-peering-using-powershell"></a>PowerShell を使用して Direct ピアリングを作成または変更する

この記事では、PowerShell コマンドレットと Resource Manager デプロイ モデルを使用して、Microsoft Direct ピアリングを作成する方法について説明します。 この記事では、リソースの状態確認、更新、または削除およびプロビジョニング解除の方法も示します。

必要に応じて、[ポータル](howto-direct-portal.md)を使用してこのガイドを完了することもできます。

## <a name="before-you-begin"></a>開始する前に
* 構成を開始する前に、[前提条件](prerequisites.md)に関する記事と「[Direct ピアリングのチュートリアル](walkthrough-direct-all.md)」を確認してください。
* Microsoft との Direct ピアリングが既にあり、Azure リソースに変換されていない場合は、「[ポータルを使用してレガシの Direct ピアリングを Azure リソースに変換する](howto-legacy-direct-powershell.md)」を参照してください

### <a name="working-with-azure-powershell"></a>Azure PowerShell を使用する
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="create-and-provision-a-direct-peering"></a>Direct ピアリングを作成およびプロビジョニングする

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Azure アカウントにサインインしてサブスクリプションを選択する
[!INCLUDE [Account](./includes/account-powershell.md)]

### <a name="get-the-list-of-supported-peering-locations-for-direct-peering"></a><a name=direct-location></a>Direct ピアリングでサポートされているピアリングの場所の一覧を取得する
[!INCLUDE [direct-location](./includes/direct-powershell-create-location.md)]

### <a name="create-a-direct-peering"></a><a name=create></a>Direct ピアリングを作成する
[!INCLUDE [direct-peering](./includes/direct-powershell-create-connection.md)]

### <a name="verify-direct-peering"></a><a name=get></a>Direct ピアリングを確認する
[!INCLUDE [peering-direct-get](./includes/direct-powershell-get.md)]

## <a name="modify-a-direct-peering"></a><a name="modify"></a>Direct ピアリングを変更する
[!INCLUDE [peering-direct-modify](./includes/direct-powershell-modify.md)]

## <a name="deprovision-a-direct-peering"></a><a name="delete"></a>Direct ピアリングをプロビジョニング解除する
[!INCLUDE [peering-direct-delete](./includes/delete.md)]

## <a name="next-steps"></a>次のステップ

* [PowerShell を使用して Exchange ピアリングを作成または変更します](howto-exchange-powershell.md)。
* [PowerShell を使用してレガシの Exchange ピアリングを Azure リソースに変換します](howto-legacy-exchange-powershell.md)。

## <a name="additional-resources"></a>その他のリソース
次のコマンドを実行することで、すべてのパラメーターの詳細な説明を取得できます。

```powershell
Get-Help Get-AzPeering -detailed
```

詳細については、「[インターネット ピアリングのよくあるご質問](faqs.md)」を参照してください。
