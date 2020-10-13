---
title: インクルード ファイル
description: インクルード ファイル
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 10/06/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: e1a9256cc9a015a5d8286de5e5bd7b61ed915a3b
ms.sourcegitcommit: 5abc3919a6b99547f8077ce86a168524b2aca350
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/07/2020
ms.locfileid: "91812753"
---
1. 仮想 WAN のページで、 **[User VPN configurations]\(ユーザー VPN の構成\)** を選択します。
1. ページの上部にある **[Download user VPN config]\(ユーザー VPN 構成をダウンロードする\)** を選択します。WAN レベルの構成をダウンロードすると、組み込みの Traffic Manager ベースのユーザー VPN プロファイルを入手できます。 グローバル プロファイルまたはハブベースのプロファイルの詳細については、[ハブ プロファイル](https://docs.microsoft.com/azure/virtual-wan/global-hub-profile)に関する記事をご覧ください。 グローバル プロファイルを使用すると、フェールオーバーのシナリオが単純化されます。

   なんらかの理由でハブが利用できない場合、サービスによって提供される組み込みのトラフィック管理によって、(別のハブを介した) Azure リソースへの接続がポイント対サイト ユーザー用に確保されます。 ハブ固有の VPN 構成は、そのハブに移動することでいつでもダウンロードできます。 **[ユーザー VPN (ポイントからサイトへ)]** で、仮想ハブの**ユーザー VPN** プロファイルをダウンロードします。
1. ファイルの作成が完了したら、リンクを選択してファイルをダウンロードします。
1. プロファイル ファイルを使用して VPN クライアントを構成します。
