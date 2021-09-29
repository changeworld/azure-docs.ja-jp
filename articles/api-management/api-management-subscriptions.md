---
title: Azure API Management のサブスクリプション | Microsoft Docs
description: Azure API Management のサブスクリプションの概念について説明します。 コンシューマーは、Azure API Management のサブスクリプションを使用して API にアクセスします。
services: api-management
documentationcenter: ''
author: dlepow
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/27/2021
ms.author: danlep
ms.openlocfilehash: acf1db006dac4dd67937bf1d0788a219b6c482e9
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128637933"
---
# <a name="subscriptions-in-azure-api-management"></a>Azure API Management のサブスクリプション

Azure API Management でのサブスクリプションは、API コンシューマーが API Management インスタンスを通じて公開された API にアクセスするための最も一般的な方法です。 この記事では、この概念の概要について説明します。

## <a name="what-are-subscriptions"></a>サブスクリプションとは

API Management を通じて API を公開することで、サブスクリプション キーを用いて API アクセスを簡単に保護できます。 発行された API を使用するには、それらの API を呼び出す際に、HTTP 要求に有効なサブスクリプション キーを含める必要があります。 有効なサブスクリプション キーがない場合、呼び出しは次を実行します。
* API Management ゲートウェイによって直ちに拒否されます。 
* バックエンド サービスに転送されません。

API にアクセスするには、サブスクリプションとサブスクリプション キーが必要です。 *サブスクリプション* は、基本的にサブスクリプション キーのペアの名前付きコンテナーです。 

キーの定期的な再生成は一般的なセキュリティ上の予防策です。そのため、サブスクリプション キーを必要とするほとんどの Azure 製品では、キーがペアで生成されます。 サービスを利用する各アプリケーションは、*キー A* から *キー B* に切り替え、最小限の中断で鍵 A を再生成できます。その逆も同様です。 

さらに、次の点も考慮する必要があります。

* 開発者は、API 発行元の承認なしにサブスクリプションを取得できます。 
* API の公開元は、API コンシューマー用のサブスクリプションを直接作成することもできます。

> [!TIP]
> API Management では、API へのアクセスをセキュリティで保護するための他のメカニズムもサポートしています。たとえば、以下があります。
> - [OAuth2.0](api-management-howto-protect-backend-with-aad.md)
> - [クライアント証明書](api-management-howto-mutual-certificates-for-clients.md)
> - [呼び出し元 IP を制限する](./api-management-access-restriction-policies.md#RestrictCallerIPs)

## <a name="scope-of-subscriptions"></a>サブスクリプションの範囲

サブスクリプションは、製品、すべての API、または個々の API というさまざまな範囲に関連付けることができます。

### <a name="subscriptions-for-a-product"></a>製品のサブスクリプション

従来、API Management のサブスクリプションは、常に 1 つの [API 製品](api-management-terminology.md)のスコープに関連付けられていました。 開発者 : 
* 開発者ポータルで製品の一覧を確認していました。 
* 使用する製品のサブスクリプション要求を送信します。 
* これらのサブスクリプションのキー (自動的に承認または API 発行元によって承認) を使用して、製品内のすべての API にアクセスします。 
    * サブスクリプション のスコープ (製品、グローバル、または API) に関係なく、サブスクリプション キーを使用または使用せずに API にアクセスできます。

現時点では、製品をスコープとするサブスクリプションは、開発者ポータルの **ユーザー プロファイル セクション** にのみ表示されます。 

> [!NOTE]
> API スコープのサブスクリプション キーを使用している場合、製品スコープで構成されている *ポリシー* は、そのサブスクリプションには適用されません。

![製品のサブスクリプション](./media/api-management-subscriptions/product-subscription.png)

> [!TIP]
> シナリオによっては、API の公開元は、サブスクリプションを必要としない API 製品を公開することがあります。 Azure portal の製品の **[設定]** ページで **[サブスクリプションを要求する]** オプションをオフにすることができます。 その結果、API キーを使用せずに製品のすべての API にアクセスできるようになります。

### <a name="subscriptions-for-all-apis-or-an-individual-api"></a>すべての API または個々の API のサブスクリプション

API Management の[従量課金](https://aka.ms/apimconsumptionblog)層を追加することで、サブスクリプション キーの管理がより合理的になります。 

#### <a name="two-more-subscription-scopes"></a>さらに 2 つのサブスクリプション スコープ

サブスクリプション スコープが API 製品に限定されなくなったので、次のいずれかのアクセス権を付与するキーを作成できます。
* 1 つの API、または 
* API Management インスタンス内のすべての API。 

API を追加する前に、製品を作成する必要はありません。 

さらに、各 API Management インスタンスには、不変の全 API サブスクリプションが付属するようになりました。 このサブスクリプションにより、テスト コンソール内での API のテストとデバッグがより簡単に、わかりやすくなりました。

#### <a name="standalone-subscriptions"></a>スタンドアロン サブスクリプション

API Management で、*スタンドアロン* のサブスクリプションが許可されるようになりました。 サブスクリプションを開発者アカウントに関連付ける必要がなくなりました。 この機能は、サブスクリプションを共有する複数の開発者やチームと同様のシナリオで役立ちます。

所有者を割り当てずにサブスクリプションを作成すると、サブスクリプションはスタンドアロン サブスクリプションになります。 開発者とチームの残りの部分にスタンドアロン サブスクリプション キーへのアクセスを許可するには、次のいずれかを実行します。
* サブスクリプション キーを手動で共有します。
* カスタム システムを使用して、サブスクリプション キーをチームで使用できます。

#### <a name="creating-subscriptions-in-azure-portal"></a>Azure portal でのサブスクリプションの作成

API の公開元は、Azure portal で直接[サブスクリプションを作成](api-management-howto-create-subscriptions.md)できるようになりました。

![柔軟なサブスクリプション](./media/api-management-subscriptions/flexible-subscription.png)

## <a name="next-steps"></a>次の手順
API Management の詳細情報:

+ API Management の他の[概念](api-management-terminology.md)を確認します。
+ [チュートリアル](import-and-publish.md)に従って API Management の詳細を確認します。
+ [FAQ ページ](api-management-faq.yml)で一般的な質問を確認します。
