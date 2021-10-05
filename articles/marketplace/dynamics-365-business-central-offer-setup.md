---
title: Microsoft AppSource (Azure Marketplace) で Dynamics 365 for Business Central オファーを作成します
description: Microsoft AppSource (Azure Marketplace) で Dynamics 365 for Business Central オファーを作成します。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: vamahtan
ms.author: vamahtan
ms.date: 09/27/2021
ms.openlocfilehash: 6056259817c24079ae1a2efc912a8251feeaa1ad
ms.sourcegitcommit: 10029520c69258ad4be29146ffc139ae62ccddc7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/27/2021
ms.locfileid: "129081998"
---
# <a name="create-a-dynamics-365-for-business-central-offer"></a>Dynamics 365 for Business Central オファーを作成する

このアーティクルでは、新しい [Dynamics 365 Business Central](https://dynamics.microsoft.com/business-central/overview) オファーの作成方法について説明します。 このオファー タイプは、財務、業務、サプライ チェーン、CRM、プロジェクト管理、電子商取引など、幅広いビジネス プロセスをサポートするエンタープライズ リソース プランニング (ERP) サービスです。 Dynamics 365 のすべてのオファーは、弊社の認定プロセスを通過する必要があります。

開始する前に、[パートナー センター](create-account.md)でコマーシャル マーケットプレース アカウントを作成し、それがコマーシャル マーケットプレース プログラムに登録されているようにします。

## <a name="before-you-begin"></a>開始する前に

[Dynamics 365 オファーの計画](marketplace-dynamics-365.md)を見直します。 これには、このオファーの技術的な要件の説明と、その作成時に必要となる情報と資産が記載されています。

## <a name="create-a-new-offer"></a>新しいオファーを作成する

[!INCLUDE [Workspaces view note](./includes/preview-interface.md)]

#### <a name="workspaces-view"></a>[ワークスペース ビュー](#tab/workspaces-view)

1. [パートナー センター](https://go.microsoft.com/fwlink/?linkid=2166002)にサインインします。

1. ホーム ページで、 **[Marketplace offers]\(Marketplace のオファー\)** タイルを選択します。

    [ ![パートナー センターのホーム ページにある [Marketplace offers]\(Marketplace のオファー\) タイルを示しています。](./media/workspaces/partner-center-home.png) ](./media/workspaces/partner-center-home.png#lightbox)

1. [Marketplace offers]\(Marketplace のオファー\) ページで、 **[+ 新しいプラン]**  >  **[Dynamics 365 for Business Central]** の順に選択します。

    [ ![[新しいプラン] ボタンを示しています。新しいプランである Dynamics 365 Business Central を確認できます。](media/dynamics-365/new-offer-dynamics-365-business-central-workspaces.png) ](media/dynamics-365/new-offer-dynamics-365-business-central-workspaces.png#lightbox)

> [!IMPORTANT]
> オファーが公開された後にパートナー センターで行った編集は、そのオファーを再公開した後にのみ Microsoft AppSource に反映されます。 変更後は必ずオファーを再公開するようにしてください。

#### <a name="current-view"></a>[現在のビュー](#tab/current-view)

1. [パートナー センター](https://partner.microsoft.com/dashboard/home)にサインインします。
1. 左側のナビゲーション メニューで、 **[コマーシャル マーケットプレース]**  >  **[概要]** の順に選択します。
1. [概要] ページで、 **[+ New offer (+ 新しいオファー)]**  >  **[Dynamics 365 Business Central]** の順に選択します。

    :::image type="content" source="media/dynamics-365/new-offer-dynamics-365-business-central.png" alt-text="左ペイン メニューと [新しいオファー] ボタン。":::

> [!IMPORTANT]
> オファーが公開された後にパートナー センターで行った編集は、そのオファーを再公開した後にのみ Microsoft AppSource に反映されます。 変更後は必ずオファーを再公開するようにしてください。

---

## <a name="new-offer"></a>新しいプラン

表示されるダイアログ ボックスに、**プラン ID** を入力します。 これは、アカウントのオファーごとに一意の識別子です。

- この ID は、オファーの Web アドレス内と Azure Resource Manager テンプレート内 (該当する場合) の顧客に表示されます。
- 使用できるのは小文字と数字だけです。 ID にはハイフンとアンダースコアを含めることができますが、スペースは使用できず、文字数は 50 文字に制限されています。 たとえば、パブリッシャー ID が `testpublisherid` であれば、「**test-offer-1**」と入力すると、オファーの Web アドレスは `https://appsource.microsoft.com/product/dynamics-365/testpublisherid.test-offer-1` になります。
- **[作成]** を選択した後で、オファー ID を変更することはできません。

**[オファーのエイリアス]** を入力します。 これは、パートナー センター内でオファーに使用される名前です。

- この名前は、AppSource では使用されません。 これは、顧客に表示されるオファー名やその他の値とは異なります。
- **[作成]** を選択した後にこの名前を変更することはできません。

**[作成]** を選択してオファーを生成します。 パートナー センターで **[オファーのセットアップ]** ページが開きます。

## <a name="alias"></a>エイリアス

パートナー センター内でのみこのオファーを参照するために使用する、わかりやすい名前を入力します。 このオファーのエイリアス (オファー作成時に入力した内容によって事前入力済み) は、マーケットプレースでは使用されず、顧客に表示されるオファー名とは異なります。 オファー名を後で更新する場合は、[オファー登録情報](dynamics-365-business-central-offer-listing.md)ページを参照してください。

## <a name="setup-details"></a>セットアップの詳細

オファーに適用する **パッケージの種類** を選択します (Biz Central)

- **アドオン** アプリは、Dynamics 365 Business Central のエクスペリエンスと既存の機能を拡張します。 詳細については、「[アドオン アプリ](/dynamics365/business-central/dev-itpro/developer/readiness/readiness-add-on-apps)」を参照してください。
- **接続** アプリは、Dynamics 365 Business Central とサードパーティのソリューションまたはサービスとの間にポイントツーポイント接続を確立する必要があるシナリオで使用できます。 詳細については、「[接続アプリ](/dynamics365/business-central/dev-itpro/developer/readiness/readiness-connect-apps)」を参照してください。

**[潜在顧客がこの登録オファーを利用する方法について選択してください。]** で、このオファーに使用するオプションを選択します。

- **今すぐ入手する (無料)** – 無料のオファーの一覧を顧客に提示します。
- **無料試用版 (一覧)** – 無料試用版へのリンクが付いたオファーの一覧を顧客に提示します。 オファー登録情報の無料試用版がご利用のサービスによって作成、管理、および構成され、Microsoft によって管理されるサブスクリプションはありません。

    > [!NOTE]
    > 試用版リンクからアプリケーションが受信するトークンは、そのアプリのアカウント作成を自動化するためのユーザー情報を Azure Active Directory (Azure AD) を介して取得するためだけに使用できます。 このトークンを使用した認証には、Microsoft アカウントはサポートされません。

- **連絡してください** – 顧客関係管理 (CRM) システムに接続して、顧客の連絡先情報を収集します。 顧客は、自分の情報を共有する許可を求められます。 これらの顧客の詳細は、オファーの名前と ID のほか、顧客がオファーを見つけたマーケットプレース ソースと一緒に、お客様が構成した CRM システムに送信されます。 CRM の構成の詳細については、「[潜在顧客](#customer-leads)」を参照してください。

## <a name="test-drive"></a>体験版

体験版は、購入前に試用するオプションを提供することで潜在顧客へのオファーを披露し、その結果、コンバージョンが増加し、見込みの高いリードが生成される優れた方法です。 詳細については、「[What is test drive? (テスト ドライブとは)](what-is-test-drive.md)」を参照してください。

一定期間、体験版を有効にするには、 **[体験版を有効にする]** チェック ボックスをオンにします。 オファーから体験版を削除するには、このチェック ボックスをオフにします。

> [!TIP]
> 体験版は、無料試用版とは異なります。 体験版または無料試用版のいずれかまたは両方を提供できます。 これらはどちらも、ソリューションを一定期間、顧客に提供します。 ただし、体験版には、実際の実装シナリオの中で製品の主な機能や利点を体験できる実践的なセルフガイド ツアーも含まれます。

## <a name="customer-leads"></a>潜在顧客

[!INCLUDE [Connect lead management](includes/customer-leads.md)]

詳細については、「[コマーシャル マーケットプレース オファーからの顧客リード](partner-center-portal/commercial-marketplace-get-customer-leads.md)」を参照してください。

左側のナビゲーション メニューの次のタブである **[プロパティ]** に進む前に、 **[下書きの保存]** を選択します。

## <a name="next-steps"></a>次のステップ

- [オファーのプロパティを構成する](dynamics-365-business-central-properties.md)
- [オファーの掲載のベスト プラクティス](gtm-offer-listing-best-practices.md)
