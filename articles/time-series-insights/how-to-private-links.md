---
title: プライベート リンクを有効にする - Azure Time Series Insights Gen2 | Microsoft Docs
description: Private Link を使用した Azure Time Series Insights Gen2 ソリューションのプライベート アクセスを、Azure portal を使用して有効にする方法について説明します。
author: tedvilutis
ms.author: tvilutis
manager: cnovak
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 09/01/2021
ms.openlocfilehash: 21a6c529aaa086fa75aef9e665cbbdce882baf5a
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/26/2021
ms.locfileid: "129061941"
---
# <a name="enable-private-access-for-tsi-with-private-link-preview"></a>Private Link を使用して TSI のプライベート アクセスを有効にする (プレビュー)

この記事では、[Azure Time Series Insights Gen2 環境のプライベート エンドポイントを使用してプライベート リンクを有効にする](concepts-private-links.md)方法について説明します (現在プレビュー段階)。 Azure Time Series Insights Gen2 環境のプライベート エンドポイントを構成することで、Azure Time Series Insights 環境をセキュリティで保護し、パブリック エクスポージャを排除するだけでなく、[Azure Virtual Network (VNet)](../virtual-network/virtual-networks-overview.md) からのデータ流出を回避することができます。

この記事では、<bpt id="p1">[</bpt><bpt id="p2">**</bpt>Azure portal<ept id="p2">**</ept><ept id="p1">](https://portal.azure.com)</ept> を使用したプロセスについて説明します。

この記事では、次の手順を説明します。 
1. Private Link を有効にして、Time Series Insights Gen2 環境のプライベート エンドポイントを構成します。
1. パブリック ネットワーク アクセス フラグを無効または有効にして、Private Link 接続のみにアクセスを制限します。

## <a name="prerequisites"></a>前提条件

プライベート エンドポイントをセットアップする前に、エンドポイントをデプロイできる <bpt id="p1">[</bpt><bpt id="p2">**</bpt>Azure Virtual Network (VNet)<ept id="p2">**</ept> <ept id="p1">](../virtual-network/virtual-networks-overview.md)</ept> が必要です。 VNet がまだない場合は、Azure Virtual Network のいずれかの<bpt id="p1">[</bpt>クイックスタート<ept id="p1">](../virtual-network/quick-create-portal.md)</ept>に従って、これを設定できます。

## <a name="add-a-private-endpoint-for-an-azure-time-series-insights-gen2-environment"></a>Azure Time Series Insights Gen2 環境のプライベート エンドポイントを追加する 

[Azure portal](https://portal.azure.com) を使用する場合、環境の初期設定の一部として Azure Time Series Insights Gen2 環境のプライベート エンドポイントで Private Link を有効にするか、既に存在する環境で後から有効にするかを選択できます。 

どちらの作成方法でも、環境に用意される構成オプションと結果は同じです。 このセクションでは、それぞれの方法について説明します。

>[!TIP]
> また、Azure Time Series Insights Gen2 環境を使用するのではなく、Private Link サービスを使用して Private Link エンドポイントを設定することもできます。 その場合も、構成オプションと最終的な結果は同じです。
>
> Private Link リソースの設定の詳細については、<bpt id="p1">[</bpt>Azure portal<ept id="p1">](../private-link/create-private-endpoint-portal.md)</ept>、<bpt id="p2">[</bpt>Azure CLI<ept id="p2">](../private-link/create-private-endpoint-cli.md)</ept>、<bpt id="p3">[</bpt>ARM<ept id="p3">](../private-link/create-private-endpoint-template.md)</ept>、または <bpt id="p4">[</bpt>PowerShell<ept id="p4">](../private-link/create-private-endpoint-powershell.md)</ept> の Private Link に関するドキュメントを参照してください。

### <a name="add-a-private-endpoint-during-environment-creation"></a>環境の作成時にプライベート エンドポイントを追加する

このセクションでは、現在作成中の Azure Time Series Insights Gen2 環境でプライベート エンドポイントを使用して Private Link を有効にします。 このセクションでは、現在作成中の Azure Time Series Insights Gen2 環境上のプライベート エンドポイントを使用して Private Link を有効にします。[*環境の設定方法*](tutorial-set-up-environment.md)に関するページを参照してください。

Private Link のオプションは、環境の設定の **[ネットワーク]** タブにあります。

このタブでは、 <bpt id="p2">**</bpt>[接続方法]<ept id="p2">**</ept> の <bpt id="p1">**</bpt>[プライベート エンドポイント]<ept id="p1">**</ept> オプションを選択することにより、プライベート エンドポイントを有効にできます。

これによって追加される <bpt id="p1">**</bpt>[プライベート エンドポイント接続]<ept id="p1">**</ept> というセクションで、プライベート エンドポイントの詳細を構成できます。 <bpt id="p1">**</bpt>[+ 追加]<ept id="p1">**</ept> ボタンをクリックして続けます。

:::image type="content" source="media/private-links/create-instance-networking.png" alt-text="Time Series Insights Gen2 の [リソースの作成] ダイアログの [ネットワーク] タブを示す Azure portal のスクリーンショット。タブ名が強調表示され、[接続方法] には [プライベート エンドポイント] オプションがあり、新しいプライベート エンドポイント接続を作成するための [+ 追加] ボタンがあります。" lightbox="media/private-links/create-instance-networking.png":::

これにより、新しいプライベート エンドポイントの詳細を入力するページが開きます。

<bpt id="p1">:::image type="content" source="media/private-links/create-private-endpoint.png" alt-text="</bpt>[プライベート エンドポイントの作成] ページが表示されている Azure portal のスクリーンショット。以下で説明するフィールドが含まれます。<ept id=&quot;p1&quot;>":::</ept>

1. <bpt id="p1">**</bpt>[サブスクリプション]<ept id="p1">**</ept> と <bpt id="p2">**</bpt>[リソース グループ]<ept id="p2">**</ept> の選択を入力します。 <bpt id="p1">**</bpt>[場所]<ept id="p1">**</ept> を、使用する VNet と同じ場所に設定します。 エンドポイントの **[名前]** を選択し、 **[Target sub-resources]\(ターゲット サブリソース\)** で *[environment]* または *[tsiExplorer]* を選択します。

1. 次に、エンドポイントのデプロイに使用する <bpt id="p1">**</bpt>[仮想ネットワーク]<ept id="p1">**</ept> と <bpt id="p2">**</bpt>[サブネット]<ept id="p2">**</ept> を選択します。

1. 最後に、 <bpt id="p1">**</bpt>[プライベート DNS ゾーンと統合する]<ept id="p1">**</ept> かどうかを選択します。 既定の <bpt id="p1">**</bpt>[はい]<ept id="p1">**</ept> をそのまま使用できます。または、このオプションの詳細については、ポータルのリンクに従って、<bpt id="p2">[</bpt>プライベート DNS 統合の詳細を確認<ept id="p2">](../private-link/private-endpoint-overview.md#dns-configuration)</ept>してください。

構成オプションを設定した後、 <bpt id="p1">**</bpt>[OK]<ept id="p1">**</ept> をクリックして完了します。

これにより、Azure Time Series Insights Gen2 環境の設定の **[ネットワーク]** タブに戻ります。そこの **[プライベート エンドポイント接続]** の下に、新しいエンドポイントが表示されているはずです。

その後、下部にあるナビゲーション ボタンを使用して、環境の残りの設定を続けることができます。

### <a name="add-a-private-endpoint-to-an-existing-environment"></a>既存の環境にプライベート エンドポイントを追加する

このセクションでは、既に存在する Azure Time Series Insights Gen2 環境でプライベート エンドポイントを使用して Private Link を有効にします。

1. まず、ブラウザーで <bpt id="p1">[</bpt>Azure portal<ept id="p1">](https://portal.azure.com)</ept> に移動します。 ポータルの検索バーで名前を検索して、Azure Time Series Insights Gen2 環境を表示します。

1. 左側のメニューで、 <bpt id="p1">**</bpt>[Networking (preview)]\(ネットワーク (プレビュー)\)<ept id="p1">**</ept> を選択します。

1. <bpt id="p1">**</bpt>[プライベート エンドポイント接続]<ept id="p1">**</ept> タブに切り替えます。

1. <bpt id="p1">**</bpt>[+ プライベート エンドポイント]<ept id="p1">**</ept> を選択して、 <bpt id="p2">**</bpt>[プライベート エンドポイントの作成]<ept id="p2">**</ept> の設定を開きます。

    :::image type="content" source="media/private-links/add-private-endpoint.png" alt-text="Azure portal のスクリーンショット。Azure Time Series Insights Gen2 環境の [ネットワーク (プレビュー)] ページが表示されています。[プライベート エンドポイント接続] タブが強調表示され、[+ プライベート エンドポイント] ボタンも強調表示されています。" lightbox="media/private-links/add-private-endpoint.png":::

1. <bpt id="p1"> **</bpt>[基本]<ept id="p1">**</ept> <ph id="ph1"> </ph>タブで、プロジェクトの <bpt id="p2">**</bpt>[サブスクリプション]<ept id="p2">**</ept> と <bpt id="p3">**</bpt>[リソース グループ]<ept id="p3">**</ept> 、およびエンドポイントの <bpt id="p4">**</bpt>[名前]<ept id="p4">**</ept> と <bpt id="p5">**</bpt>[リージョン]<ept id="p5">**</ept> を入力または選択します。 リージョンは、使用している VNet のリージョンと同じにする必要があります。

    <bpt id="p1">:::image type="content" source="media/private-links/create-private-endpoint-2.png" alt-text="</bpt>[プライベート エンドポイントの作成] ダイアログの最初の ([基本]) タブが表示されている Azure portal のスクリーンショット。上で説明したフィールドが含まれます。<ept id=&quot;p1&quot;>":::</ept>

    終わったら、 <bpt id="p1">**</bpt>[次: リソース >]<ept id="p1">**</ept> ボタンを選択して、次のタブに進みます。

1. <bpt id="p1">**</bpt>[リソース]<ept id="p1">**</ept> タブで、次の情報を入力または選択します。 
    * **接続方法**: **[マイ ディレクトリ内の Azure リソースに接続します]** を選択し、Azure Time Series Insights Gen2 環境を検索します。
    * <bpt id="p1">**</bpt>サブスクリプション<ept id="p1">**</ept>:お使いのサブスクリプションを入力します。
    * **リソースの種類**: **[Microsoft.TimeSeriesInsights/environments]** を選択します。
    * **リソース**: Azure Time Series Insights Gen2 環境の名前を選択します。
    * **Target sub-resource (ターゲット サブリソース)** : **[environment]** または **[tsiExplorer]** を選択します。

    <bpt id="p1">:::image type="content" source="media/private-links/create-private-endpoint-3.png" alt-text="</bpt>[プライベート エンドポイントの作成] ダイアログの 2 番目の ([リソース]) タブが表示されている Azure portal のスクリーンショット。上で説明したフィールドが含まれます。<ept id=&quot;p1&quot;>":::</ept>

    終わったら、 <bpt id="p1">**</bpt>[次: 構成 >]<ept id="p1">**</ept> ボタンを選択して、次のタブに進みます。    

1. <bpt id="p1"> **</bpt>[構成]<ept id="p1">**</ept> タブで、次の情報を入力または選択します。
    * <bpt id="p1">**</bpt>仮想ネットワーク<ept id="p1">**</ept>:仮想ネットワークを選択します。
    * <bpt id="p1">**</bpt>サブネット<ept id="p1">**</ept>:仮想ネットワークからサブネットを選択します。
    * <bpt id="p1">**</bpt>プライベート DNS ゾーンと統合する<ept id="p1">**</ept>: <bpt id="p2">**</bpt>[プライベート DNS ゾーンと統合する]<ept id="p2">**</ept> かどうかを選択します。 既定の <bpt id="p1">**</bpt>[はい]<ept id="p1">**</ept> をそのまま使用できます。または、このオプションの詳細については、ポータルのリンクに従って、<bpt id="p2">[</bpt>プライベート DNS 統合の詳細を確認<ept id="p2">](../private-link/private-endpoint-overview.md#dns-configuration)</ept>してください。
    <bpt id="p1">**</bpt>[はい]<ept id="p1">**</ept> を選択した場合は、既定の構成情報をそのまま使用できます。

    <bpt id="p1">:::image type="content" source="media/private-links/create-private-endpoint-4.png" alt-text="</bpt>[プライベート エンドポイントの作成] ダイアログの 3 番目の ([構成]) タブが表示されている Azure portal のスクリーンショット。上で説明したフィールドが含まれます。<ept id=&quot;p1&quot;>":::</ept>

    終わったら、 <bpt id="p1">**</bpt>[確認および作成]<ept id="p1">**</ept> ボタンを選択して、セットアップを完了します。 

1. <bpt id="p1">**</bpt>[確認および作成]<ept id="p1">**</ept> タブで選択内容を確認し、<bpt id="p2"> **</bpt>[作成]<ept id="p2">**</ept> ボタンを選択します。 

エンドポイントのデプロイが完了すると、Azure Time Series Insights Gen2 環境用のプライベート エンドポイント接続に表示されます。

>[!TIP]
> エンドポイントは、Azure portal の Private Link センターから表示することもできます。

## <a name="disable--enable-public-network-access-flags"></a>パブリック ネットワーク アクセス フラグを無効または有効にする

Azure Time Series Insights Gen2 環境の構成によって、すべてのパブリック接続を拒否し、プライベート エンドポイント経由の接続のみを許可するようにして、ネットワークのセキュリティを強化できます。 この操作は、<bpt id="p1">**</bpt>パブリック ネットワーク アクセス フラグ<ept id="p1">**</ept>で行います。 

このポリシーを使用すると、アクセスを Private Link 接続のみに制限することができます。 パブリック ネットワーク アクセス フラグが "*無効*" に設定されていると、パブリック クラウドから Azure Time Series Insights Gen2 環境のデータ プレーンへのすべての REST API 呼び出しで、`403, Unauthorized` が返されます。 または、ポリシーが "<bpt id="p1">*</bpt>無効<ept id="p1">*</ept>" に設定されていて、要求がプライベート エンドポイントを通して行われた場合は、API 呼び出しは成功します。

[Azure portal](https://portal.azure.com) でパブリック ネットワーク アクセスを無効または有効にするには、ポータルを開き、お使いの Azure Time Series Insights Gen2 環境に移動します。

1. 左側のメニューで、 <bpt id="p1">**</bpt>[Networking (preview)]\(ネットワーク (プレビュー)\)<ept id="p1">**</ept> を選択します。

1. <bpt id="p1">**</bpt>[パブリック アクセス]<ept id="p1">**</ept> タブの <bpt id="p2">**</bpt>[Allow public network access to]\(パブリック ネットワーク アクセスを許可する\)<ept id="p2">**</ept> を、 <bpt id="p3">**</bpt>[無効]<ept id="p3">**</ept> または <bpt id="p4">**</bpt>[すべてのネットワーク]<ept id="p4">**</ept> に設定します。

    :::row:::
        :::column:::
            :::image type="content" source="media/private-links/network-flag-portal.png" alt-text="Azure Time Series Insights Gen2 環境の [ネットワーク (プレビュー)] ページを示す Azure portal のスクリーンショット。[パブリック アクセス] タブが強調表示され、パブリック ネットワーク アクセスを許可するかどうかを選択するオプションも強調表示されています。" lightbox="media/private-links/network-flag-portal.png":::
        :::column-end:::
        :::column:::
        :::column-end:::
    :::row-end:::

    <bpt id="p1">**</bpt>[保存]<ept id="p1">**</ept> を選択します。

## <a name="next-steps"></a>次のステップ

Azure 用 Private Link の詳細を確認します。 
* <bpt id="p1">[</bpt><bpt id="p2">*</bpt>Azure Private Link サービスとは<ept id="p2">*</ept><ept id="p1">](../private-link/private-link-service-overview.md)</ept>