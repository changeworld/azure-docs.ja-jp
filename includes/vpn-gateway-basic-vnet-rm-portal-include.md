---
title: インクルード ファイル
description: インクルード ファイル
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/25/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 872ba86c9e43b1f6642331908eb829605f6c19bd
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/26/2020
ms.locfileid: "77619701"
---
VNet は、Resource Manager デプロイ モデルと Azure portal を使用し、以下の手順に従って作成できます。 仮想ネットワークの詳細については、[Virtual Network の概要](../articles/virtual-network/virtual-networks-overview.md)に関するページを参照してください。

>[!NOTE]
>この VNet をオンプレミスの場所に接続するには、オンプレミスのネットワーク管理者と調整を行って、この仮想ネットワーク専用に使用できる IP アドレスの範囲を見つけ出します。 VPN 接続の両側に重複するアドレス範囲が存在する場合、予期しない方法でトラフィックがルーティングされます。 また、この VNet を別の VNet に接続する場合、アドレス空間を他の VNet と重複させることはできません。 この点を踏まえてネットワーク構成を計画してください。
>
>

1. [Azure portal](https://portal.azure.com) にサインインします。
1. **[Search resources, service, and docs (G+/)]\(リソース、サービス、ドキュメントを検索する (G+/)\)** に「*virtual network*」と入力します。

   ![仮想ネットワークのリソース ページを探す](./media/vpn-gateway-basic-vnet-rm-portal-include/marketplace.png "仮想ネットワークのリソース ページを探す")
1. **[Marketplace]** 結果から **[Virtual Network]** を選択します。

   ![仮想ネットワークを選択する](./media/vpn-gateway-basic-vnet-rm-portal-include/marketplace-results.png "仮想ネットワークのリソース ページを探す")
1. **[仮想ネットワーク]** ページで、 **[作成]** をクリックします。

   ![仮想ネットワーク ページ](./media/vpn-gateway-basic-vnet-rm-portal-include/vnet-click-create.png "Create をクリックしてください。")
1. [作成] をクリックすると、 **[仮想ネットワークの作成]** ページが開きます。

   ![[仮想ネットワークの作成]](./media/vpn-gateway-basic-vnet-rm-portal-include/create-virtual-network-page.png "[仮想ネットワークの作成] ページ") ページ
1. **[仮想ネットワークの作成]** ページで、VNet の設定を構成します。 フィールドへの入力時、入力された文字の有効性が確認されれば、赤色の感嘆符が緑色のチェック マークに変わります。 いくつかの値は自動入力されます。実際の値に置き換えてください。

   - **Name**:仮想ネットワークの名前を入力します。
   - **[アドレス空間]** : アドレス空間を入力します。 追加するアドレス空間が複数ある場合、ここには、1 つ目のアドレス空間を入力してください。 その他のアドレス空間は後で、VNet を作成した後に追加できます。 構成に IPv6 アドレス空間が必要な場合、チェックボックスにチェックマークを入れ、その情報を入力します。
   - **サブスクリプション**:一覧表示されているサブスクリプションが正しいことを確認します。 ドロップダウンを使用して、サブスクリプションを変更できます。
   - **[リソース グループ]** :既存のリソース グループを選択するか、新しいリソース グループの名前を入力して新しく作成します。 新しいグループを作成する場合は、計画した構成値に基づいて、リソース グループに名前を付けます。 リソース グループの詳細については、「[Azure Resource Manager の概要](../articles/azure-resource-manager/management/overview.md#resource-groups)」を参照してください。
   - **[場所]** :VNet の場所を選択します。 この場所の設定によって、この VNet にデプロイしたリソースの配置先が決まります。
   - **サブネット**:サブネットの**名前**と**アドレス範囲**を追加します。 その他のサブネットは後で、VNet を作成した後に追加できます。
   - **DDos 防御**:Standard サービスを使用する場合を除き、 **[Basic]** を選択します。
   - **[サービス エンドポイント]** :お使いの構成によって設定が指定されない限り、この設定は **[無効]** のままにします。
   - **ファイアウォール**:お使いの構成によって設定が指定されない限り、この設定は **[無効]** のままにします。
1. **[作成]** をクリックし、仮想ネットワークのデプロイを開始します。
