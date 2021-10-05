---
title: コマーシャル マーケットプレースで SaaS オファーを作成する
description: Azure Marketplace で、Microsoft AppSource、Azure Marketplace、またはクラウド ソリューション プロバイダー (CSP) プログラムでリスト登録または販売を行うために新しいサービスとしてのソフトウェア (SaaS) オファーを作成します。
author: mingshen-ms
ms.author: mingshen
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 09/27/2021
ms.openlocfilehash: 5da6232a9bedeeb8228caecc79c7a7160630a8cd
ms.sourcegitcommit: 10029520c69258ad4be29146ffc139ae62ccddc7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/27/2021
ms.locfileid: "129080820"
---
# <a name="create-a-saas-offer"></a>SaaS オファーを作成する

コマーシャル マーケットプレースの発行元は、潜在顧客が SaaS ベースの技術ソリューションを購入できるように、サービスとしてのソフトウェア (SaaS) オファーを作成することができます。 この記事では、Microsoft コマーシャル マーケットプレース用の SaaS オファーを作成するプロセスについて説明します。

## <a name="before-you-begin"></a>開始する前に

まだ行っていない場合は、[SaaS オファーの計画](plan-saas-offer.md)に関する記事をご確認ください。 SaaS アプリの技術的な要件、およびオファーを作成するときに必要となる情報と資産について説明されています。 コマーシャル マーケットプレースで単純なリスト ( **[問い合わせ]** リスト オプション) を公開する予定がない限り、SaaS アプリケーションは認証に関する技術的な要件を満たしている必要があります。

> [!IMPORTANT]
> 別の開発/テスト (DEV) オファーと別の運用 (PROD) オファーを作成することをお勧めします。 この記事では、PROD オファーの作成方法について説明します。 DEV オファーの作成の詳細については、[「テスト SaaS オファーを作成する」](create-saas-dev-test-offer.md)を参照してください。

## <a name="create-a-saas-offer"></a>SaaS オファーを作成する

[!INCLUDE [Workspaces view note](./includes/preview-interface.md)]

#### <a name="workspaces-view"></a>[ワークスペース ビュー](#tab/workspaces-view)

1. [パートナー センター](https://partner.microsoft.com/dashboard/home)にサインインします。

1. ホーム ページで、 **[Marketplace offers]\(Marketplace のオファー\)** タイルを選択します。

    [ ![パートナー センターのホーム ページにある [Marketplace offers]\(Marketplace のオファー\) タイルを示しています。](./media/workspaces/partner-center-home.png) ](./media/workspaces/partner-center-home.png#lightbox)

1. [Marketplace offers]\(Marketplace のオファー\) ページで、 **[+ 新しいオファー]**  >  **[サービスとしてのソフトウェア]** の順に選択します。

    [ ![[新しいオファー] の一覧の SaaS オファー オプションを示しています。](./media/new-offer-saas-workspaces.png) ](./media/new-offer-saas-workspaces.png#lightbox)

1. **[New Software as a Service]\(新しいサービスとしてのソフトウェア\)** ダイアログ ボックスで、 **[オファー ID]** を入力します。 この ID は、コマーシャル マーケットプレースの一覧と Azure Resource Manager テンプレートの URL に表示されます (該当する場合)。 たとえば、このボックスに「**test-offer-1**」と入力すると、オファーの Web アドレスは `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1` になります。
   + アカウント内の各オファーには、一意のオファー ID が必要です。
   + 使用できるのは小文字と数字だけです。 ハイフンとアンダースコアを含めることができますが、スペースは使用できず、文字数は 50 文字に制限されています。
   + **[作成]** を選択した後で、オファー ID を変更することはできません。

1. **[オファーのエイリアス]** を入力します。 これは、パートナー センター内でオファーに使用される名前です。

   + この名前はコマーシャル マーケットプレースには表示されず、顧客に表示されるオファー名やその他の値とは異なります。
   + **[作成]** を選択した後で、オファーの別名を変更することはできません。
1. オファーを生成して続行するには、 **[作成]** を選択します。

#### <a name="current-view"></a>[現在のビュー](#tab/current-view)

1. [パートナー センター](https://partner.microsoft.com/dashboard/home)にサインインします。
1. 左側のナビゲーション メニューで、 **[商業マーケットプレース]**  >  **[概要]** の順に選択します。
1. **[概要]** タブで、 **[+ 新しいオファー]**  >  **[サービスとしてのソフトウェア]** の順に選択します。

   :::image type="content" source="./media/new-offer-saas.png" alt-text="左側のナビゲーション メニューと新しいオファーの一覧を示しています。":::

1. **[新しいオファー]** ダイアログ ボックスで、 **[オファー ID]** を入力します。 この ID は、コマーシャル マーケットプレースの一覧と Azure Resource Manager テンプレートの URL に表示されます (該当する場合)。 たとえば、このボックスに「**test-offer-1**」と入力すると、オファーの Web アドレスは `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1` になります。
   + アカウント内の各オファーには、一意のオファー ID が必要です。
   + 使用できるのは小文字と数字だけです。 ハイフンとアンダースコアを含めることができますが、スペースは使用できず、文字数は 50 文字に制限されています。
   + **[作成]** を選択した後で、オファー ID を変更することはできません。

1. **[オファーのエイリアス]** を入力します。 これは、パートナー センター内でオファーに使用される名前です。

   + この名前はコマーシャル マーケットプレースには表示されず、顧客に表示されるオファー名やその他の値とは異なります。
   + **[作成]** を選択した後で、オファーの別名を変更することはできません。
1. オファーを生成して続行するには、 **[作成]** を選択します。

---

## <a name="configure-your-saas-offer-setup-details"></a>SaaS オファーのセットアップの詳細を構成する

**[オファーのセットアップ]** タブの **[セットアップの詳細]** で、Microsoft を通じてオファーを販売するか、取引を個別に管理するかを選択します。 Microsoft を通じて販売されるオファーは "_取引可能オファー_" と呼ばれます。これは、Microsoft が発行元の代わりにソフトウェア ライセンス費用に対する金銭のやり取りを円滑に進めることを意味します。 これらのオプションの詳細については、「[リスト オプション](plan-saas-offer.md#listing-options)」と「[公開オプションを選定する](determine-your-listing-type.md)」を参照してください。

1. Microsoft を通じて販売し、取引を円滑に進めるには、 **[はい]** を選択します。 「[体験版を有効にする](#enable-a-test-drive-optional)」に進みます。
1. コマーシャル マーケットプレースを通じてオファーを一覧表示し、取引を個別に処理するには、 **[いいえ]** を選択し、次のいずれかの操作を行います。
   + オファーの無料サブスクリプションを提供するには、 **[今すぐ入手 (無料)]** を選択します。 次に、表示される **[オファーの URL]** ボックスに、顧客が [Azure Active Directory (Azure AD) を使用したワンクリック認証](azure-ad-saas.md)で試用版を入手できる (*http* または *https* で始まる) URL を入力します。 たとえば、「 `https://contoso.com/saas-app` 」のように入力します。
   + 30 日間の無料試用版を提供するには、 **[無料試用版]** を選択し、表示される **[試用版 URL]** ボックスに、顧客が [Azure Active Directory (Azure AD) を使用したワンクリック認証](azure-ad-saas.md)で試用版を入手できる (*http* または *https* で始まる) URL を入力します。 たとえば、「 `https://contoso.com/trial/saas-app` 」のように入力します。
   + 潜在顧客からオファーの購入について連絡を受けるには、 **[問い合わせ]** を選択します。

    > [!NOTE]
    > 状況が変化した場合、コマーシャル マーケットプレース オファーを通じて、公開された掲載専用オファーを販売に変換できますが、公開された取引可能オファーを掲載専用オファーに変換することはできません。 代わりに、新しい掲載専用オファーを作成し、公開された取引可能オファーの配布を停止する必要があります。

## <a name="enable-a-test-drive-optional"></a>体験版を有効にする (省略可能)

体験版は、構成済み環境に一定時間アクセスできるようにすることで、潜在顧客に対してオファーを紹介するのに最適な方法です。 体験版を提供すると、コンバージョン率が向上し、質の高い潜在顧客を獲得できます。 体験版の詳細については、「[体験版とは](./what-is-test-drive.md)」を参照してください。

> [!TIP]
> 体験版は、無料試用版とは異なります。 体験版または無料試用版のいずれかまたは両方を提供できます。 これらはどちらも、ソリューションを一定期間、顧客に提供します。 ただし、体験版には、実際の実装シナリオの中で製品の主な機能や利点を体験できる実践的なセルフガイド ツアーも含まれます。

### <a name="to-enable-a-test-drive"></a>体験を有効にするには

1. **[体験版]** で、 **[体験版を有効にする]** チェック ボックスをオンにします。
1. 表示される一覧から、体験版の種類を選択します。

## <a name="configure-lead-management"></a>リード管理の構成

カスタマー リレーションシップ マネジメント (CRM) システムをコマーシャル マーケットプレースのオファーに接続して、顧客が興味を示したり製品をデプロイしたりしたときに顧客の連絡先情報を受け取ることができるようにします。 この接続は、オファーの作成中または作成後にいつでも変更できます。

> [!NOTE]
> Microsoft を通じてオファーを販売している場合、または **[問い合わせ]** リスト オプションを選択した場合は、リード管理を構成する必要があります。 詳細なガイダンスについては、「[コマーシャル マーケットプレース オファーからの潜在顧客](partner-center-portal/commercial-marketplace-get-customer-leads.md)」を参照してください。

### <a name="configure-the-connection-details-in-partner-center"></a>パートナー センターで接続の詳細を構成する

[!INCLUDE [Customer leads](includes/customer-leads.md)]

## <a name="configure-microsoft-365-app-integration"></a>Microsoft 365 アプリの統合を構成する

SaaS オファーとそれに関連する Microsoft 365 アプリ消費の[統合された検出と配信](plan-SaaS-offer.md)を、リンクをすることで行うことができます。

### <a name="integrate-with-microsoft-api"></a>Microsoft API との統合

1. SaaS オファーが Microsoft Graph API と統合されていない場合は、 **[いいえ]** を選択します。 引き続き、公開されている Microsoft 365 アプリ消費クライアントをリンクします。  
1. SaaS オファーが Microsoft Graph API と統合されている場合は、 **[はい]** を選択し、Microsoft Graph API と統合するために作成して登録した Azure Active Directory アプリ ID を指定します。

### <a name="link-published-microsoft-365-app-consumption-clients"></a>公開された Microsoft 365 アプリ消費クライアントをリンクする

1. SaaS オファーで動作する、公開された Office アドイン、Teams アプリ、または SharePoint Framework ソリューションがない場合は、 **[いいえ]** を選択します。
1. SaaS オファーで動作する、公開された Office アドイン、Teams アプリ、または SharePoint Framework ソリューションがある場合は、 **[はい]** を選択し、 **[+別の AppSource リンクを追加]** を選択して新しいリンクを追加します。  
1. 有効な AppSource リンクを指定してください。
1. **[+別の AppSource リンクを追加]** を選択し、有効な AppSource リンクを指定して、すべてのリンクを追加し続けます。  
1. SaaS オファーの一覧ページに、リンクされた製品が表示される順序は、ランク値によって示されます。これを変更するには、[=] アイコンを選択し、ホールドしながらアイコンを一覧で上下に移動させます。 
1. 製品行の **[削除]** を選択すると、リンクされた製品を削除できます。  

> [!IMPORTANT]
> リンクされた製品を販売停止した場合、SaaS オファーで自動的にリンク解除されることはありません。リンクされた製品の一覧から削除し、SaaS オファーを再送信する必要があります。  

## <a name="next-steps"></a>次のステップ

- [SaaS オファーのプロパティの構成](create-new-saas-offer-properties.md)
