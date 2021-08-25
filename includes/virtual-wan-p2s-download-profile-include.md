---
ms.author: cherylmc
author: cherylmc
ms.date: 07/29/2021
ms.service: virtual-wan
ms.topic: include
ms.openlocfilehash: dc99ee5068819b67338ae84dd2fef561105d8351
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121734605"
---
1. **仮想 WAN** のページで、 **[ユーザー VPN 構成]** を選択します。
1. **[ユーザー VPN 構成]** ページで、構成を選択し、 **[仮想 WAN のユーザー VPN プロファイルのダウンロード]** を選択します。

   :::image type="content" source="./media/virtual-wan-p2s-download-profile/download.png" alt-text="[仮想 WAN のユーザー VPN プロファイルのダウンロード] のスクリーンショット。":::

   * WAN レベルの構成をダウンロードすると、組み込みの Traffic Manager ベースのユーザー VPN プロファイルを入手できます。 
   
   * グローバル プロファイルおよびハブベースのプロファイルの情報については、[ハブ プロファイル](../articles/virtual-wan/global-hub-profile.md)に関する記事をご覧ください。 グローバル プロファイルを使用すると、フェールオーバーのシナリオが単純化されます。

   * なんらかの理由でハブが利用できない場合、サービスによって提供される組み込みのトラフィック管理によって、(別のハブを介した) Azure リソースへの接続がポイント対サイト ユーザー用に確保されます。 ハブ固有の VPN 構成は、そのハブに移動することでいつでもダウンロードできます。 **[ユーザー VPN (ポイントからサイトへ)]** で、仮想ハブの **ユーザー VPN** プロファイルをダウンロードします。
1. **[仮想 WAN のユーザー VPN プロファイルのダウンロード]** ページで、 **[認証の種類]** を選択し、次に **[プロファイルを生成してダウンロードする]** をクリックします。 クライアントの構成設定を含むプロファイル パッケージ (ZIP ファイル) が生成され、コンピューターにダウンロードされます。

   :::image type="content" source="./media/virtual-wan-p2s-download-profile/generate.png" alt-text="[プロファイルを生成してダウンロードする] のスクリーンショット。":::

