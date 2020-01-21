---
title: PowerShell を使用し、Direct ピアリングで Peering Service を有効にする
titleSuffix: Azure
description: PowerShell を使用し、Direct ピアリングで Peering Service を有効にする
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: ac843fa5440f7ba085d33cd897bcd4a1722f77ac
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/09/2020
ms.locfileid: "75773630"
---
# <a name="enable-peering-service-on-a-direct-peering-using-powershell"></a>PowerShell を使用し、Direct ピアリングで Peering Service を有効にする

この記事では、PowerShell コマンドレットと Resource Manager デプロイモデルを使用して、 Direct ピアリングで [Peering Service](overview-peering-service.md) を有効にする方法について説明します。

必要に応じて、[ポータル](howto-peering-service-portal.md)を使用してこのガイドを完了することもできます。

## <a name="before-you-begin"></a>開始する前に
* 構成を開始する前に、[前提条件](prerequisites.md)を確認してください。
* Peering Service を有効にするサブスクリプションで、Direct ピアリングを選択します。 その時点でピアリングがない場合には、レガシの Direct ピアリングを変換するか、または、新しい Direct ピアリングを作成します。
    * レガシの Direct ピアリングを変換するには、「[PowerShell を使用してレガシの Direct ピアリングを Azure リソースに変換する](howto-legacy-direct-powershell.md)」の手順に従います。
    * 新しい Direct ピアリングを作成するには、「[PowerShell を使用して Direct ピアリングを作成または変更する](howto-direct-powershell.md)」の手順に従います。

### <a name="working-with-azure-powershell"></a>Azure PowerShell を使用する
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="enable-peering-service-on-a-direct-peering"></a>Direct ピアリングで Peering Service を有効にする

### <a name= get></a>Direct ピアリングを表示する
[!INCLUDE [peering-direct-get](./includes/direct-powershell-get.md)]

### <a name= get></a>Peering Service 向けに Direct ピアリングを有効にする

前のステップで Direct ピアリングを取得した後、これを Peering Service 向けに有効にします。
[!INCLUDE [peering-direct-modify](./includes/peering-service-direct-powershell.md)]

## <a name="modify-a-direct-peering-connection"></a>Direct ピアリング接続を変更する

接続設定を変更する必要がある場合は、[「PowerShel を使用してDirect ピアリングを作成または変更する」](howto-direct-powershell.md)の中の **「Direct ピアリングを変更する」** のセクションを参照してください。

## <a name="next-steps"></a>次のステップ

* [PowerShell を使用して Exchange ピアリングを作成または変更する](howto-exchange-powershell.md)
* [PowerShell を使用してレガシの Exchange ピアリングを Azure リソースに変換する](howto-legacy-exchange-powershell.md)

## <a name="additional-resources"></a>その他のリソース
次のコマンドを実行することで、すべてのパラメーターの詳細な説明を取得できます。

```powershell
Get-Help Get-AzPeering -detailed
```

よく寄せられる質問については、「[Peering Service に関する FAQ](service-faqs.md)」を参照してください。