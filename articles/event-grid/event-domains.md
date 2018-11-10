---
title: Azure Event Grid のイベント ドメイン
description: イベント ドメインを使用して、Azure Event Grid のトピックを管理する方法について説明します。
services: event-grid
author: banisadr
ms.service: event-grid
ms.author: babanisa
ms.topic: conceptual
ms.date: 10/30/2018
ms.openlocfilehash: fe66ca8b8f5b4474290e302f73b35868dce68caa
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/31/2018
ms.locfileid: "50669329"
---
# <a name="understand-event-domains-for-managing-event-grid-topics"></a>Event Grid トピックを管理するためのイベント ドメインについて

この記事では、イベント ドメインを使用して、さまざまなビジネス組織、顧客、アプリケーションへのカスタム イベントのフローを管理する方法について説明します。 イベント ドメインを使用して、次のことを行います。

* 大規模なマルチテナントのイベント処理アーキテクチャを管理する。
* 承認と認証を管理する。
* 各トピックを個別に管理するのではなく、パーティション分割する。
* 各トピック エンドポイントへの個別の発行を回避する。

[!INCLUDE [event-grid-preview-feature-note.md](../../includes/event-grid-preview-feature-note.md)]


## <a name="event-domains-overview"></a>イベント ドメインの概要

イベント ドメインは、同じアプリケーションに関連する数多くの Event Grid トピック用の管理ツールです。 数千の個々のトピックを含めることができるメタ トピックと考えることができます。

イベント ドメインでは、Storage や IoT Hub などのアーキテクチャ Azure サービスを使用して、独自のシステムで利用可能なイベントを発行します。 数千のトピックにイベントを発行できます。 また、ドメインでは、テナントをパーティション分割できるように各トピックの承認と認証を制御できます。

### <a name="example-use-case"></a>ユース ケースの例

例を使えば、イベント ドメインを最も簡単に説明できます。 Contoso Construction Machinery という会社を経営しているとします。この会社はトラクター、掘削機器、その他の重機を製造しています。 経営の一部として、機器のメンテナンス、システムの正常性、契約の更新などについて顧客にリアルタイムの情報をプッシュします。この情報はすべて、アプリ、顧客のエンドポイント、顧客が設定したその他のインフラストラクチャを含むさまざまなエンドポイントに到達します。

イベント ドメインを使用することで、Contoso Construction Machinery を単一のイベント処理エンティティとしてモデル化することができます。 それぞれの顧客はドメイン内でトピックとして表されます。認証と承認は、Azure Active Directory を使用して処理されます。 各顧客はトピックをサブスクライブし、イベントを自分に配信させることができます。 イベント ドメインを介した管理アクセスにより、確実に自分のトピックにのみアクセスできるようになります。

また、すべての顧客イベントを発行できる、単一のエンドポイントが提供されます。 Event Grid では、各トピックにより、そのテナントをスコープとするイベントのみが認識されるようにします。

![Contoso Construction の例](./media/event-domains/contoso-construction-example.png)

## <a name="access-management"></a>アクセス管理

 ドメインでは、Azure のロールベースのアクセス確認 (RBAC) を使用して、各トピックの承認と認証を細かく制御することができます。 これらのロールを使用することで、アプリケーションの各テナントを、アクセスを許可するトピックにのみ制限することができます。

イベント ドメインのロール ベースのアクセス確認 (RBAC) は、その他の Event Grid と Azure での[管理アクセス制御](https://docs.microsoft.com/azure/event-grid/security-authentication#management-access-control)の動作と同じように動作します。 RBAC を使用して、イベント ドメインでカスタム ロール定義を作成して適用します。

### <a name="built-in-roles"></a>組み込みロール

Event Grid には、RBAC をより簡単にするための次の 2 つの組み込みロール定義があります。

#### <a name="eventgrid-eventsubscription-contributor-preview"></a>EventGrid EventSubscription 共同作成者 (プレビュー)

```json
[
  {
    "Description": "Lets you manage EventGrid event subscription operations.",
    "IsBuiltIn": true,
    "Id": "428e0ff05e574d9ca2212c70d0e0a443",
    "Name": "EventGrid EventSubscription Contributor (Preview)",
    "IsServiceRole": false,
    "Permissions": [
      {
        "Actions": [
          "Microsoft.Authorization/*/read",
          "Microsoft.EventGrid/eventSubscriptions/*",
          "Microsoft.EventGrid/topicTypes/eventSubscriptions/read",
          "Microsoft.EventGrid/locations/eventSubscriptions/read",
          "Microsoft.EventGrid/locations/topicTypes/eventSubscriptions/read",
          "Microsoft.Insights/alertRules/*",
          "Microsoft.Resources/deployments/*",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Support/*"
        ],
        "NotActions": [],
        "DataActions": [],
        "NotDataActions": [],
        "Condition": null
      }
    ],
    "Scopes": [
      "/"
    ]
  }
]
```

#### <a name="eventgrid-eventsubscription-reader-preview"></a>EventGrid EventSubscription 閲覧者 (プレビュー)

```json
[
  {
    "Description": "Lets you read EventGrid event subscriptions.",
    "IsBuiltIn": true,
    "Id": "2414bbcf64974faf8c65045460748405",
    "Name": "EventGrid EventSubscription Reader (Preview)",
    "IsServiceRole": false,
    "Permissions": [
      {
        "Actions": [
          "Microsoft.Authorization/*/read",
          "Microsoft.EventGrid/eventSubscriptions/read",
          "Microsoft.EventGrid/topicTypes/eventSubscriptions/read",
          "Microsoft.EventGrid/locations/eventSubscriptions/read",
          "Microsoft.EventGrid/locations/topicTypes/eventSubscriptions/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read"
        ],
        "NotActions": [],
        "DataActions": [],
        "NotDataActions": []
       }
    ],
    "Scopes": [
      "/"
    ]
  }
]
```

## <a name="subscribing-to-topics"></a>トピックのサブスクライブ

イベント ドメイン内のトピックのイベントをサブスクライブすることは、[カスタム トピックのイベント サブスクリプションを作成](./custom-event-quickstart.md)したり、Azure で提供される組み込みのイベント発行元のいずれかをサブスクライブするのと同じです。

### <a name="domain-scope-subscriptions"></a>ドメイン スコープ サブスクリプション

イベント ドメインでは、ドメイン スコープ サブスクリプションも許可されます。 イベント ドメインのイベント サブスクリプションでは、イベントの送信先となるトピックに関係なく、ドメインに送信されるすべてのイベントが受信されます。 ドメイン スコープ サブスクリプションは、管理や監査の目的で役立ちます。

## <a name="publishing-to-an-event-domain"></a>イベント ドメインへの発行

イベント ドメインを作成するときに、Event Grid でトピックを作成した場合と同じような発行エンドポイントが与えられます。 

イベント ドメインの任意のトピックにイベントを発行するには、[カスタム トピックの場合と同じように](./post-to-custom-topic.md)、ドメインのエンドポイントにイベントをプッシュします。 唯一の違いは、イベントの配信先となるトピックを指定する必要があることです。

たとえば、次のイベントの配列を発行すると、`"id": "1111"` のイベントがトピック `foo` に送信され、`"id": "2222"` のイベントはトピック `bar` に送信されます。

```json
[{
  "topic": "foo",
  "id": "1111",
  "eventType": "maintenanceRequested",
  "subject": "myapp/vehicles/diggers",
  "eventTime": "2018-10-30T21:03:07+00:00",
  "data": {
    "make": "Contoso",
    "model": "Small Digger"
  },
  "dataVersion": "1.0"
},
{
  "topic": "bar",
  "id": "2222",
  "eventType": "maintenanceCompleted",
  "subject": "myapp/vehicles/tractors",
  "eventTime": "2018-10-30T21:04:12+00:00",
  "data": {
    "make": "Contoso",
    "model": "Big Tractor"
  },
  "dataVersion": "1.0"
}]
```

イベント ドメインにより、トピックへの発行が自動的に処理されます。 個々に管理する各トピックにイベントを発行するのではなく、すべてのイベントをドメインのエンドポイントに発行することができ、Event Grid では各イベントが確実に正しいトピックに送信されるようにします。

## <a name="limits-and-quotas"></a>制限とクォータ

### <a name="control-plane"></a>コントロール プレーン

プレビュー期間中、イベント ドメインはドメイン内の 1,000 個のトピックに制限され、ドメイン内のトピックごとに 50 個のイベント サブスクリプションに制限されます。 イベント ドメイン スコープ サブスクリプションも 50 に制限されます。

### <a name="data-plane"></a>データ プレーン

プレビュー期間中、イベント ドメインのイベント スループットは、カスタム トピックが制限されるのと同じ 5,000 イベント/秒の取り込み率に制限されます。

## <a name="pricing"></a>価格

プレビュー期間中、イベント ドメインでは、Event Grid の他のすべての機能で使用されるのと同じ[操作価格](https://azure.microsoft.com/pricing/details/event-grid/)が使用されます。

イベント ドメインでの操作は、カスタム トピックの場合と同じように動作します。 イベント ドメインへのイベントの各取り込みは 1 回の操作であり、イベントの各配信試行も 1 回の操作となります。

## <a name="next-steps"></a>次の手順

* イベント ドメインの設定、トピックの作成、イベント サブスクリプションの作成、イベントの発行の詳細については、[イベント ドメインの管理](./how-to-event-domains.md)に関するページを参照してください。