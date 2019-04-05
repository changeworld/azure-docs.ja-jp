---
title: 価格と課金 - Azure Logic Apps | Microsoft Docs
description: Azure Logic Apps の価格と課金のしくみについて説明します
services: logic-apps
ms.service: logic-apps
ms.suite: logic-apps
author: kevinlam1
ms.author: klam
ms.reviewer: estfan, LADocs
manager: carmonm
ms.assetid: f8f528f5-51c5-4006-b571-54ef74532f32
ms.topic: article
ms.date: 02/26/2019
ms.openlocfilehash: 9b5452f112c6325dafd5edbe693b90ec2a94abc0
ms.sourcegitcommit: f7f4b83996640d6fa35aea889dbf9073ba4422f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/28/2019
ms.locfileid: "56990239"
---
# <a name="pricing-model-for-azure-logic-apps"></a>Azure Logic Apps の価格モデル

Azure Logic Apps を使用するときは、スケーリング可能な自動化された統合ワークフローをクラウドに作成して実行できます。 ここでは、Logic Apps の課金と価格のしくみの詳細を示します。 

<a name="consumption-pricing"></a>

## <a name="consumption-pricing-model"></a>従量課金モデル

パブリックまたは "グローバル" な Logic Apps サービスで実行する新しいロジック アプリの場合は、使用分についてのみ課金されます。 これらのロジック アプリでは、従量制のプランと価格モデルが使用されます。 ロジック アプリの定義では、各ステップがアクションです。 アクションには、トリガー、制御フローのステップ、組み込みアクション、コネクタの呼び出しが含まれます。 Logic Apps では、ロジック アプリ内で実行されるすべてのアクションが課金されます。  
詳細については、「[Logic Apps の価格](https://azure.microsoft.com/pricing/details/logic-apps)」を参照してください。

<a name="fixed-pricing"></a>

## <a name="fixed-pricing-model"></a>固定価格モデル

"[*統合サービス環境*" (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) 内で実行される新しいロジック アプリの場合、組み込みアクションと標準コネクタについて固定の月額料金を支払います。 ISE では、Azure 仮想ネットワーク内のリソースにアクセスできる分離されたロジック アプリを作成して実行するための手段が提供されます。 

ISE ベース ユニットの容量は固定されているため、さらにスループットが必要な場合は、作成中または作成後に[スケール ユニットを追加](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#add-capacity)できます。 ISE には無料のエンタープライズ コネクタが 1 つ含まれ、それには必要なだけいくつでも接続を含めることができます。 追加のエンタープライズ コネクタを使用すると、エンタープライズ使用料金に基づいて課金されます。 

> [!NOTE]
> ISE は、["*パブリック プレビュー*"](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) 段階にあります。 詳細については、「[Logic Apps の価格](https://azure.microsoft.com/pricing/details/logic-apps)」を参照してください。

<a name="triggers"></a>

## <a name="triggers"></a>トリガー

トリガーは、特定のイベントが発生したときに、ロジック アプリ インスタンスを作成する特別なアクションです。 トリガーにはさまざまな動作があり、それによってロジック アプリの課金方法が変わります。

* **ポーリング トリガー** – このトリガーは、ロジック アプリ インスタンスを作成しワークフローを開始するための基準を満たすメッセージの有無について、継続的にエンドポイントをチェックします。 ロジック アプリ インスタンスが作成されていない場合でも、Logic Apps は各ポーリング要求を実行として課金します。 ポーリング間隔を指定するには、ロジック アプリ デザイナーを使用してトリガーを設定します。

  [!INCLUDE [logic-apps-polling-trigger-non-standard-metering](../../includes/logic-apps-polling-trigger-non-standard-metering.md)]

* **Webhook トリガー** – このトリガーは、クライアントが特定のエンドポイントに要求を送信するまで待機します。 Webhook のエンドポイントに送信された個々の要求がアクションの実行としてカウントされます。 たとえば、要求 Webhook トリガーと HTTP Webhook トリガーはどちらも Webhook トリガーです。

* **繰り返しトリガー** – このトリガーは、トリガー内に設定された繰り返し間隔に基づいてロジック アプリ インスタンスを作成します。 たとえば、3 日ごとに実行されるトリガーや、より複雑なスケジュールで実行されるトリガーを設定できます。

## <a name="actions"></a>Actions

Logic Apps では、組み込みアクションはネイティブ アクションとして課金されます。 たとえば、組み込みアクションには、HTTP 経由の呼び出し、Azure Functions または API Management からの呼び出し、およびループや条件などの制御フローのステップが含まれます 
- それぞれに固有のアクションの種類があります。 [コネクタ](https://docs.microsoft.com/connectors)を呼び出すアクションの種類は "ApiConnection" です。 これらのコネクタは、標準コネクタまたはエンタープライズ コネクタのいずれかに分類され、該当する[価格][pricing]に基づいて課金されます。 エンタープライズ コネクタは "*プレビュー*" 段階であり、標準コネクタとして課金されます。

Logic Apps では、成功と失敗のすべての実行アクションが、アクション実行として課金されます。 Logic Apps では、次のアクションは課金されません。 

* 条件が満たされていないためスキップされたアクション
* 終了前にロジック アプリが停止したために実行されなかったアクション

無効にされたロジック アプリは、新しいインスタンスを作成できないため、無効になっている間は課金されません。

> [!NOTE]
> ロジック アプリを無効にした場合、現在実行中のインスタンスが完全に停止するまで少し時間がかかることがあります。

ループ内で実行されるアクションについては、ループ内のサイクルごとに各アクションがカウントされます。 たとえば、リストを処理する "for each" ループがあるものとします。 この場合は、リスト項目の数にループ内のアクションの数を乗算したものに、ループを開始するアクションを追加した合計のアクション数に対して課金されます。 10 項目のリストの場合の計算は (10 * 1) + 1 になり、結果は 11 個のアクション実行になります。

## <a name="integration-account-usage"></a>統合アカウントの使用

従量制の使用量は、Logic Apps の [B2B/EDI](logic-apps-enterprise-integration-b2b.md) 機能と [XML 処理](logic-apps-enterprise-integration-xml.md)機能を追加コストなしで調査、開発、およびテストできる[統合アカウント](logic-apps-enterprise-integration-create-integration-account.md)に適用されます。 リージョンごとに 1 つの統合アカウントを使用できます。 各統合アカウントでは、特定の[数まで成果物](../logic-apps/logic-apps-limits-and-config.md) (取引先、契約、マップ、スキーマ、アセンブリ、証明書、バッチ構成など) を格納できます。

Logic Apps は、Logic Apps SLA によってサポートされている基本統合アカウントと標準統合アカウントも提供します。 メッセージの処理のみを使用する場合、または大規模なビジネス エンティティと取引パートナー関係がある小規模なビジネス パートナーとして機能する場合は、基本統合アカウントを使用できます。 標準統合アカウントは、より複雑な B2B 関係をサポートし、管理できるエンティティの数が大きくなります。 詳しくは、「[Logic Apps の価格](https://azure.microsoft.com/pricing/details/logic-apps)」をご覧ください。

## <a name="next-steps"></a>次の手順

* [Logic Apps の詳細を見る][whatis]
* [初めてのロジック アプリの作成][create]

[pricing]: https://azure.microsoft.com/pricing/details/logic-apps/
[whatis]: logic-apps-overview.md
[create]: quickstart-create-first-logic-app-workflow.md

