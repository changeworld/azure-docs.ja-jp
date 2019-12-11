---
title: インクルード ファイル
description: インクルード ファイル
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: a826bb01a906153b4ca2a3c854623c83b6192aec
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/04/2019
ms.locfileid: "74828884"
---
Azure Portal を使用して Resource Manager デプロイ モデルで VNet を作成するには、次の手順に従います。 スクリーンショットは例として示されています。 サンプルの値は必ず実際の値に変更してください。 仮想ネットワークの操作の詳細については、「 [仮想ネットワークの概要](../articles/virtual-network/virtual-networks-overview.md)」を参照してください。

>[!NOTE]
>P2S 構成を作成するだけでなく、この VNet をオンプレミスの場所に接続するには、オンプレミスのネットワーク管理者と調整を行って、この仮想ネットワーク専用に使用できる IP アドレスの範囲を見つけ出す必要があります。 VPN 接続の両側に重複するアドレス範囲が存在する場合、トラフィックが期待どおりにルーティングされない可能性があります。 また、この VNet を別の VNet に接続する場合、アドレス空間を他の VNet と重複させることはできません。 したがって、慎重にネットワーク構成を計画してください。
>
>

1. [Azure Portal](https://portal.azure.com) にサインインします。  Azure portal メニューで、または **[ホーム]** ページから、 **[リソースの作成]** を選択します。 **[新規]** ページが開きます。

2. **[マーケットプレースを検索]** に「*仮想ネットワーク*」と入力し、その結果から **[仮想ネットワーク]** を選択します。

   ![仮想ネットワークのリソース ページを探す](./media/vpn-gateway-basic-p2s-vnet-rm-portal-include/search-marketplace-for-virtual-network.png "仮想ネットワークのリソース ページを探す")

3. [仮想ネットワーク] ページの下の方にある **[デプロイ モデルの選択]** の一覧で、 **[リソース マネージャー]** を選択し、 **[作成]** をクリックします。

   ![リソース マネージャーを選択する](./media/vpn-gateway-basic-p2s-vnet-rm-portal-include/resourcemanager250.png "Select Resource Manager")
4. **[仮想ネットワークの作成]** ページで、VNet の設定を構成します。 フィールドへの入力時、入力された文字が有効であれば、赤色の感嘆符が緑色のチェック マークに変わります。 自動的に入力される値もあります。 そのような値については、実際の値に変更してください。 **[仮想ネットワークの作成]** ページは、次のようになっています。

   ![フィールドの検証](./media/vpn-gateway-basic-p2s-vnet-rm-portal-include/vnetp2s.png "Field validation")
5. **Name**:仮想ネットワークの名前を入力します。
6. **[アドレス空間]** : アドレス空間を入力します。 追加するアドレス空間が複数ある場合は、1 つ目のアドレス空間を追加してください。 その他のアドレス空間は後で、VNet を作成した後に追加できます。
7. **サブスクリプション**:一覧表示されているサブスクリプションが正しいことを確認します。 ドロップダウンを使用して、サブスクリプションを変更できます。
8. **[リソース グループ]** :既存のリソース グループを選択するか、新しいリソース グループの名前を入力して新しく作成します。 新しいグループを作成する場合は、計画した構成値に基づいて、リソース グループに名前を付けます。 リソース グループの詳細については、「 [Azure リソース マネージャーの概要](../articles/azure-resource-manager/resource-group-overview.md#resource-groups)」を参照してください。
9. **[場所]** :VNet の場所を選択します。 この場所の設定によって、この VNet にデプロイしたリソースの配置先が決まります。
10. **サブネット**:サブネットの名前とアドレス範囲を追加します。 その他のサブネットは後で、VNet を作成した後に追加できます。
11. ダッシュボードで VNet を簡単に検索できるようにするには、 **[ダッシュボードにピン留めする]** を選択します。その後、 **[作成]** をクリックします。

    ![ダッシュボードにピン留めする](./media/vpn-gateway-basic-p2s-vnet-rm-portal-include/pintodashboard150.png "[ダッシュボードにピン留めする]")
12. **[作成]** をクリックした後で、VNet の進捗状況を反映するタイルがダッシュボードに表示されます。 タイルは、VNet の作成が進むに従って変化します。

    ![仮想ネットワーク作成中のタイル](./media/vpn-gateway-basic-p2s-vnet-rm-portal-include/deploying150.png "仮想ネットワーク タイルの作成")
