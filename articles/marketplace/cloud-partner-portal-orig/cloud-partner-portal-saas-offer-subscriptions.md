---
title: SaaS - Azure で販売する | Microsoft Docs
description: SaaS アプリケーションのサブスクリプション課金モデルとその実装方法について説明します。
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/17/2018
ms.author: pbutlerm
ms.openlocfilehash: f193138fbc5e779c3a6671757320d8918e08db46
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/05/2018
ms.locfileid: "48808096"
---
<a name="saas---sell-through-azure"></a>SaaS - Azure で販売する
=========================

この記事では、Cloud パートナー ポータルでの SaaS アプリケーションのサブスクリプション課金モデルとその実装方法について説明します。


<a name="overview"></a>概要
--------

Azure Marketplace では SaaS サービスを発行して収益化することができます。 顧客は Azure から直接請求を受け取ることができ、アクティブ化したすべてのリソースとサービスについての請求が Azure に一本化されます。 発行については、SaaS のサブスクリプションには次の利点があります。

-   **一貫性のある発行エクスペリエンス** -- Azure Marketplace で既に SaaS オファーまたは他のオファーを発行している場合、発行エクスペリエンスは同じ発行ポータルを使用します。
-   **検出用の新しいネットショップ** -- 発行されるすべてのオファーは、外部の Azure Marketplace ネットショップと Azure portal 内の Azure Marketplace 拡張機能の両方に表示されます。
-   **統合された課金** -- Azure が販売しているすべてのリージョンで販売でき、Azure に課金を任せることができます。
-   **統合されたリード生成** -- Azure のユーザーが Azure Marketplace を使用して SaaS サービスをサブスクライブするたびに、好みの CRM で Azure からリードを自動的に受け取ることができます。
-   **Microsoft 販売者との共同販売** -- 双方向での潜在顧客の共有、カタログでの優先表示、Microsoft の販売者との足並みを揃えた協業の資格を得ることができます。

<a name="billing-models"></a>課金モデル
--------------

現在サポートされている唯一の課金モデルは、SaaS サービスのサブスクリプション単位の月額固定料金です。

**注:** 将来、新しい課金モデルを使用できるようになる場合があります。

各 SaaS オファーは、1 つまたは複数のプラン (たとえば、Basic または Premium) を持つことができます。 各プランには、いくつかの基本的なメタデータと共に、価格が関連付けられています。

パートナーは、プランの定義を完全に制御できます。 たとえば、Basic プランは何で構成されるのでしょうか。 プランはパートナーが定義し、パートナーはプランの発行の一部としてプランの説明に必要なテキストを提供できます。

プランに関連付けられた価格は、Azure ユーザーがサービスの使用に対して支払う月額固定料金です。

### <a name="what-is-not-supported-today"></a>現在サポートされていないもの

-   カスタム ユニットに基づく課金 -- たとえば、要求の数に基づく設定価格。
-   シートの割り当てに基づく課金 -- たとえば、ユーザーの数に基づいて Azure ユーザーがライセンスを購入できるようにすること。

<a name="end-to-end-flow"></a>エンド ツー エンドのフロー
---------------

最初にエンド ユーザーから見た SaaS サブスクリプション オファーのフローを示します

**注:** このフローの説明では、パートナーは "Contoso demo SaaS" という名前の SaaS オファーを Azure Marketplace で発行しているものとします。

![SaaS サブスクリプションのユーザーのフロー](media/saas-offer-subscription/saas-subscription-user-flow.png)

Azure ユーザーは、SaaS サービスと次のようにやり取りできます。

-   Azure Marketplace で SaaS サービスを見つける
-   Azure で SaaS サービスに登録する
-   Azure portal から SaaS サービスに移動する
-   SaaS サービスにアカウントを作成し、SaaS でアカウントを管理する (プランの変更/削除)
-   Azure portal から SaaS サービスを登録解除する

<a name="discover-your-saas-service-in-azure-marketplace"></a>Azure Marketplace で SaaS サービスを見つける
-----------------------------------------------

ユーザーが Azure portal で Azure Marketplace を開始すると、[Software as a service (SaaS)] というカテゴリが表示されます。

![カテゴリ メニューを使用して SaaS を見つける](media/saas-offer-subscription/saas-category-menu.png)

ユーザーは、SaaS サービスを検索することもできます。

![検索を使用して SaaS を見つける](media/saas-offer-subscription/saas-cpp-search.png)

ユーザーはサービスの詳細を確認してから、**[作成]** をクリックします。

![SaaS サブスクリプションの作成](media/saas-offer-subscription/saas-create-subscription.png)

### <a name="subscribe-to-saas-service-in-azure"></a>Azure で SaaS サービスに登録する

ユーザーは、Azure から SaaS サービスに登録できます。

![SaaS サービスに登録する](media/saas-offer-subscription/saas-subscribe-signup.png)

ユーザーは、SaaS サービスに登録するときに、次の情報を提供します

-   名前 -- ユーザーが Azure でこの SaaS サブスクリプション インスタンスを発見または管理できる名前。
-   サブスクリプション -- SaaS サービスに対する課金を関連付けるサブスクリプション コンテキスト。
-   プラン -- ユーザーが登録したい SaaS サービス プラン。

オファー発行の一部として提供される使用条件のドキュメントは、ユーザーが SaaS サービスに登録する前に、ユーザーにも表示されます。

ユーザーは、**[サブスクライブ]** をクリックしてサービスに登録できるようになります。
サブスクリプションが完了すると、Azure はポータルで通知を送信します。

### <a name="navigate-to-the-saas-service-from-azure-portal"></a>Azure portal から SaaS サービスに移動する

ユーザーは、**[リソースに移動]** をクリックして、SaaS サブスクリプション インスタンスを表示または管理します。

![SaaS インスタンスを表示または管理する](media/saas-offer-subscription/saas-go-to-resource.png)

ユーザーは、最初に SaaS サービスでアカウントを構成する必要があることを通知されます。 請求は、SaaS サービスが Azure に課金の開始を通知した後で開始され、これはユーザーが SaaS サービス サイトでアカウントを作成したときです。

![SaaS インスタンスを構成する](media/saas-offer-subscription/saas-configure-account.png)

ユーザーは、**[アカウントの構成]** をクリックすると、SaaS サービス エンドポイントにリダイレクトされます。 このリダイレクトの間に、クエリ パラメーターとしてトークンが渡されます。 例: 

![SaaS アカウント トークン](media/saas-offer-subscription/saas-account-token.png)

このトークンの値に注意してください。 このトークンは短期のものであり、Azure でサブスクリプション ID を取得するために解決する必要があります。

<a name="creating-a-saas-offer-subscription"></a>SaaS オファー サブスクリプションの作成
----------------------------------

このエクスペリエンスを構築するには、2 つの作業を行う必要があります。

-   SaaS サービスの Web サイトを Microsoft の SaaS API と接続します。 この接続を作成する方法については、「[Azure を通じた SaaS の販売 - API](./cloud-partner-portal-saas-subscription-apis.md)」をご覧ください。  
-   Cloud パートナー ポータルの **[Technical Info]\(技術情報\)** セクションで **[Sell through Azure]\(Azure を通じて販売\)** を有効にし、構成の詳細をすべて提供します。

![SaaS 新規オファーの技術情報](media/cpp-creating-saas-offers/saas-new-offer-technical-info-2.png)

以下では、**[Technical Info]\(技術情報\)** セクションのすべての必須フィールドについて説明します。

|  **オファーのフィールド**                 |  **説明**                                   |
|  ----------------                 |  -------------------------------------             |
| Preview Subscription IDs (プレビュー サブスクリプション ID)          | 一般に公開する前にプレビューでオファーをテストするために使用されるすべての Azure サブスクリプション識別子。 |
| Getting Started Instructions (使用開始時の説明)      | SaaS アプリに接続するときに役立つ、顧客と共有する指示。 `<p>`、`<h1>`、`<li>` のようなタグなど、基本的な HTML を使用できます。  |
| Landing Page URL (ランディング ページの URL)                  | Azure portal から取得した後の顧客に対して表示するサイトの URL。 この URL は、Microsoft との取引を容易にするための接続 API を受信するエンドポイントにもなります。  |
| Connection Webhook (接続 Webhook)                | Microsoft が顧客に代わってパートナーに送信する必要があるすべての非同期イベント (例: Azure サブスクリプションが無効になりました) のために、パートナーは Microsoft に接続 Webhook を提供する必要があります。 Webhook システムをまだ導入していない場合、最も簡単な構成は、送信されたイベントをリッスンする HTTP エンドポイント ロジック アプリを作成し、イベントを適切に処理することです。  詳しくは、「[ロジック アプリで HTTP エンドポイントを通じてワークフローを呼び出し、トリガーし、入れ子にする](https://docs.microsoft.com/azure/logic-apps/logic-apps-http-endpoint)」をご覧ください。 |
| Azure AD Tenant ID and App ID (Azure AD のテナント ID とアプリ ID)     | Azure portal 内では、Microsoft の 2 つのサービス間の接続が認証済みの通信の背後で行われることを Microsoft が検証できるように、パートナーは Active Directory アプリを作成する必要があります。 これらのフィールドについては、AD アプリを作成し、対応するテナント ID とアプリ ID を貼り付ける必要があります。 |
|  |  |


最後に、**[Sell through Azure]\(Azure を通じて販売\)** を選択すると、**[Plans]\(プラン\)** という追加セクションが表示されます。 プランは、[Sell through Azure]\(Azure を通じて販売\) を選択した場合にのみ必要です。 このセクションには、SaaS アプリでサポートされる特定のプランとそれに対応する価格が一覧表示されます。 現時点では、月額料金と、1 または 3 か月間の無料アクセスの許可に対応しています。 これらのプランと価格は、パートナーの SaaS アプリ サイトで示されているプランおよび価格と正確に一致する必要があります。
