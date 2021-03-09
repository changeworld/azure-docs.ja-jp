---
title: 'Azure ExpressRoute: Azure portal を使用して Global Reach を構成する'
description: この記事では、ExpressRoute 回線を相互にリンクしてオンプレミス ネットワーク間にプライベート ネットワークを構築し、Azure portal を使用して Global Reach を有効にする方法について説明します。
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 01/11/2021
ms.author: duau
ms.openlocfilehash: 8366978d50875389ce872c2d1402f0defa2a7371
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/04/2021
ms.locfileid: "99539351"
---
# <a name="configure-expressroute-global-reach-using-the-azure-portal"></a>Azure portal を使用して ExpressRoute Global Reach を構成する

この記事は、PowerShell を使用して ExpressRoute Global Reach を構成する際に役立ちます。 詳細については、[ExpressRouteRoute Global Reach](expressroute-global-reach.md) に関するページを参照してください。

 ## <a name="before-you-begin"></a>開始する前に

構成を開始する前に、次の条件をご確認ください。

* ExpressRoute 回線のプロビジョニング [ワークフロー](expressroute-workflows.md)を理解していること。
* ExpressRoute 回線がプロビジョニングされた状態にあること。
* ExpressRoute 回線上に Azure プライベート ピアリングが構成されていること。
* PowerShell をローカルで実行する場合は、Azure PowerShell の最新バージョンがコンピューターにインストールされていることを確認します。

## <a name="identify-circuits"></a>回線を特定する

1. ブラウザーから [Azure ポータル](https://portal.azure.com) に移動し、Azure アカウントでサインインします。

2. 使用する ExpressRoute 回線を特定します。 任意の 2 つの ExpressRoute 回線のプライベート ピアリング間で ExpressRoute Global Reach を有効にすることができますが、それは、それらがサポートされている国や地域に配置されている場合に限られます。 回線は別々のピアリング場所に作成されている必要があります。 

   * サブスクリプションが両方の回線を所有している場合は、以降のセクションで構成を実行するために、どちらかの回線を選択できます。
   * 2 つの回線が異なる Azure サブスクリプションに含まれている場合は、1 つの Azure サブスクリプションからの承認が必要です。 その後、もう一方の Azure サブスクリプションで構成コマンドを実行するときに承認キーを渡します。

    :::image type="content" source="./media/expressroute-howto-set-global-reach-portal/expressroute-circuit-global-reach-list.png" alt-text="ExpressRoute 回線の一覧":::

## <a name="enable-connectivity"></a>接続性の確保

オンプレミス ネットワーク間の接続を有効にします。 同じ Azure サブスクリプション内にある回線と、異なるサブスクリプションである回線には、異なるセットの手順があります。

### <a name="expressroute-circuits-in-the-same-azure-subscription"></a>同じ Azure サブスクリプション内の ExpressRoute 回線

1. **[Azure プライベート]** のピアリング構成を選択します。 

    :::image type="content" source="./media/expressroute-howto-set-global-reach-portal/expressroute-circuit-private-peering.png" alt-text="ExpressRoute ピアリングの概要":::

1. **[Global Reach を有効にする]** チェック ボックスをオンにし、 **[Global Reach の追加]** を選択して *[Global Reach の追加]* 構成ページを開きます。

    :::image type="content" source="./media/expressroute-howto-set-global-reach-portal/private-peering-enable-global-reach.png" alt-text="プライベート ピアリングから Global Reach を有効にする":::

1. *[Global Reach の追加]* 構成ページで、この構成の名前を指定します。 この回線に接続する "*ExpressRoute 回線*" を選択し、 *[Global Reach subnet]\(Global Reach サブネット\)* に **/29 の IPv4** を入力します。 このサブネット内の IP アドレスを使用して、2 つの ExpressRoute 回線間の接続を確立します。 このサブネット内のアドレスは Azure 仮想ネットワークやオンプレミス ネットワークで使用しないでください。 **[追加]** を選択して、回線をプライベート ピアリング構成に追加します。

    :::image type="content" source="./media/expressroute-howto-set-global-reach-portal/add-global-reach-configuration.png" alt-text="Global Reach の構成ページ":::

1. **[保存]** を選択して Global Reach の構成を完了します。 操作が完了すると、両方の ExpressRoute 回線を介して 2 つのオンプレミス ネットワーク間の接続があります。

    :::image type="content" source="./media/expressroute-howto-set-global-reach-portal/save-private-peering-configuration.png" alt-text="プライベート ピアリング構成を保存する":::

### <a name="expressroute-circuits-in-different-azure-subscriptions"></a>異なる Azure サブスクリプションの ExpressRoute 回線

2 つの回線が同じ Azure サブスクリプション内にない場合は、承認が必要になります。 次の構成では、回線 2 のサブスクリプションから承認が生成されます。 それから承認キーが回線 1 に渡されます。

1. 承認キーを生成します。

   :::image type="content" source="./media/expressroute-howto-set-global-reach-portal/create-authorization-expressroute-circuit.png" alt-text="承認キーを生成する"::: 

   回線 2 の回線リソース ID と承認キーをメモします。

1. **[Azure プライベート]** のピアリング構成を選択します。 

    :::image type="content" source="./media/expressroute-howto-set-global-reach-portal/expressroute-circuit-private-peering.png" alt-text="回線 1 のピアリングの概要":::

1. **[Global Reach を有効にする]** チェック ボックスをオンにし、 **[Global Reach の追加]** を選択して *[Global Reach の追加]* 構成ページを開きます。

    :::image type="content" source="./media/expressroute-howto-set-global-reach-portal/private-peering-enable-global-reach.png" alt-text="回線 1 からの Global Reach を有効にする":::

1. *[Global Reach の追加]* 構成ページで、この構成の名前を指定します。 **[承認の利用]** チェック ボックスをオンにします。 手順 1 で生成および取得した **承認キー** と **ExpressRoute 回線 ID** を入力します。 次に、 *[Global Reach subnet]\(Global Reach サブネット\)* に **/29 の IPv4** を入力します。 このサブネット内の IP アドレスを使用して、2 つの ExpressRoute 回線間の接続を確立します。 このサブネット内のアドレスは Azure 仮想ネットワークやオンプレミス ネットワークで使用しないでください。 **[追加]** を選択して、回線をプライベート ピアリング構成に追加します。

    :::image type="content" source="./media/expressroute-howto-set-global-reach-portal/add-global-reach-configuration-with-authorization.png" alt-text="承認キーを使用して Global Reach を追加する":::

1. **[保存]** を選択して Global Reach の構成を完了します。 操作が完了すると、両方の ExpressRoute 回線を介して 2 つのオンプレミス ネットワーク間の接続があります。

    :::image type="content" source="./media/expressroute-howto-set-global-reach-portal/save-private-peering-configuration.png" alt-text="回線 1 でのプライベート ピアリング構成を保存する":::

## <a name="verify-the-configuration"></a>構成を確認する

ExpressRoute 回線の構成にある *[プライベート ピアリング]* を選択して、Global Reach 構成を確認します。 正しく構成されている場合、構成は次のようになります。

:::image type="content" source="./media/expressroute-howto-set-global-reach-portal/verify-global-reach-configuration.png" alt-text="Global Reach 構成を確認する":::

## <a name="disable-connectivity"></a>接続の無効化

Global Reach を無効にする場合は、2 つのオプションがあります。 すべての回線間の接続を無効にするには、 **[Global Reach を有効にする]** をオフにすることで、すべての回線間の接続を無効にします。 個々の回線間の接続を無効にするには、 *[Global Reach name]\(グローバル リーチ名\)* の横にある削除ボタンを選択して、それらの間の接続を削除します。 次に、 **[保存]** を選択して操作を完了します。

:::image type="content" source="./media/expressroute-howto-set-global-reach-portal/disable-global-reach-configuration.png" alt-text="Global Reach を無効にする構成":::

操作が完了すると、ExpressRoute 回線を経由したオンプレミス ネットワーク間の接続は存在しなくなります。

## <a name="next-steps"></a>次のステップ
1. [ExpressRoute Global Reach について詳しく学習する](expressroute-global-reach.md)
2. [ExpressRoute 接続を確認する](expressroute-troubleshooting-expressroute-overview.md)
3. [ExpressRoute 回線を Azure 仮想ネットワークにリンクする](expressroute-howto-linkvnet-arm.md)
