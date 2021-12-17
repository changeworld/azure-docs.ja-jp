---
title: Azure Marketplace での Azure アプリケーション オファー作成
description: コマーシャル マーケットプレース ポータルを使用して、Azure Marketplace で、またはクラウド ソリューション プロバイダー (CSP) プログラムを通してリスト登録または販売を行うために、Azure アプリケーション オファーを作成します。
author: aarathin
ms.author: aarathin
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 09/27/2021
ms.openlocfilehash: c0226e8ffdd8445818313d6f21255752dc2e5616
ms.sourcegitcommit: 10029520c69258ad4be29146ffc139ae62ccddc7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/27/2021
ms.locfileid: "129083271"
---
# <a name="create-an-azure-application-offer"></a>Azure アプリケーション オファーを作成する

コマーシャル マーケットプレースの発行元は、潜在顧客がソリューションを購入できるように、Azure アプリケーション オファーを作成することができます。 この記事では、Microsoft コマーシャル マーケットプレース用に Azure アプリケーション オファーを作成するプロセスについて説明します。

まだそうしていない場合は、「<bpt id="p1">[</bpt>コマーシャル マーケットプレース用の Azure アプリケーション オファーを計画する<ept id="p1">](plan-azure-application-offer.md)</ept>」を参照してください。 リソースが提供され、オファーを作成するときに必要になる情報と資産を収集する助けになります。

## <a name="create-a-new-offer"></a>新しいオファーを作成する

[!INCLUDE [Workspaces view note](./includes/preview-interface.md)]

#### <a name="workspaces-view"></a>[ワークスペース ビュー](#tab/workspaces-view)

1. <bpt id="p1">[</bpt>パートナー センター<ept id="p1">](https://partner.microsoft.com/dashboard/home)</ept>にサインインします。

1. ホーム ページで、 **[Marketplace offers]\(Marketplace のプラン\)** タイルを選択します。

    [ ![パートナー センターのホーム ページにある [Marketplace offers]\(Marketplace のプラン\) タイルの画像。](./media/workspaces/partner-center-home.png) ](./media/workspaces/partner-center-home.png#lightbox)

1. [Marketplace offers]\(Marketplace のプラン\) ページで、 **[+ 新しいプラン]**  >  **[Azure アプリケーション]** を選択します。

    [ ![Marketplace の [プラン] ページにある [新しいプラン] ボタンの画像。](./media/create-new-azure-app-offer/new-offer-azure-app-workspaces.png) ](./media/create-new-azure-app-offer/new-offer-azure-app-workspaces.png#lightbox)

1. **[New Azure Application]\(新しい Azure アプリケーション\)** ダイアログ ボックスの **[プラン ID]** を入力します。 これは、アカウントのオファーごとに一意の識別子です。 この ID は、コマーシャル マーケットプレースの一覧と Azure Resource Manager テンプレートの URL に表示されます (該当する場合)。 たとえば、このボックスに「test-offer-1」と入力すると、オファーの Web アドレスは <ph id="ph1">`https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`</ph> になります。

     * アカウント内の各オファーには、一意のオファー ID が必要です。
     * 使用できるのは小文字と数字だけです。 ハイフンとアンダースコアを含めることができますが、スペースは使用できず、文字数は 50 文字に制限されています。
     * <bpt id="p1">**</bpt>[作成]<ept id="p1">**</ept> を選択した後で、オファー ID を変更することはできません。

1. <bpt id="p1">**</bpt>[オファーのエイリアス]<ept id="p1">**</ept> を入力します。 これは、パートナー センター内でオファーに使用される名前です。

     * この名前はパートナー センター内でのみ表示され、顧客に表示されるオファー名やその他の値とは異なります。
     * <bpt id="p1">**</bpt>[作成]<ept id="p1">**</ept> を選択した後で、オファーの別名を変更することはできません。

1. オファーを生成して続行するには、 <bpt id="p1">**</bpt>[作成]<ept id="p1">**</ept> を選択します。

#### <a name="current-view"></a><bpt id="p1">[</bpt>現在のビュー<ept id="p1">](#tab/current-view)</ept>

1. <bpt id="p1">[</bpt>パートナー センター<ept id="p1">](https://partner.microsoft.com/dashboard/home)</ept>にサインインします。

1. 左側のナビゲーション メニューで、 <bpt id="p1">**</bpt>[商業マーケットプレース]<ept id="p1">**</ept> <ph id="ph1"> > </ph> <bpt id="p2">**</bpt>[概要]<ept id="p2">**</ept> の順に選択します。

1. [概要] ページで、 <bpt id="p1">**</bpt>[+ 新しいオファー]<ept id="p1">**</ept> <ph id="ph1"> > </ph> <bpt id="p2">**</bpt>[Azure アプリケーション]<ept id="p2">**</ept> の順に選択します。

    ![左側のナビゲーション メニューの画像。](./media/create-new-azure-app-offer/new-offer-azure-app.png)

1. <bpt id="p1">**</bpt>[新しいオファー]<ept id="p1">**</ept> ダイアログ ボックスで、 <bpt id="p2">**</bpt>[オファー ID]<ept id="p2">**</ept> を入力します。 これは、アカウントのオファーごとに一意の識別子です。 この ID は、コマーシャル マーケットプレースの一覧と Azure Resource Manager テンプレートの URL に表示されます (該当する場合)。 たとえば、このボックスに「test-offer-1」と入力すると、オファーの Web アドレスは <ph id="ph1">`https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`</ph> になります。

     * アカウント内の各オファーには、一意のオファー ID が必要です。
     * 使用できるのは小文字と数字だけです。 ハイフンとアンダースコアを含めることができますが、スペースは使用できず、文字数は 50 文字に制限されています。
     * <bpt id="p1">**</bpt>[作成]<ept id="p1">**</ept> を選択した後で、オファー ID を変更することはできません。

1. <bpt id="p1">**</bpt>[オファーのエイリアス]<ept id="p1">**</ept> を入力します。 これは、パートナー センター内でオファーに使用される名前です。

     * この名前はパートナー センター内でのみ表示され、顧客に表示されるオファー名やその他の値とは異なります。
     * <bpt id="p1">**</bpt>[作成]<ept id="p1">**</ept> を選択した後で、オファーの別名を変更することはできません。

1. オファーを生成して続行するには、 <bpt id="p1">**</bpt>[作成]<ept id="p1">**</ept> を選択します。

---

## <a name="configure-your-azure-application-offer-setup-details"></a>Azure アプリケーション オファーのセットアップの詳細を構成する

<bpt id="p1">**</bpt>[オファーのセットアップ]<ept id="p1">**</ept> タブの <bpt id="p2">**</bpt>[セットアップの詳細]<ept id="p2">**</ept> で、体験版を構成するかどうかを選択します。 また、お使いのカスタマー リレーションシップ マネジメント (CRM) システムを、コマーシャル マーケットプレース オファーと接続します。

### <a name="enable-a-test-drive-optional"></a>体験版を有効にする (省略可能)

体験版は、構成済み環境に一定時間アクセスできるようにすることで、潜在顧客に対してオファーを紹介するのに最適な方法です。 体験版を提供すると、コンバージョン率が向上し、質の高い潜在顧客を獲得できます。 体験版の詳細については、「<bpt id="p1">[</bpt>体験版<ept id="p1">](plan-azure-application-offer.md#test-drive)</ept>」を参照してください。

#### <a name="to-enable-a-test-drive"></a>体験を有効にするには

- <bpt id="p1">**</bpt>[体験版]<ept id="p1">**</ept> で、 <bpt id="p2">**</bpt>[体験版を有効にする]<ept id="p2">**</ept> チェック ボックスをオンにします。

### <a name="customer-lead-management"></a>顧客リードの管理

カスタマー リレーションシップ マネジメント (CRM) システムをコマーシャル マーケットプレースのオファーに接続して、顧客が興味を示したり製品をデプロイしたりしたときに顧客の連絡先情報を受け取ることができるようにします。

#### <a name="to-configure-the-connection-details-in-partner-center"></a>パートナー センターで接続の詳細を構成するには

1. <bpt id="p1">**</bpt>[潜在顧客]<ept id="p1">**</ept> で、 <bpt id="p2">**</bpt>[接続]<ept id="p2">**</ept> リンクを選択します。
1. <bpt id="p1">**</bpt>[接続の詳細]<ept id="p1">**</ept> ダイアログ ボックスで、一覧から潜在顧客の宛先を選択します。
1. 表示されるフィールドに入力します。 詳細な手順については、次の記事を参照してください。

   - <bpt id="p1">[</bpt>リードを Azure テーブルに送信するようにオファーを構成する<ept id="p1">](partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table.md#configure-your-offer-to-send-leads-to-the-azure-table)</ept>
   - <bpt id="p1">[</bpt>Dynamics 365 Customer Engagement (旧称 Dynamics CRM Online) にリードを送信するようにオファーを構成する<ept id="p1">](partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md#configure-your-offer-to-send-leads-to-dynamics-365-customer-engagement)</ept>
   - <bpt id="p1">[</bpt>潜在顧客を HTTPS エンドポイントに送信するようにオファーを構成する<ept id="p1">](partner-center-portal/commercial-marketplace-lead-management-instructions-https.md#configure-your-offer-to-send-leads-to-the-https-endpoint)</ept>
   - <bpt id="p1">[</bpt>リードを Marketo に送信するようにオファーを構成する<ept id="p1">](partner-center-portal/commercial-marketplace-lead-management-instructions-marketo.md#configure-your-offer-to-send-leads-to-marketo)</ept>
   - <bpt id="p1">[</bpt>リードを Salesforce に送信するようにオファーを構成する<ept id="p1">](partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce.md#configure-your-offer-to-send-leads-to-salesforce)</ept>

1. 指定した構成を検証するには、 <bpt id="p1">**</bpt>[検証]<ept id="p1">**</ept> リンクを選択します (該当する場合)。
1. ダイアログ ボックスを閉じるには、 <bpt id="p1">**</bpt>[接続]<ept id="p1">**</ept> を選択します。
1. <bpt id="p1">**</bpt>[下書きの保存]<ept id="p1">**</ept> を選択してから、次のプロパティ。

> [!NOTE]
> リードが失われないように、リード ターゲットへの接続が最新の状態に保たれていることを確認します。 何かが変更されたときは、必ずこれらの接続を更新してください。

## <a name="next-steps"></a>次のステップ

- <bpt id="p1">[</bpt>Azure アプリケーションのプロパティを構成する<ept id="p1">](azure-app-properties.md)</ept>
