---
title: インクルード ファイル
description: インクルード ファイル
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: include
ms.date: 10/15/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 4f49220da5d996615c9f8ef7cad2b6c6793866b7
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2019
ms.locfileid: "73466253"
---
### <a name="regions"></a>利用可能なリージョン

[!INCLUDE [region](bastion-regions-include.md)]

### <a name="publicip"></a>自分の仮想マシンにはパブリック IP が必要ですか?

Azure Bastion サービスを使用して接続する Azure 仮想マシンにはパブリック IP は必要ありません。 Bastion サービスは、ご使用の仮想ネットワーク内で、お客様の仮想マシンのプライベート IP 経由でお客様の仮想マシンへの RDP または SSH セッションや接続を開きます。

### <a name="rdpssh"></a>RDP または SSH クライアントは必要ですか?

ご使用の Azure portal 内でお客様の Azure 仮想マシンに RDP または SSH アクセスするために RDP または SSH クライアントは必要ありません。 [Azure portal](https://portal.azure.com) を使用して、ブラウザー内で仮想マシンに直接 RDP または SSH アクセスすることができます。

### <a name="agent"></a>Azure 仮想マシン内で実行するエージェントは必要ですか?

ご使用のブラウザーやお客様の Azure 仮想マシンにエージェントやソフトウェアをインストールする必要はありません。 Bastion サービスはエージェントレスのため、RDP や SSH 用の追加のソフトウェアを必要としません。

### <a name="browsers"></a>どのブラウザーがサポートされていますか?

Windows では Microsoft Edge ブラウザーまたは Google Chrome をご使用ください。 Apple Mac では、Google Chrome ブラウザーをご使用ください。 Microsoft Edge Chromium も Windows と Mac の両方でサポートされます。

### <a name="roles"></a>仮想マシンにアクセスするには、なんらかのロールが必要ですか?

接続を作成するには、次のロールが必要です。

* 仮想マシンに対する閲覧者ロール
* 仮想マシンのプライベート IP を使用する NIC に対する閲覧者ロール
* Azure Bastion リソースに対する閲覧者ロール

### <a name="pricingpage"></a>料金はどのようになっていますか?

詳細については、 [価格に関するページ](https://aka.ms/BastionHostPricing)を参照してください。

### <a name="session"></a>Bastion セッションの開始前に "セッションの有効期限が切れています" というエラー メッセージを受け取るのはなぜですか?

セッションは Azure portal からのみ開始される必要があります。 もう一度 Azure portal にサインインして、自分のセッションを開始してください。 別のブラウザー セッションまたはタブから直接 URL に移動する場合に、このエラーが発生します。 これは、自分のセッションがより安全で、セッションが Azure portal からのみアクセスできることを確保するのに役立ちます。
