---
title: Azure Active Directory 認証を使用して P2S ユーザー VPN 接続を構成する
titleSuffix: Azure Virtual WAN
description: Virtual WAN のユーザー VPN (ポイント対サイト) 用に Azure Active Directory 認証を構成する方法について説明します。
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 08/20/2021
ms.author: cherylmc
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 38006136ddd5a5331699b2768c35ebd794e563a8
ms.sourcegitcommit: 28cd7097390c43a73b8e45a8b4f0f540f9123a6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/24/2021
ms.locfileid: "122778592"
---
# <a name="configure-a-point-to-site-user-vpn-connection---azure-active-directory-authentication"></a>ポイント対サイト ユーザー VPN 接続を構成する - Azure Active Directory 認証

この記事では、Virtual WAN のユーザー VPN 用に Azure AD 認証を構成し、OpenVPN VPN 接続経由で Azure のリソースに接続する方法について説明します。 Azure Active Directory 認証は、OpenVPN プロトコルを使用するゲートウェイでのみ使用できます。 Virtual WAN の詳細については、[Virtual WAN の概要](virtual-wan-about.md)に関するページを参照してください。

[!INCLUDE [OpenVPN note](../../includes/vpn-gateway-openvpn-auth-include.md)]

この記事では、次のことについて説明します。

* 仮想 WAN を作成する
* ユーザー VPN 構成を作成する
* 仮想 WAN ユーザー VPN プロファイルをダウンロードする
* 仮想ハブを作成する
* ハブを編集して P2S ゲートウェイを追加する
* VNet を仮想ハブに接続する
* ユーザー VPN クライアント構成をダウンロードして適用する
* 仮想 WAN を表示する

:::image type="content" source="./media/virtual-wan-about/virtualwanp2s.png" alt-text="仮想 WAN の図。":::

## <a name="before-you-begin"></a>開始する前に

構成を開始する前に、以下の条件を満たしていることを確認します。

* 接続先の仮想ネットワークが用意されていること。 オンプレミス ネットワークのどのサブネットも接続先の仮想ネットワークと重複していないことを確認してください。 Azure portal で仮想ネットワークを作成するには、[クイックスタート](../virtual-network/quick-create-portal.md)を参照してください。

* 仮想ネットワークに仮想ネットワーク ゲートウェイが存在しないこと。 仮想ネットワークにゲートウェイ (VPN または ExpressRoute のどちらか) が存在する場合は、すべてのゲートウェイを削除する必要があります。 代わりに、この構成では、仮想ネットワークが Virtual WAN ハブ ゲートウェイに接続されている必要があります。

* ハブ リージョンの IP アドレス範囲を取得します。 このハブは、Virtual WAN によって作成および使用される仮想ネットワークです。 ハブに指定するアドレス範囲が、接続先の既存の仮想ネットワークと重複することはできません。 さらに、オンプレミスで接続するアドレス範囲と重複することもできません。 オンプレミス ネットワーク構成に含まれている IP アドレス範囲になじみがない場合は、それらの詳細を提供できるだれかと調整してください。

* Azure サブスクリプションをお持ちでない場合は、[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成してください。

## <a name="create-a-virtual-wan"></a><a name="wan"></a>仮想 WAN を作成する

ブラウザーから [Azure ポータル](https://portal.azure.com) に移動し、Azure アカウントでサインインします。

[!INCLUDE [Create a virtual WAN](../../includes/virtual-wan-create-vwan-include.md)]

## <a name="create-a-user-vpn-configuration"></a><a name="user-config"></a>ユーザー VPN 構成を作成する

ユーザー VPN 構成には、リモート クライアントを接続するためのパラメーターが定義されています。 使用するユーザー VPN 構成を指定する必要があるため、P2S の設定で仮想ハブを構成する前に、ユーザー VPN 構成を作成することが重要です。

1. **[仮想 WAN] -> [ユーザー VPN 構成]** ページに移動し、 **[+ ユーザー VPN 構成の作成]** をクリックします。

   :::image type="content" source="./media/virtual-wan-point-to-site-azure-ad/user-vpn.png" alt-text="ユーザー VPN 構成の作成のスクリーンショット。":::
1. **[基本]** ページでパラメーターを指定します。

   :::image type="content" source="./media/virtual-wan-point-to-site-azure-ad/basics.png" alt-text="[基本] ページのスクリーンショット。":::

   * **構成名** - ユーザー VPN 構成に付ける名前を入力します。
    * **[トンネルの種類]** - ドロップダウン メニューから [OpenVPN] を選択します。
1. **[Azure Active Directory]** をクリックしてページを開きます。

   :::image type="content" source="./media/virtual-wan-point-to-site-azure-ad/values.png" alt-text="[Azure Active Directory] ページのスクリーンショット。":::

    **[Azure Active Directory]** を **[はい]** に切り替え、テナントの詳細に基づいて次の値を指定します。 必要な値は、ポータルのエンタープライズ アプリケーションの [Azure Active Directory] ページで確認できます。
   * **認証方法**: [Azure Active Directory] を選択します。
   * **対象ユーザー** - Azure AD テナントに登録されている [Azure VPN](openvpn-azure-ad-tenant.md) エンタープライズ アプリケーションのアプリケーション ID を入力します。 
   * **発行者** - `https://sts.windows.net/<your Directory ID>/`
   * **AAD テナント** - `https://login.microsoftonline.com/<your Directory ID>`
1. **[作成]** をクリックしてユーザー VPN 構成を作成します。 この構成は、演習で後ほど選択します。

## <a name="create-an-empty-hub"></a><a name="site"></a>空のハブを作成する

この演習では、空の仮想ハブを作成します。 次のセクションでは、既存のハブにゲートウェイを追加します。 ただし、これらのステップを組み合わせて、ハブと P2S ゲートウェイの設定を一度に作成することもできます。

[!INCLUDE [Create an empty hub](../../includes/virtual-wan-empty-hub-include.md)]

## <a name="add-a-p2s-gateway-to-a-hub"></a><a name="hub"></a>ハブに P2S ゲートウェイを追加する

このセクションでは、既存の仮想ハブにゲートウェイを追加する方法を示します。 このステップでハブの更新が完了するまで、最大で 30 分かかることがあります。

1. 仮想 WAN の下にある **[ハブ]** ページに移動します。
1. VPN サーバーの構成を関連付けるハブを選択し、省略記号 **[...]** をクリックしてメニューを表示します。 次に、 **[仮想ハブを編集する]** をクリックします。

   :::image type="content" source="media/virtual-wan-point-to-site-azure-ad/select-hub.png" alt-text="メニューで [仮想ハブを編集する] が選択されていることを示すスクリーンショット。" lightbox="media/virtual-wan-point-to-site-azure-ad/select-hub.png":::

1. **[仮想ハブを編集する]** ページで、 **[VPN サイト用の VPN ゲートウェイを含める]** と **[ポイント対サイト ゲートウェイを含める]** のチェック ボックスをオンにして、設定を表示します。 次に、値を構成します。

   :::image type="content" source="./media/virtual-wan-point-to-site-azure-ad/edit-virtual-hub.png" alt-text="[仮想ハブを編集する] ページを示すスクリーンショット。":::

   * **[ゲートウェイ スケール ユニット]** : ゲートウェイ スケール ユニットを選択します。 スケール ユニットは、ユーザー VPN ゲートウェイの合計容量を表します。 40 以上のゲートウェイ スケール ユニットを選択した場合は、それに応じてクライアント アドレス プールを計画します。 この設定がクライアント アドレス プールに与える影響の詳細については、[クライアント アドレス プールについて](about-client-address-pools.md)の記事を参照してください。 ゲートウェイ スケール ユニットについては、[FAQ](virtual-wan-faq.md#for-user-vpn-point-to-site--how-many-clients-are-supported) に関する記事を参照してください。
   * **[ユーザー VPN 構成]** : 前に作成した構成を選択します。
   * **[クライアント アドレス プール]** : VPN クライアントに割り当てる IP アドレスが含まれるクライアント アドレス プールを指定します。 この設定は、指定したゲートウェイ スケール ユニットに対応します 
1. **[Confirm]\(確認\)** をクリックします。 ハブの更新には最大 30 分かかる場合があります。

## <a name="connect-vnet-to-hub"></a><a name="connect-vnet"></a>VNet をハブに接続する

このセクションでは、仮想ハブと VNet の間の接続を作成します。

[!INCLUDE [Connect virtual network](../../includes/virtual-wan-connect-vnet-hub-include.md)]

## <a name="download-user-vpn-profile"></a><a name="device"></a>ユーザー VPN プロファイルをダウンロードする

VPN クライアントに必要なすべての構成設定は、VPN クライアント構成 ZIP ファイルに含まれています。 ZIP ファイル内の設定を使用することで、VPN クライアントを簡単に構成できます。 生成する VPN クライアント構成ファイルは、お使いのゲートウェイのユーザー VPN 構成に固有です。 このセクションでは、VPN クライアントの構成に使用するファイルを生成してダウンロードします。

[!INCLUDE [Download profile](../../includes/virtual-wan-p2s-download-profile-include.md)]

## <a name="configure-user-vpn-clients"></a>ユーザー VPN クライアントを構成する

接続する各コンピューターには、クライアントがインストールされている必要があります。 前の手順でダウンロードした VPN ユーザー クライアント プロファイル ファイルを使用して、各クライアントを構成します。 接続するオペレーティング システムに関連する記事を使用してください。

### <a name="to-configure-macos-vpn-clients-preview"></a>macOS VPN クライアントを構成するには (プレビュー)

**macOS** クライアントの手順については、[VPN クライアントの構成 - macOS (プレビュー)](openvpn-azure-ad-client-mac.md) に関する記事を参照してください。

### <a name="to-configure-windows-vpn-clients"></a>Windows VPN クライアントを構成するには

[!INCLUDE [Download Azure VPN client](../../includes/vpn-gateway-download-vpn-client.md)]

#### <a name="to-import-a-vpn-client-profile-windows"></a><a name="import"></a>VPN クライアント プロファイルをインポートするには (Windows)

1. ページ上で、 **[インポート]** を選択します。

   :::image type="content" source="./media/virtual-wan-point-to-site-azure-ad/import/import-1.png" alt-text="[インポート] ページを示すスクリーンショット。":::

1. プロファイル xml ファイルを参照し、選択します。 ファイルが選択された状態で、 **[開く]** を選択します。

    ![[開く] ダイアログ ボックスを示すスクリーンショット。ここでは、ファイルを選択できます。](./media/virtual-wan-point-to-site-azure-ad/import/import2.jpg)

1. プロファイルの名前を指定し、 **[保存]** を選択します。

    ![[接続名] が追加され、[保存] ボタンが選択されていることを示すスクリーンショット。](./media/virtual-wan-point-to-site-azure-ad/import/import3.jpg)

1. **[接続]** を選択して VPN に接続します。

    ![作成したばかりの接続用の [接続] ボタンを示すスクリーンショット。](./media/virtual-wan-point-to-site-azure-ad/import/import4.jpg)

1. 接続されると、アイコンが緑色に変わり、 **[接続済み]** と表示されます。

    ![[接続済み] 状態の接続と [切断] オプションを示すスクリーンショット。](./media/virtual-wan-point-to-site-azure-ad/import/import5.jpg)

#### <a name="to-delete-a-client-profile---windows"></a><a name="delete"></a>クライアント プロファイルを削除するには - Windows

1. 削除するクライアント プロファイルの横にある省略記号 (...) を選択します。 **[削除]** を選択します。

    ![メニューで [削除] が選択されていることを示すスクリーンショット。](./media/virtual-wan-point-to-site-azure-ad/delete/delete1.jpg)

1. **[削除]** を選択して削除します。

    ![[確認] ダイアログ ボックスと [削除] オプションまたは [キャンセル] オプションを示すスクリーンショット。](./media/virtual-wan-point-to-site-azure-ad/delete/delete2.jpg)

#### <a name="diagnose-connection-issues---windows"></a><a name="diagnose"></a>接続の問題を診断する - Windows

1. 接続の問題を診断するには、**診断** ツールを使用します。 診断する VPN 接続の横にある省略記号 (...) を選択して、メニューを表示します。 次に、 **[診断]** を選択します。

    ![メニューで [診断] が選択されていることを示すスクリーンショット。](./media/virtual-wan-point-to-site-azure-ad/diagnose/diagnose1.jpg)

1. **[接続プロパティ]** ページで、 **[Run Diagnosis]\(診断の実行\)** を選択します。

    ![接続の [Run Diagnosis]\(診断の実行\) ボタンを示すスクリーンショット。](./media/virtual-wan-point-to-site-azure-ad/diagnose/diagnose2.jpg)

1. 自分の資格情報でサインインします。

    ![このアクションの [サインイン] ダイアログ ボックスを示すスクリーンショット。](./media/virtual-wan-point-to-site-azure-ad/diagnose/diagnose3.jpg)

1. 診断結果を確認します。

    ![診断の結果を示すスクリーンショット。](./media/virtual-wan-point-to-site-azure-ad/diagnose/diagnose4.jpg)

## <a name="view-your-virtual-wan"></a><a name="viewwan"></a>仮想 WAN を表示する

1. 仮想 WAN に移動します。
2. [概要] ページのマップ上の各ポイントは、ハブを表します。
3. ハブと接続のセクションでは、ハブの状態、サイト、リージョン、VPN 接続の状態、および入出力バイト数を表示できます。

## <a name="clean-up-resources"></a><a name="cleanup"></a>リソースをクリーンアップする

作成したリソースは、不要になったら削除してください。 Virtual WAN リソースのいくつかは、依存関係に応じた特定の順序で削除する必要があります。 削除が完了するまでに 30 分程度かかる場合があります。

[!INCLUDE [Delete resources](../../includes/virtual-wan-resource-cleanup.md)]

## <a name="next-steps"></a>次のステップ

Virtual WAN の詳細については、[Virtual WAN の概要](virtual-wan-about.md)に関するページを参照してください。
