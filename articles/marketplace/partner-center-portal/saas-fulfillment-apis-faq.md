---
title: SaaS Fulfillment API - FAQ | Azure Marketplace
description: Azure Marketplace での SaaS オファーのユーザーによる検出と購入のエクスペリエンス。
author: qianw211
manager: evansma
ms.author: v-qiwe
ms.service: marketplace
ms.topic: conceptual
ms.date: 07/11/2019
ms.openlocfilehash: b324d3b9dca710dca6f5f99ad50ce4d973a42d2a
ms.sourcegitcommit: 10251d2a134c37c00f0ec10e0da4a3dffa436fb3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/13/2019
ms.locfileid: "67869334"
---
# <a name="saas-fulfillment-apis---faq"></a>SaaS Fulfillment API - FAQ

Azure ユーザーが SaaS オファーにサブスクライブできるようにするための Azure Marketplace との統合要件を示します。

## <a name="discovery-experience"></a>検出エクスペリエンス

オファーが発行された後、Azure ユーザーは Azure Marketplace で SaaS オファーを検出できます。 ユーザーは、製品の種類 (SaaS) に基づいてオファーをフィルター処理して、関心のある SaaS サービスを検出できます。

## <a name="purchase-experience"></a>購入エクスペリエンス

ユーザーは、特定の SaaS サービスに関心を持つと、Azure Marketplace からそれにサブスクライブできます。

### <a name="what-does-it-mean-for-an-azure-user-to-subscribe-to-a-saas-offer-in-azure-marketplace"></a>Azure ユーザーが Azure Marketplace で SaaS オファーにサブスクライブするとは、どういう意味ですか?

ユーザーが SaaS サービスに関連する使用条件とプライバシーに関する声明を表示し、Microsoft の請求書で、あなた (SaaS オファーの発行元) によって設定された請求条件に従って、その料金を支払うことに同意することを意味します。 ユーザーは、Azure の既存の支払いプロファイルを使用して、SaaS サービスの利用に対して支払いを行うことができます。

これは、さまざまな理由で便利です。 ユーザーは、信頼できるソースとして Microsoft Cloud Platform を使用して 1 か所で検出とサブスクライブを行うことができます。使用しようとしているすべての ISV ソフトウェアを入念に調べる必要はありません。 また、ユーザーは、既存の支払いプロファイルを使用できます。ISV ソフトウェアごとに個別に明示的に支払いを行う必要はありません。

### <a name="is-the-user-charged-automatically-when-the-offer-is-subscribed"></a>オファーにサブスクライブすると、ユーザーに対して自動的に課金が行われますか?

SaaS オファーにサブスクライブしている間、ユーザーは Microsoft プラットフォームを通じた SaaS サービスの使用料の支払いに同意します。 ただし、課金が開始されるのは、オファーが使用された場合のみです。 ユーザーは、SaaS オファーの使用を開始するためには、そのオファーにアクセスして、アカウントの作成を確認する必要があります。 その後、このユーザーの SaaS サブスクリプションに対して課金を開始するよう Microsoft に通知します。

### <a name="how-are-you-notified-when-a-user-subscribes-to-your-saas-offer"></a>ユーザーが SaaS オファーにサブスクライブすると、どのように通知されますか?

Azure ユーザーは、オファーにサブスクライブした後、Azure ですべてのオファーを検出および管理できます。 既定では、新しくサブスクライブされた SaaS オファーの状態は **[Provisioning, fulfillment pending]\(プロビジョニング中、フルフィルメント保留中\)** です。 この状態では、Azure ユーザーは、Azure portal で SaaS サブスクリプション管理エクスペリエンスを参照するために、 **[アカウントを構成]** するためのアクションが求められます。

ユーザーが **[アカウントの構成]** をクリックすると、ユーザーは SaaS サービスの Web サイトにリダイレクトされます。 移動先の URL は、オファーの発行時に発行元が指定します。 このページは、発行元のランディング ページと呼ばれます。 Azure ユーザーは、Azure の既存の AAD 資格情報に基づいて、SaaS ランディング ページにログインできる必要があります。

Azure ユーザーがランディング ページにリダイレクトされると、トークンがクエリ URL に追加されます。 このトークンは有効期間が短く、24 時間有効です。 その後、このトークンが存在していることを検出し、Microsoft の API を呼び出して、トークンに関連する追加のコンテキストを取得できます。

![ユーザーのサブスクリプション フロー](media/saas-metering-service-integration-flow-a.png)

SaaS オファーのライフサイクルでトランザクション シナリオを処理するための API コントラクトについて詳しくは、[SaaS Fulfillment API](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-fulfillment-api-v2) に関するドキュメントをご覧ください。

### <a name="how-do-you-know-the-saas-offer-to-which-the-user-subscribes-in-azure"></a>ユーザーが Azure でサブスクライブする SaaS オファーはどのように確認しますか?

`Resolve` API への応答には、SaaS サブスクリプションに関連するオファーとプラン情報が含まれます。

### <a name="how-can-the-azure-user-change-the-plan-associated-with-this-azure-subscription"></a>Azure ユーザーは、この Azure サブスクリプションに関連するプランをどのように変更できますか?

* Azure ユーザーは、SaaS サブスクリプションに関連するプランを、SaaS エクスペリエンスで直接変更することも、Microsoft プラットフォームを使用して変更することもできます。

* 変換は、課金サイクル中いつでも実行できます。 変換はすべて確認する必要があり、確認されると有効になります。

* 前払いプラン (**月次**または**年間**) の料金は、日割りで計算されます。 変換の時点までに発生した超過分は、次の請求書で請求されます。 新しいプランに基づいて新しい超過分が生成されます。

>[!Note]
>特定の変換パスをサポートしない場合、ダウングレードをブロックできます。

次のシーケンスは、Azure ユーザーが SaaS エクスペリエンスでプランを変更する場合のフローを取り込んだものです。

![ユーザーのプラン変更フロー](media/saas-metering-service-integration-flow-b.png)

次のシーケンスは、Azure ユーザーが Microsoft のネットショップでプランを変更する場合のフローを取り込んだものです。

![ユーザーのネットショップでのプラン変更フロー](media/saas-metering-service-integration-flow-c.png)

### <a name="how-can-the-azure-user-unsubscribe-from-the-plan-associated-with-azure-subscription"></a>Azure ユーザーは、Azure サブスクリプションに関連するプランからどのように登録を解除できますか?

Azure ユーザーは、購入した SaaS オファーから、SaaS エクスペリエンスで直接登録を解除することも、Microsoft プラットフォームを使用して登録を解除することもできます。 ユーザーが登録を解除すると、次の請求サイクルからは課金されなくなります。

次のシーケンスは、Azure ユーザーが SaaS エクスペリエンスで SaaS オファーの登録を解除する場合のフローを取り込んだものです。

![ユーザーが SaaS エクスペリエンスで登録を解除する](media/saas-metering-service-integration-flow-d.png)

次のシーケンスは、Azure ユーザーが Microsoft のネットショップで登録を解除する場合のフローを取り込んだものです。

![ユーザーが Microsoft のネットショップで登録を解除する](media/saas-metering-service-integration-flow-e.png)

## <a name="next-steps"></a>次の手順

- 詳細については、「[Marketplace の測定サービス API](./marketplace-metering-service-apis.md)」をご覧ください。
