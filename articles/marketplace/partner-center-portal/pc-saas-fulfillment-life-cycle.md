---
title: SaaS サブスクリプションのライフサイクルの管理 | Microsoft コマーシャル マーケットプレース
description: Fulfillment API バージョン 2 を使用して SaaS サブスクリプションのライフサイクルを管理する方法について説明します。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 10/27/2021
author: saasguide
ms.author: souchak
ms.openlocfilehash: dfe47c220cb0dc427d9ff00bdfbaa85da949f520
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/09/2021
ms.locfileid: "132063878"
---
# <a name="managing-the-saas-subscription-life-cycle"></a>SaaS サブスクリプション ライフサイクルの管理

コマーシャル マーケットプレースでは、エンド ユーザーによる購入後の SaaS サブスクリプションのライフ サイクル全体が管理されます。 また、実際の SaaS サブスクリプションのアクティブ化、使用、更新、および取り消しを駆動するメカニズムとして、ランディング ページ、Fulfillment API、Operations API、Webhook が使用されています。 エンド ユーザーの請求書は、Microsoft が管理する SaaS サブスクリプションの状態に基づきます。

## <a name="states-of-a-saas-subscription"></a>SaaS サブスクリプションの状態

次の図は、SaaS サブスクリプションの状態と適用できるアクションを示しています。

[ ![マーケットプレースにおけるサービスとしてのソフトウェアのサブスクリプションのライフ サイクルを示す図。](./media/saas-subscription-lifecycle-api-v2.png) ](./media/saas-subscription-lifecycle-api-v2.png#lightbox)

### <a name="purchased-but-not-yet-activated-pendingfulfillmentstart"></a>購入済みであるが、まだアクティブ化されていない (*PendingFulfillmentStart*)

エンド ユーザーまたはクラウド ソリューション プロバイダー (CSP) がコマーシャル マーケットプレースで SaaS プランを購入すると、その購入が公開元に通知されます。 その後、公開元はそのエンド ユーザー用に、公開元側で新しい SaaS アカウントを作成して構成できます。

アカウントの作成が行われるようにするには:

1. 顧客は Microsoft AppSource または Azure portal での購入が正常に終了した後に、SaaS オファーで使用可能な **[Configure account now]\(今すぐアカウントを構成\)** ボタンを選択します。 代わりに、顧客は購入後すぐに受信する電子メールの **[今すぐ構成]** ボタンを使用することもできます。
2. その後、新しいブラウザー タブで token パラメーター (コマーシャル マーケットプレースからの購入 ID トークン) を含むランディング ページの URL が開かれることで、Microsoft からパートナーに対して購入に関する通知が行われます。

このような呼び出しの例として `https://contoso.com/signup?token=<blob>` がありますが、パートナー センターでは、この SaaS オファーのランディング ページの URL は `https://contoso.com/signup` として構成されています。 このトークンにより、SaaS の購入と顧客を一意に識別する ID が発行者に提供されます。

[!INCLUDE [pound-sign-note](../includes/pound-sign-note.md)]

> [!IMPORTANT]
> ランディング ページの URL は毎日一日中稼働し、いつでも Microsoft からの新しい呼び出しを受け取る準備ができている必要があります。 ランディング ページが使用できなくなった場合、お客様は SaaS サービスにサインアップして使用を開始することができません。

次に、公開元は Microsoft に "*トークン*" を返す必要があります。これは、[SaaS Resolve API](pc-saas-fulfillment-subscription-api.md#resolve-a-purchased-subscription) を呼び出し、`x-ms-marketplace-token header` ヘッダー パラメーターの値としてそのトークンを入力して行います。 Resolve API 呼び出しの結果として、そのトークンが、購入の一意の ID、購入されたオファーの ID、購入されたプランの ID などの SaaS 購入の詳細と交換されます。

ランディング ページでは、顧客は Azure Active Directory (Azure AD) シングル サインオン (SSO) を使用して、新規または既存の SaaS アカウントにサインインする必要があります。

>[!NOTE]
>顧客が Microsoft 側からの構成プロセスを開始するまで、SaaS の購入は公開元に通知されません。

公開元は、このフローに Microsoft が要求するユーザー エクスペリエンスを提供するために、SSO を実装する必要があります。 SSO を構成するときに、必ず、マルチテナント Azure AD アプリケーションを使用し、職場と学校のアカウントの両方または個人の Microsoft アカウントを許可してください。 この要件が適用されるのはランディング ページのみで、Microsoft 資格情報を使用して既にサインインしているときに SaaS サービスにリダイレクトされるユーザー向けです。 SSO は、SaaS サービスへのすべてのサインインに必要とされるわけではありません。

> [!NOTE]
>SSO で、管理者がアプリに対するアクセス許可を付与しなければならないことが求められる場合、パートナー センターでのオファーの説明で、管理者レベルのアクセス許可が必要であることを明記する必要があります。 この開示は、[コマーシャル マーケットプレースの認定ポリシー](/legal/marketplace/certification-policies#10003-authentication-options)に準拠するためです。

サインインした後、顧客は公開元側で SaaS 構成を完了する必要があります。 その後、公開元は、SaaS アカウントのプロビジョニングが完了したことを示す通知を Azure Marketplace に送信するために、[Activate Subscription API](pc-saas-fulfillment-subscription-api.md#activate-a-subscription) を呼び出す必要があります。
この操作により、顧客の請求期間が開始されます。 Activate Subscription API の呼び出しが失敗した場合、顧客は購入に対して請求されません。

![プロビジョニング シナリオの API 呼び出しを示す図。](./media/saas-update-api-v2-calls-from-saas-service-a.png) 

### <a name="active-subscribed"></a>アクティブ (*Subscribed*)

"*アクティブ (Subscribed)* " は、プロビジョニング済み SaaS サブスクリプションの安定した状態です。 Microsoft 側で [Activate Subscription API](pc-saas-fulfillment-subscription-api.md#activate-a-subscription) 呼び出しが処理された後、SaaS サブスクリプションが *Subscribed* としてマークされます。 これで、顧客は公開元側で SaaS サービスを使用できるようになり、請求されます。

SaaS サブスクリプションが既にアクティブになっている場合、顧客は Azure portal または Microsoft 365 管理センターから **[SaaS エクスペリエンスの管理]** を選択することができます。 また、この操作により、アクティブ化フローの場合のように、Microsoft が *token* パラメーターを使用して **ランディングページの URL** を呼び出すことになります。 公開元は、新規購入と既存の SaaS アカウントの管理を区別し、このランディング ページの URL 呼び出しを適宜処理する必要があります。

### <a name="being-updated-subscribed"></a>更新中 (*Subscribed*)

このアクションは、既存のアクティブな SaaS サブスクリプションに対する更新が、Microsoft と公開元の両方で処理中であることを意味します。 このような更新は、次のユーザーによって開始することができます。

- コマーシャル マーケットプレースから顧客によって。
- コマーシャル マーケットプレースから CSP によって。
- 公開元の SaaS サイトから顧客によって (CSP が行った購入は対象外)。

SaaS サブスクリプションに対して 2 種類の更新ができます。

- 顧客がサブスクリプションに対して別のプランを選択した場合に、プランを更新します。
- 顧客がサブスクリプションの購入済みシート数を変更したときに、数量を更新します。

アクティブなサブスクリプションのみを更新できます。 サブスクリプションが更新されている間は、Microsoft 側ではアクティブな状態のままとなります。

#### <a name="update-initiated-from-the-commercial-marketplace"></a>コマーシャル マーケットプレースから開始された更新

このフローでは、顧客は Azure portal または Microsoft 365 管理センターからサブスクリプション プランまたはシート数を変更します。

1. 更新が入力された後、Microsoft によって、パートナー センターの _[技術的な構成]_ ページにある **[接続 Webhook]** フィールドに構成されている公開元の Webhook URL が、*action* およびその他の関連するパラメーターの適切な値を使用して呼び出されます。
1. 公開元側は SaaS サービスに対して必要な変更を行い、[Update Status of Operation API](pc-saas-fulfillment-operations-api.md#update-the-status-of-an-operation) を呼び出すことで完了時に Microsoft に通知します。
1. "*失敗*" の状態でパッチが送信されると、更新プロセスは Microsoft 側で完了しません。 SaaS サブスクリプションは、既存のプランとシート数のままになります。

> [!NOTE]
> 公開元は、Webhook 通知を受信した後 "*10 秒以内に*" PATCH を呼び出して、Failure または Success の応答で [Status of Operation API を更新する](pc-saas-fulfillment-operations-api.md#update-the-status-of-an-operation)必要があります。 操作状態の PATCH が 10 秒以内に受信されなかった場合、変更計画は *成功として自動的にパッチが適用* されます。

コマーシャル マーケットプレースから開始される更新シナリオ向けの一連の API 呼び出しを、次の図に示します。

![マーケットプレースで開始された更新の API 呼び出しを示す図。](./media/saas-update-status-api-v2-calls-marketplace-side.png)

#### <a name="update-initiated-from-the-publisher"></a>発行者から開始された更新

このフローでは、顧客が SaaS サービス自体から購入したサブスクリプション プランまたはシート数を変更します。 

1. 要求された変更を公開元側で行う前に、公開元のコードで [Change Plan API](pc-saas-fulfillment-subscription-api.md#change-the-plan-on-the-subscription) または [Change Quantity API](pc-saas-fulfillment-subscription-api.md#change-the-quantity-of-seats-on-the-saas-subscription) あるいはその両方を呼び出す必要があります。

1. Microsoft はサブスクリプションに変更を適用し、**接続 Webhook** を使用して、同じ変更を適用するように発行者に対して通知します。

1. その段階でようやく、公開元は SaaS サブスクリプションに対して必要な変更を行い、変更が完了したら [Update Status of Operation API](pc-saas-fulfillment-operations-api.md#update-the-status-of-an-operation) を呼び出して Microsoft に通知します。

公開元側から開始される更新シナリオ向けの一連の API 呼び出しを、次の図に示します。

![公開元側で開始された更新の API 呼び出しを示す図。](./media/saas-update-status-api-v2-calls-publisher-side.png)

### <a name="suspended-suspended"></a>中断 (*中断*)

この状態は、SaaS サービスに対する顧客の支払いが受け取られていないことを示します。 公開元には、SaaS サブスクリプションの状態のこの変更が Microsoft によって通知されます。 この通知は、*action* パラメーターを *Suspended* に設定した Webhook の呼び出しによって行われます。

公開元は、公開元側で SaaS サービスへの変更を行うことも、行わないこともあります。 公開元側で、中断された顧客に対してこの情報が提供されるようにし、SaaS サービスへの顧客のアクセスを制限またはブロックすることをお勧めします。 支払いが一切受け取られない可能性があります。

> [!NOTE]
> Microsoft は、サブスクリプションを自動的に取り消す前に、30 日間の猶予期間を顧客に提供します。 30 日間の猶予期間が過ぎた後、Webhook は `Unsubscribe` アクションを受け取ります。

サブスクリプションが *Suspended* 状態にある場合:

* パートナーまたは ISV は SaaS アカウントを復旧可能な状態にしておく必要があります。これにより、データや設定を失わずにすべての機能を復元できます。
* パートナーまたは ISV は、猶予期間中に支払いが発生した場合は、サブスクリプションの復帰要求を想定し、そうでなければ、猶予期間の終わりにこのサブスクリプションのプロビジョニング解除要求を想定してください。 両方の要求は、Webhook メカニズムを使用して送信されます。

発行者が何らかのアクションを実行する前に、サブスクリプションの状態が Microsoft 側で "中断" に変更されます。 アクティブなサブスクリプションのみを中断できます。

### <a name="reinstated-suspended"></a>復帰 (*中断*)

このアクションは、顧客の支払い方法が再び有効になり、SaaS サブスクリプションに対して支払いが行われており、サブスクリプションが復帰中であることを示します。 この場合、次のようになります。 

1. Microsoft によって、*action* パラメーターを *Reinstate* の値に設定した Webhook が呼び出されます。
1. 公開元で、このサブスクリプションを再び完全に運用可能にします。
1. 発行者は、成功の状態を持つ [Patch Operation API](pc-saas-fulfillment-operations-api.md#update-the-status-of-an-operation) を呼び出します。
1. 復帰プロセスが成功し、顧客に SaaS サブスクリプションの請求が再び行われます。 

パッチが "*失敗*" 状態で送信された場合、復帰プロセスは Microsoft 側では完了せず、サブスクリプションは *Suspended* のままになります。

中断されたサブスクリプションのみを復帰させることができます。 中断された SaaS サブスクリプションは、復帰中は *Suspended* 状態のままです。 この操作が完了すると、サブスクリプションの状態は "*アクティブ*" になります。

### <a name="renewed-subscribed"></a>更新済み (*サブスクライブ済み*)

SaaS サブスクリプションは、1 か月または 1 年間のサブスクリプション期間の終了時に Microsoft によって自動的に更新されます。 自動更新設定の既定値は、すべての SaaS サブスクリプションで *true* です。 アクティブな SaaS サブスクリプションは、定期的に更新され続けます。 Microsoft では、更新イベントに関する情報提供専用の Webhook 通知を提供しています。 顧客は、Microsoft 365 管理ポータル経由で、SaaS サブスクリプションの自動更新を無効にすることができます。 この場合、SaaS サブスクリプションは、現在の請求期間の終了時に自動的に取り消されます。 また、顧客はいつでも SaaS サブスクリプションを取り消すことができます。

アクティブなサブスクリプションのみが自動的に更新されます。 サブスクリプションは、更新プロセス中および自動更新が成功した場合はアクティブに維持されます。 更新後、サブスクリプション期間の開始日と終了日が新しい期間の日付に更新されます。

支払いの問題によって自動更新が失敗した場合、サブスクリプションは *Suspended* になり、公開元に通知されます。

### <a name="canceled-unsubscribed"></a>取り消し (*登録解除済み*)

サブスクリプションは、公開元サイト、Azure portal、または Microsoft 365 管理センターからサブスクリプションを取り消すという、顧客または CSP の明示的なアクションに呼応して、この状態になります。 サブスクリプションは、料金未払いの結果として 30 日間 *Suspended* 状態になった後、暗黙的に取り消すこともできます。

公開元は取り消しの Webhook 呼び出しを受け取ったら、要求に応じて復旧できるように顧客データを少なくとも 7 日間保持する必要があります。 その段階でようやく、顧客データを削除できます。

SaaS サブスクリプションは、ライフサイクルの任意の時点で取り消すことができます。 サブスクリプションは、取り消された後に再びアクティブにすることはできません。

## <a name="next-steps"></a>次の手順

- [SaaS Fulfillment サブスクリプション API v2](pc-saas-fulfillment-subscription-api.md)
- [SaaS Fulfillment 操作 API v2](pc-saas-fulfillment-operations-api.md)
