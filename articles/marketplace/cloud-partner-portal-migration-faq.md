---
title: パートナー センターへの移行に関してよく寄せられる質問 | Microsoft コマーシャル マーケットプレース
description: Cloud パートナー ポータルからパートナー センターへのオファーの移行に関してよく寄せられる質問に対する回答。
author: anbene
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: mingshen
ms.openlocfilehash: c4fdde94ea703d194e2de27a8df429f62ea374f4
ms.sourcegitcommit: 6fd8dbeee587fd7633571dfea46424f3c7e65169
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/21/2020
ms.locfileid: "83727480"
---
# <a name="common-questions-about-migrating-from-the-cloud-partner-portal-to-partner-center"></a>Cloud パートナー ポータルからパートナー センターへの移行に関してよく寄せられる質問

この記事では、Cloud パートナー ポータルからパートナー センターへのオファーの移行に関してよく寄せられる質問について説明します。

## <a name="what-does-offer-migration-mean"></a>オファーの移行にはどのような意味がありますか?

現在、Cloud パートナー ポータルからパートナー センターにオファー データを移行しています。これにより、オファーの公開と管理体験が変わります。

| 領域  | [変更点]  |
|-------|----------|
| **公開とオファー管理体験** | パートナー センターで直感的なインターフェイスを使用して、ユーザー体験を向上させることができます。 詳細については、「[パートナー センターと Cloud パートナー ポータルの違い](#what-are-the-differences-between-partner-center-and-the-cloud-partner-portal)」を参照してください。 |
| **マーケットプレースでのオファーの可用性** | 変更はありません。 オファーがマーケットプレースでライブの場合は、移行中および移行の完了後も引き続きライブのままになります。 |
| **新しい購入とデプロイ** | 変更はありません。 引き続き、中断なくオファーを購入してデプロイできます。 |
| **支払** | 移行中または移行後に発生する購入とデプロイは、引き続き通常どおりにお支払いいただけます。 |
|**既存の [Cloud パートナー ポータル API を使用した API 統合](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview)** | 既存の Cloud パートナー ポータル API は、移行後も引き続きサポートされ、既存の統合も機能し続けます。 詳細については、「[Cloud パートナー ポータル REST API は移行後にサポートされますか?](#will-the-cloud-partner-portal-rest-apis-be-supported-post-migration)」を参照してください。 |
| | |

## <a name="can-i-still-access-the-cloud-partner-portal-and-manage-my-offers-during-migration"></a>移行中も引き続き Cloud パートナー ポータルにアクセスしてオファーを管理できますか?

すべてのオファーはパートナー センターに移行されています。 各オファーの移行期間は 24 時間以内であったはずです。 オファーが移行された後は、電子メールで通知を受け取っているはずです。

オファーは移行後、Cloud パートナー ポータルで**限定期間**で読み取り専用モードになります。 このような状態では「パートナー センターに移動しました」と表示され、次のスクリーンショットに示すようにパートナー センターにオファーへのリンクが含まれます。 この時点から、あらゆるオファーの更新プログラムを管理したり、パートナー センターを通して新しい特別なプランを作成したりできるようになります。

:::image type="content" source="media/migration-faq/all-offers-2.png" alt-text="パートナー センターに移行されたオファーに対して指定されたメッセージを示しています。":::

:::image type="content" source="media/migration-faq/offer-has-moved.png" alt-text="移行されたオファーの Cloud パートナー ポータルを示しています。":::

## <a name="how-will-i-create-new-offers"></a>新しいオファーの作成方法を教えてください。

Cloud パートナー ポータルからパートナー センターで新しいオファーを作成するよう指示されます。

:::image type="content" source="media/migration-faq/create-new-offer-1.png" alt-text="Cloud パートナー ポータルで新しいオファーを作成するメニューを示しています":::

新しいオファーを選択すると、以下のようなメッセージが表示されます。

:::image type="content" source="media/migration-faq/create-new-offer-2.png" alt-text="CPP で新しいオファーを作成した際に受け取るメッセージを示しています":::

## <a name="do-i-need-to-create-a-new-account-to-manage-offers-in-partner-center"></a>パートナー センターでオファーを管理するには、新しいアカウントを作成する必要がありますか?

いいえ。 基本的なアカウントが維持されます。すでにパートナー センターでこれを管理しているはずです。 つまり、既存のパートナーの場合は、既存の Cloud パートナー ポータル アカウント資格情報を使用して、移行後のパートナー センターにログインできます。 Cloud パートナー ポータルからパートナー センターに移行するのは、オファーおよび関連のある管理エクスペリエンスだけです。 新しいアカウントを作成しないでください。オファーは新しいアカウントに関連付けられていません。

## <a name="i-see-a-message-in-the-cloud-partner-portal-to-activate-my-account-what-does-this-mean"></a>アカウントをアクティブ化するためのメッセージが Cloud パートナー ポータルに表示されます。これはどういう意味ですか?

アカウントをパートナー センターに適切に移行し、オファーの移行完了後にパートナー センターでオファーを管理できるようにするには、さらに詳細な情報が必要です。 アカウントのアクティブ化に関する詳細については、「[Cloud パートナー ポータルからパートナー センターへのアカウント移行](https://docs.microsoft.com/azure/marketplace/partner-center-portal/account-migration-from-cpp-to-pc)」を参照してください。

アカウントのアクティブ化を完了するために必要な手順は、アカウント ロールによって異なります。

| アカウント ロール | アクティブ化の手順 |
|--------------|----------------|
|所有者 | Cloud パートナー ポータルで [[パブリッシャー プロファイル]](https://cloudpartner.azure.com/#profile) ページに進み、バナーのリンクをクリックして有効にします。 アカウントのアクティブ化を完了するため、パートナー センターにリダイレクトされます。 |
| Contributor | 所有者ロールを持つアカウントのユーザーのみが、アカウントをアクティブ化できます。 アカウントのアクティブ化を完了するには、アカウント所有者に問い合わせてください。 アカウント所有者はバナー メッセージに表示されます。 |
| | |

## <a name="im-having-trouble-logging-in-to-my-account-and-opening-a-support-ticket"></a>アカウントにログインしてサポート チケットを開くことができません。

アカウントにログインできない場合は、[サポート チケット](https://partner.microsoft.com/support/v2/?stage=1)を開くことができます。

## <a name="where-can-i-find-documentation-on-the-new-partner-center-publishing-experience"></a>新しいパートナー センターの公開エクスペリエンスに関するドキュメントはどこで入手できますか?

[[コマーシャル マーケットプレースのドキュメント]](https://docs.microsoft.com/azure/marketplace/) に移動してください。 その後、 **[パートナー センターのコマーシャル マーケットプレース ポータル]**   >  **[新規オファーの作成]** と進むと、各タイプのオファーを作成するためのヘルプ トピックが表示されます。

:::image type="content" source="media/migration-faq/marketplace-help-topics.png" alt-text="パートナー センターのヘルプ トピックを示しています":::

### <a name="what-are-the-differences-between-partner-center-and-the-cloud-partner-portal"></a>パートナー センターと Cloud パートナー ポータルの差は何ですか?

Cloud パートナー ポータルとパートナー センターの間で次のような違いに気づくかもしれません。

### <a name="modular-publishing-capabilities"></a>モジュール公開機能

パートナー センターには、常にすべての更新プログラムを一度に公開するのではなく、発行する変更を選択できるモジュール形式の公開オプションが用意されています。 たとえば、以下のスクリーンショットは、公開されるように選択された変更のみが **[プロパティ]** と **[オファー リスト]** への変更であることを示しています。

:::image type="content" source="media/migration-faq/review-and-publish-migration.png" alt-text="[プレビューと公開] ページを示しています":::

公開しない更新内容は、下書きとして保存されます。 引き続きオファーのプレビューを使用し、公開する前にオファーを確認します。

### <a name="rich-text-format"></a>リッチ テキスト形式

[オファーのリストとプランのリスト] ページのリッチ テキスト エディターを使用して、オファーとプランの説明を拡充します。

:::image type="content" source="media/migration-faq/rich-text-editor-migration.png" alt-text="リッチ テキスト エディターを示しています":::

### <a name="enhanced-preview-options"></a>拡充されたプレビュー オプション

パートナー センターには、フィルター オプションが改善された[比較機能](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#compare-changes-to-marketplace-offers)が含まれています。 これにより、オファーのプレビューとライブ バージョンを比較できます。

:::image type="content" source="media/migration-faq/enhanced-preview.png" alt-text="比較機能を示しています":::

### <a name="branding-and-navigation-changes"></a>ブランド化とナビゲーションの変更

ブランド化がいくつか変更されていることがわかります。 たとえば、「SKU」はパートナー センターで「プラン」としてブランド化されています。

:::image type="content" source="media/migration-faq/plan-overview-migration.png" alt-text="プランの概要を示しています。":::

また、Cloud パートナー ポータルの **[マーケットプレース]** または **[店頭の詳細]** (コンサルティング サービス、Power BI アプリ) ページで提供される情報は、パートナー センターの **[オファーのリスト]** ページで収集されます。

:::image type="content" source="media/migration-faq/offer-listing-migration.png" alt-text="オファーのリスト ページを示しています。":::

Cloud パートナー ポータルの 1 ページで SKU に提供するために使用した情報をパートナー センターの複数のページで収集できるようになりました。

* プラン設定ページ
* [プランのリスト] ページ
* [プランの可用性] ページ
* [技術的な構成の計画] ページ (スクリーンショットを参照)

:::image type="content" source="media/migration-faq/tech-config-migration.png" alt-text="[技術的な構成の計画] ページを示しています。":::

オファー ID がオファーの左側のナビゲーション バーに表示されるようになりました。

:::image type="content" source="media/migration-faq/offer-id-offer-overview.png" alt-text="オファー ID を含む左側のナビゲーション メニューを示しています。":::

### <a name="stop-selling-an-offer"></a>オファーの販売を停止する

パートナー センター ポータルから直接、マーケットプレースで[オファーの販売停止](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#stop-selling-an-offer-or-plan)を要求できます。 このオプションは、オファーの **[オファー概要]** ページで利用できます。

:::image type="content" source="media/migration-faq/stop-selling-migration.png" alt-text="販売停止オプションを含む [オファー概要] ページを示しています。":::

## <a name="what-pages-in-partner-center-correspond-to-pages-i-used-in-the-cloud-partner-portal"></a>Cloud パートナー ポータルで使用していたページに対応するパートナーセンターのページは何ですか?

下表は、2 つのポータル間で該当するリンクを示しています。

| ページ | Cloud パートナー ポータル リンク | パートナー センター リンク |
|------|---------------------------|---------------------|
| **[すべてのプラン] ページ** | https://cloudpartner.azure.com/#alloffers | https://partner.microsoft.com/dashboard/commercial-marketplace/overview |
| **[すべての発行元] ページ** | https://cloudpartner.azure.com/#publishers | https://partner.microsoft.com/dashboard/account/v3/publishers/list |
| **パブリッシャー プロファイル** | https://cloudpartner.azure.com/#profile | https://partner.microsoft.com/dashboard/account/management |
| **[ユーザー] ページ** | https://cloudpartner.azure.com/#users | https://partner.microsoft.com/dashboard/account/usermanagement |
| **[履歴] ページ** | https://cloudpartner.azure.com/#history | [履歴] 機能はまだパートナー センターではサポートされていません。 |
| **Insights ダッシュボード** | https://cloudpartner.azure.com/#insights | https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary |
| **支払いレポート** | https://cloudpartner.azure.com/#insights/payout | https://partner.microsoft.com/dashboard/payouts/reports/incentivepayments |
| | |

## <a name="will-the-cloud-partner-portal-rest-apis-be-supported-post-migration"></a>Cloud パートナー ポータル REST API は移行後もサポートされますか?

Cloud パートナー ポータル API はパートナー センターに統合されており、オファーがパートナー センターに移行された後も引き続き機能します。 統合によりわずかな変更が行われました。 下表でこのような変更を確認し、パートナー センターへの移行後もお使いになっているコードが引き続き機能するようにしてください。

| **API** | **変更の説明** | **影響** |
| ------- | ---------------------- | ---------- |
| 公開、GoLive、キャンセル後 | 移行されたオファーの場合、応答ヘッダーは異なる形式になりますが、操作状態を取得する相対パスを示して、引き続き同じように動作します。 | オファーに対応する POST 要求のいずれかを送信する場合、オファーの移行状態に応じて Location ヘッダーは次の 2 つの形式のいずれかになります。<ul><li>移行されていないオファー<br>`/api/operations/{PublisherId}${offerId}$2$preview?api-version=2017-10-31`</li><li>移行されたオファー<br>`/api/publishers/{PublisherId}/offers/{offereId}/operations/408a4835-0000-1000-0000-000000000000?api-version=2017-10-31`</li> |
| GET 操作 | 以前に応答の 'notification-email' フィールドをサポートしていたオファーの場合、このフィールドは非推奨になり、移行されたオファーでは返されなくなります。 | 移行されたオファーの場合、要求で指定されたメール アドレスのリストに通知が送信されなくなります。 その代わりに、API サービスとパートナー センターの通知メール プロセスが連携され、メールが送信されます。 具体的には、パートナー センターの [アカウント設定] の [販売元の連絡先情報] セクションに設定されたメール アドレスに通知が送信され、操作の進行状況が通知されます。<br><br>パートナー センターで [[アカウント設定]](https://partner.microsoft.com/dashboard/account/management) の [販売元の連絡先情報] セクションに設定されているメール アドレスをチェックし、通知用の正しいメール アドレスが指定されていることを確認します。  |
| | | |
