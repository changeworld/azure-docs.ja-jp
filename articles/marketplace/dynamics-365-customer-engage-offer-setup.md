---
title: Microsoft AppSource (Azure Marketplace) で Dynamics 365 for Customer Engagement & Power Apps オファーを作成します。
description: Microsoft AppSource (Azure Marketplace) で Dynamics 365 for Customer Engagement & Power Apps オファーを作成します。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: vamahtan
ms.author: vamahtan
ms.date: 09/27/2021
ms.openlocfilehash: 764875272975e6a7698b6dafc452e597c8195000
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130225701"
---
# <a name="create-a-dynamics-365-for-customer-engagement--power-apps-offer"></a>Dynamics 365 for Customer Engagement & Power Apps オファーの作成

この記事では、Dynamics 365 for Customer Engagement & Power Apps オファーの作成方法について説明します。 Dynamics 365 のすべてのオファーは、弊社の認定プロセスを通過する必要があります。 試用版エクスペリエンスでは、ユーザーがソリューションをライブの Dynamics 365 環境にデプロイできます。

開始する前に、[パートナー センター](./create-account.md)でコマーシャル マーケットプレース アカウントを作成し、それがコマーシャル マーケットプレース プログラムに登録されているようにします。

## <a name="before-you-begin"></a>開始する前に

[Dynamics 365 オファーの計画](marketplace-dynamics-365.md)を見直します。 これには、このオファーの技術的な要件の説明と、その作成時に必要となる情報と資産が記載されています。

## <a name="create-a-new-offer"></a>新しいオファーを作成する

[!INCLUDE [Workspaces view note](./includes/preview-interface.md)]

#### <a name="workspaces-view"></a>[ワークスペース ビュー](#tab/workspaces-view)

1. <bpt id="p1">[</bpt>パートナー センター<ept id="p1">](https://partner.microsoft.com/dashboard/home)</ept>にサインインします。
1. ホーム ページで、 **[Marketplace offers]\(Marketplace のオファー\)** タイルを選択します。

    [ ![パートナー センターのホーム ページにある Marketplace オファーのタイルを示す図。](./media/workspaces/partner-center-home.png) ](./media/workspaces/partner-center-home.png#lightbox)

1. [Marketplace オファー] のページで、 **[+ 新しいプラン]**  >  **[Dynamics 365 for Customer Engagement & Power Apps]** を選択します。

    [ ![[新しいオファー] ボタンが表示され、オファーのタイプとして [Customer Engagement & Power Apps] が選択されます。](./media/dynamics-365/new-offer-dynamics-365-customer-engagement-workspaces.png) ](./media/dynamics-365/new-offer-dynamics-365-customer-engagement-workspaces.png#lightbox)

> [!IMPORTANT]
> オファーが公開された後にパートナー センターで行った編集は、そのオファーを再公開した後にのみ Microsoft AppSource に反映されます。 変更後は必ずオファーを再公開するようにしてください。

#### <a name="current-view"></a><bpt id="p1">[</bpt>現在のビュー<ept id="p1">](#tab/current-view)</ept>

1. <bpt id="p1">[</bpt>パートナー センター<ept id="p1">](https://partner.microsoft.com/dashboard/home)</ept>にサインインします。
2. 左側のナビゲーション メニューで、 **[コマーシャル マーケットプレース]**  >  **[概要]** の順に選択します。
3. [概要] ページで、 **[+ 新しいオファー]**  >  **[Dynamics 365 for Customer Engagement & Power Apps]** の順に選択します。

    :::image type="content" source="media/dynamics-365/new-offer-dynamics-365-customer-engagement.png" alt-text="左ペインのメニュー オプションと [新しいオファー] ボタンを表示し、Customer Engagement が選択されています。":::

> [!IMPORTANT]
> オファーが公開された後にパートナー センターで行った編集は、そのオファーを再公開した後にのみ Microsoft AppSource に反映されます。 変更後は必ずオファーを再公開するようにしてください。

---

## <a name="new-offer"></a>新しいプラン

**[オファー ID]** を入力します。 これは、アカウントのオファーごとに一意の識別子です。

- この ID は、オファーの Web アドレス内と Azure Resource Manager テンプレート内 (該当する場合) の顧客に表示されます。
- 使用できるのは小文字と数字だけです。 ID にはハイフンとアンダースコアを含めることができますが、スペースは使用できず、文字数は 50 文字に制限されています。 たとえば、パブリッシャー ID が `testpublisherid` であれば、「**test-offer-1**」と入力すると、オファーの Web アドレスは `https://appsource.microsoft.com/product/dynamics-365/testpublisherid.test-offer-1` になります。
- **[作成]** を選択した後で、オファー ID を変更することはできません。

**[オファーのエイリアス]** を入力します。 これは、パートナー センター内でオファーに使用される名前です。

- この名前は、AppSource では使用されません。 これは、顧客に表示されるオファー名やその他の値とは異なります。
- **[作成]** を選択した後にこの名前を変更することはできません。

**[作成]** を選択してオファーを生成します。 パートナー センターで **[オファーのセットアップ]** ページが開きます。

## <a name="alias"></a>エイリアス

パートナー センター内でのみこのオファーを参照するために使用する、わかりやすい名前を入力します。 このオファーのエイリアス (オファー作成時に入力した内容によって事前入力済み) は、マーケットプレースでは使用されず、顧客に表示されるオファー名とは異なります。 オファー名を後で更新する場合は、[オファー登録情報](dynamics-365-customer-engage-offer-listing.md)ページを参照してください。

## <a name="setup-details"></a>セットアップの詳細

**[潜在顧客がこの登録オファーとやり取りする方法について選択してください。]** で、このオファーに使用するオプションを選択します。

- **[Microsoft によるアプリ ライセンス管理を有効にする]** – Microsoft を通してアプリ ライセンスを管理します。 顧客がライセンスなしでアプリの基本機能を実行したり、ライセンスを購入した後は Premium 機能を実行したりできるようにするには、 **[ライセンスが割り当てられていない場合でもアプリをインストールできるようにする]** ボックスをオンにします。 この 2 番目のボックスをオンにする場合は、ライセンスを必要としないようにソリューション パッケージを構成する必要があります。

    > [!NOTE]
    > この設定は、このオファーを公開した後には変更できません。 この設定の詳細については、「[ISV アプリのライセンス管理](isv-app-license.md)」を参照してください。

- **今すぐ入手する (無料)** – 無料のオファーの一覧を顧客に提示します。
- **無料試用版 (一覧)** – 無料試用版へのリンクが付いたオファーの一覧を顧客に提示します。 オファー登録情報の無料試用版がご利用のサービスによって作成、管理、および構成され、Microsoft によって管理されるサブスクリプションはありません。

    > [!NOTE]
    > 試用版リンクからアプリケーションが受信するトークンは、そのアプリのアカウント作成を自動化するためのユーザー情報を Azure Active Directory (Azure AD) を介して取得するためだけに使用できます。 このトークンを使用した認証には、Microsoft アカウントはサポートされません。

- **連絡してください** – 顧客関係管理 (CRM) システムに接続して、顧客の連絡先情報を収集します。 顧客は、自分の情報を共有する許可を求められます。 これらの顧客の詳細は、オファーの名前と ID のほか、顧客がオファーを見つけたマーケットプレース ソースと一緒に、お客様が構成した CRM システムに送信されます。 CRM の構成の詳細については、「[潜在顧客](#customer-leads)」を参照してください。

## <a name="test-drive"></a>体験版

体験版は、構成済み環境に一定時間アクセスできるようにすることで、潜在顧客に対してオファーを紹介するのに最適な方法です。 体験版を提供すると、コンバージョン率が向上し、質の高い潜在顧客を獲得できます。 詳細については、まず「[体験版とは](what-is-test-drive.md)」を参照してください。

> [!TIP]
> 体験版は、無料試用版とは異なります。 体験版または無料試用版のいずれかまたは両方を提供できます。 これらはどちらも、ソリューションを一定期間、顧客に提供します。 ただし、体験版には、実際の実装シナリオの中で製品の主な機能や利点を体験できる実践的なセルフガイド ツアーも含まれます。

体験版を有効にするには、 **[体験版を有効にする]** チェックボックスをオンにして、 **[体験版の種類]** を選択します。 体験版は後で構成します。 体験版を使用する場合は、潜在顧客用に CRM システムに対してオファーを構成することも必要です (次のセクションを参照)。 オファーから体験版を削除するには、このチェック ボックスをオフにします。

## <a name="customer-leads"></a>潜在顧客

[!INCLUDE [Connect lead management](includes/customer-leads.md)]

詳細については、「[コマーシャル マーケットプレース オファーからの顧客リード](partner-center-portal/commercial-marketplace-get-customer-leads.md)」を参照してください。

左側のナビゲーション メニューの次のタブである **[プロパティ]** に進む前に、 **[下書きの保存]** を選択します。

## <a name="next-steps"></a>次のステップ

- [オファーのプロパティを構成する](dynamics-365-customer-engage-properties.md)
- [オファーの掲載のベスト プラクティス](gtm-offer-listing-best-practices.md)
