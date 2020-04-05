---
title: ポータルを使用して Direct ピアリングで Peering Service を有効にする
titleSuffix: Azure
description: ポータルを使用して Direct ピアリングで Peering Service を有効にする
services: internet-peering
author: derekolo
ms.service: internet-peering
ms.topic: article
ms.date: 3/18/2020
ms.author: derekol
ms.openlocfilehash: 87a65826a338f4098ae24c33939ea7f9d4475e36
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "80129933"
---
# <a name="enable-peering-service-on-a-direct-peering-using-the-portal"></a>ポータルを使用して Direct ピアリングで Peering Service を有効にする

この記事では、ポータルを使用して Direct ピアリングで [Peering Service](overview-peering-service.md) を有効にする方法について説明します。

必要に応じて、[PowerShell](howto-peering-service-powershell.md) を使用してこのガイドを完了することもできます。

## <a name="before-you-begin"></a>開始する前に
* 構成を開始する前に、[前提条件](prerequisites.md)を確認してください。
* Peering Service を有効にするサブスクリプションで、Direct ピアリングを選択します。 その時点でピアリングがない場合には、レガシの Direct ピアリングを変換するか、または、新しい Direct ピアリングを作成します。
    * レガシの Direct ピアリングを変換するには、「[ポータルを使用してレガシの Direct ピアリングを Azure リソースに変換する](howto-legacy-direct-portal.md)」の手順に従います。
    * 新しい Direct ピアリングを作成するには、「[ポータルを使用して Direct ピアリングを作成または変更する](howto-direct-portal.md)」の手順に従います。

## <a name="enable-peering-service-on-a-direct-peering"></a>Direct ピアリングで Peering Service を有効にする

### <a name="view-direct-peering"></a><a name= get></a>Direct ピアリングを表示する
[!INCLUDE [peering-direct-get-portal](./includes/direct-portal-get.md)]

### <a name="enable-the-direct-peering-for-peering-service"></a><a name= get></a>Peering Service 向けに Direct ピアリングを有効にする

前のステップで Direct ピアリングをオープンした後、これを Peering Service 向けに有効にします。
[!INCLUDE [peering-direct-modify](./includes/peering-service-direct-portal.md)]

## <a name="modify-a-direct-peering-connection"></a>Direct ピアリング接続を変更する

接続設定を変更する必要がある場合は、 **「ポータルを使用してDirect ピアリングを作成または変更する」** の中の[「Direct ピアリングを変更する」](howto-direct-portal.md)のセクションを参照してください。

## <a name="next-steps"></a>次のステップ

* [ポータルを使用して Exchange ピアリングを作成または変更する](howto-exchange-portal.md)
* [ポータルを使用して従来の Exchange ピアリングを Azure リソースに変換する](howto-legacy-exchange-portal.md)

## <a name="additional-resources"></a>その他のリソース

よく寄せられる質問については、「[Peering Service に関する FAQ](service-faqs.md)」を参照してください。