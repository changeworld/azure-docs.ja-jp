---
title: PowerShell を使用して Direct ピアリングで Azure Peering Service を有効にする
titleSuffix: Azure
description: PowerShell を使用して Direct ピアリングで Azure Peering Service を有効にする
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: c029b822d00ce545e3623a6212421a55b2d1971f
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/21/2020
ms.locfileid: "81686996"
---
# <a name="enable-azure-peering-service-on-a-direct-peering-by-using-powershell"></a>PowerShell を使用して Direct ピアリングで Azure Peering Service を有効にする

この記事では、PowerShell コマンドレットと Azure Resource Manager デプロイモデルを使用して、Direct ピアリングで Azure [Peering Service](overview-peering-service.md) を有効にする方法について説明します。

必要に応じて、Azure [portal](howto-peering-service-portal.md) を使用してこのガイドを完了することもできます。

## <a name="before-you-begin"></a>開始する前に
* 構成を開始する前に、[前提条件](prerequisites.md)を確認します。
* Peering Service を有効にするサブスクリプションで、Direct ピアリングを選択します。 存在しない場合は、レガシの Direct ピアリングを変換するか、新しい Direct ピアリングを作成します。
    * レガシの Direct ピアリングを変換するには、「[PowerShell を使用して、レガシの Direct ピアリングを Azure リソースに変換する](howto-legacy-direct-powershell.md)」の手順に従います。
    * 新しい Direct ピアリングを作成するには、「[PowerShell を使用して Direct ピアリングを作成または変更する](howto-direct-powershell.md)」の手順に従います。

### <a name="work-with-azure-powershell"></a>Azure PowerShell を使用する
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="enable-peering-service-on-a-direct-peering"></a>Direct ピアリングで Peering Service を有効にする

### <a name="view-direct-peering"></a><a name= get></a>Direct ピアリングを表示する
[!INCLUDE [peering-direct-get](./includes/direct-powershell-get.md)]

### <a name="enable-the-direct-peering-for-peering-service"></a><a name= get></a>Peering Service 向けに Direct ピアリングを有効にする

前のステップで Direct ピアリングを確保した後、これを Peering Service 向けに有効にします。
[!INCLUDE [peering-direct-modify](./includes/peering-service-direct-powershell.md)]

## <a name="modify-a-direct-peering-connection"></a>Direct ピアリング接続を変更する

接続設定を変更する必要がある場合は、「[PowerShell を使用して Direct ピアリングを作成または変更する](howto-direct-powershell.md)」の中の「Direct ピアリングを変更する」のセクションを参照してください。

## <a name="next-steps"></a>次のステップ

* [PowerShell を使用して Exchange ピアリングを作成または変更する](howto-exchange-powershell.md)
* [PowerShell を使用してレガシの Exchange ピアリングを Azure リソースに変換する](howto-legacy-exchange-powershell.md)

## <a name="additional-resources"></a>その他のリソース
次のコマンドを実行することで、すべてのパラメーターの詳細な説明を取得できます。

```powershell
Get-Help Get-AzPeering -detailed
```

よく寄せられる質問については、「[Peering Service のよくあるご質問](service-faqs.md)」を参照してください。