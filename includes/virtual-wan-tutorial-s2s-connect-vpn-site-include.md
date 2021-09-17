---
ms.author: cherylmc
author: cherylmc
ms.date: 08/18/2021
ms.service: virtual-wan
ms.topic: include
ms.openlocfilehash: 53c6980ea6e5d868a842b1e14031f27ff51f655a
ms.sourcegitcommit: d43193fce3838215b19a54e06a4c0db3eda65d45
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2021
ms.locfileid: "122638218"
---
1. **[仮想ハブ] -> [VPN (サイト間)]** に移動します。

1. フィルターをクリアしてサイトを表示するために、 **[Hub association: Connected to this hub]\(ハブの関連付け: このハブに接続する\)** をクリックすることが必要な場合もあります。

1. 接続するサイトのチェック ボックスをオンにし、 **[VPN サイトの接続]** をクリックします。

   :::image type="content" source="./media/virtual-wan-tutorial-connect-vpn-site-include/connect-site.png" alt-text="[サイトの接続] を示すスクリーンショット。":::

1. **[サイトの接続]** ページで、必要な設定を構成します。

   :::image type="content" source="./media/virtual-wan-tutorial-connect-vpn-site-include/connect.png" alt-text="スクリーンショットには、[サイトの接続] ペインが表示されています。仮想ハブの事前共有キーと関連設定を指定できます。":::

   * **[Pre-shared key (PSK)]\(事前共有キー (PSK)\)** : VPN デバイスで使用される事前共有キーを入力します。 キーを入力しない場合、Azure により、キーが自動的に生成されます。 その場合、VPN デバイスを構成するときにそのキーを使用します。
   * **[プロトコル] と [IPsec]** :　プロトコル (IKEv2) と IPsec (既定値) の既定の設定のままにするか、カスタム設定を構成できます。 詳細については、[既定またはカスタムの IPsec](../articles/virtual-wan/virtual-wan-ipsec.md) に関する記事を参照してください。
   * **[Propagate Default Route]\(既定ルートの伝達\)** : 既定ルートを伝達する必要がある場合のみ、この設定を **[有効]** に変更します。 それ以外の場合は、 **[無効]** のままにします。 この設定は後でいつでも変更できます。 
   
     **[有効]** オプションを選択すると、仮想ハブは、学習した既定のルートをこの接続に伝達できます。 このフラグは、Virtual WAN ハブにファイアウォールをデプロイした結果としてそのハブにより既定のルートが既に学習されている場合、または接続されている別のサイトでトンネリングが強制的に有効にされている場合のみ、接続に対して既定のルートの伝達を有効にします。 既定のルートの起点は Virtual WAN ハブ内にありません。 
   * **[Use policy based traffic selector]\(ポリシーベースのトラフィック セレクターを使用\)** : この設定を使用するデバイスへの接続を構成する場合を除き、この設定は **[無効]** のままにしてください。

1. ページの下部にある **[接続]** をクリックします。

1. **[接続]** をクリックすると、接続ステータスは **[更新中]** と表示されます。 更新が完了すると、サイトに接続と接続性の状態が表示されます。

   :::image type="content" source="./media/virtual-wan-tutorial-connect-vpn-site-include/connectivity-status.png" alt-text="スクリーンショットには、サイト間接続と接続性の状態が示されています。" lightbox="./media/virtual-wan-tutorial-connect-vpn-site-include/connectivity-status.png":::

   **[接続のプロビジョニングの状態]** : これは、VPN サイトを Azure ハブの VPN ゲートウェイに接続する接続の Azure リソースの状態です。 このコントロール プレーンの操作が正常に終了すると、Azure VPN ゲートウェイとオンプレミスの VPN デバイスは、接続の確立に進みます。

   **[接続性の状態]** : これは、ハブ内の Azure の VPN ゲートウェイと VPN サイト間の接続性 (データ パス) の実際の状態です。 更新が完了すると、次のいずれかの状態が表示されます。

    * **不明**:通常、この状態は、バックエンド システムが別の状態に移行しようとしている場合に表示されます。
    * **接続中**: VPN ゲートウェイは、実際のオンプレミスの VPN サイトに接続しようとしています。
    * **接続済み**: VPN ゲートウェイとオンプレミスの VPN サイトの間に接続が確立されています。
   * **未接続**: 接続が確立されていません。
    * **切断**: この状態は、何らかの理由 (オンプレミスまたは Azure 内) で、接続が切断された場合に表示されます。
1. サイトを変更する場合は、サイトを選択し、 **[...]** のコンテキスト メニューをクリックします。

   :::image type="content" source="./media/virtual-wan-tutorial-connect-vpn-site-include/edit.png" alt-text="スクリーンショットには、編集、削除、ダウンロードが表示されています。" lightbox="./media/virtual-wan-tutorial-connect-vpn-site-include/expand/edit.png":::

   このページでは、次のことができます。 

   * VPN 接続の編集または削除。
   * このハブへの VPN 接続の削除。
   * Azure サイトの詳細に関するブランチ固有の構成のダウンロード。 ハブ内のすべての接続済みサイトに関連する構成ファイルをダウンロードする場合は、代わりにページの上部にあるメニューから **[VPN 構成のダウンロード]** を選択します。
