---
title: "価格と課金 - Azure Logic Apps | Microsoft Docs"
description: "Azure Logic Apps の価格と課金のしくみについて説明します。"
author: kevinlam1
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: f8f528f5-51c5-4006-b571-54ef74532f32
ms.service: logic-apps
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/11/2017
ms.author: LADocs; klam
ms.openlocfilehash: f2a92e45b8a759d2d8193ac188efdcfc694a3e6d
ms.sourcegitcommit: e38120a5575ed35ebe7dccd4daf8d5673534626c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/13/2017
---
# <a name="logic-apps-pricing-model"></a>Logic Apps の料金モデル
Azure Logic Apps を使用して、自動化されたスケーラブルな統合ワークフローをクラウドに作成して実行できます。 ここでは、Logic Apps の課金と価格のしくみの詳細を示します。
## <a name="consumption-pricing-model"></a>従量課金モデル
新しく作成したロジック アプリでは、使用した分だけ支払います。 新しいロジック アプリでは、従量課金プランと課金モデルが使用されます。これは、ロジック アプリ インスタンスによるすべての実行が課金対象になることを意味します。
### <a name="what-are-action-executions"></a>アクションの実行とは
ロジック アプリ定義内のすべてのステップがアクションになります。この中には、トリガー、制御フローの各ステップ、組み込まれているアクションの呼び出し、およびコネクタの呼び出しが含まれます。
### <a name="triggers"></a>トリガー
トリガーは、特定のイベントが発生したときに、ロジック アプリ インスタンスを作成する特別なアクションです。 トリガーには複数の動作があり、それによってロジック アプリの課金方法が変わります。
* **ポーリング トリガー** – このトリガーは、ワークフローを開始するロジック アプリ インスタンスを作成するための基準を満たすメッセージを取得するまで、継続的にエンドポイントをチェックします。 ロジック アプリ デザイナーを使用して、ポーリング間隔をトリガー内に設定できます。 ロジック アプリ インスタンスが作成されない場合でも、ポーリング要求は実行として数えられます。
* **Webhook トリガー** – このトリガーは、クライアントが特定のエンドポイントに要求を送信するまで待機します。 Webhook のエンドポイントに送信された個々の要求がアクションの実行としてカウントされます。 たとえば、要求 Webhook トリガーと HTTP Webhook トリガーはどちらも Webhook トリガーです。
* **繰り返しトリガー** – このトリガーは、トリガー内に設定された繰り返し間隔に基づいてロジック アプリ インスタンスを作成します。 たとえば、3 日ごとに実行されるトリガーや、より複雑なスケジュールで実行されるトリガーを設定できます。

トリガーの実行は、ロジック アプリの [概要] ウィンドウの [トリガーの履歴] セクションで確認できます。

### <a name="actions"></a>アクション
組み込まれているアクション (HTTP、Azure Functions、API Management の呼び出しなどのアクションや制御フローのステップなど) は、ネイティブ アクションとして課金され、それぞれに該当する種類があります。 [コネクタ](https://docs.microsoft.com/connectors)を呼び出すアクションの種類は "ApiConnection" です。 コネクタは、標準コネクタまたはエンタープライズ コネクタのいずれかに分類され、該当する[価格][pricing]で課金されます。
成功した実行と失敗した実行のすべてがカウントされ、アクション実行として課金されます。 ただし、条件が満たされなかったためにスキップされたアクションや、ロジック アプリが完了前に強制終了されたために実行されなかったアクションは、アクション実行としてカウントされません。 無効なロジック アプリは新しいインスタンスをインスタンス化できないため、無効になっている間は課金されることはありません。

> [!NOTE]
> ロジック アプリを無効にした場合、現在実行中のインスタンスが完全に停止するまで少し時間がかかることがあります。

ループ内で実行されるアクションは、ループ内での各サイクルごとにカウントされます。 たとえば、10 項目で構成されるリストを処理する "for each" ループのアクションは、リストの項目数 (10) にループのアクション数 (1) を乗算し、ループを開始するアクション (1) を加算したものになります。 したがって、この例では、計算は (10 * 1) + 1 になり、結果は 11 のアクション実行になります。

### <a name="integration-account-usage"></a>統合アカウントの使用
従量制のプランには、Logic Apps の [B2B/EDI](logic-apps-enterprise-integration-b2b.md) 機能と [XML 処理](logic-apps-enterprise-integration-xml.md)機能を追加コストなしで調査、開発、およびテストできる[統合アカウント](logic-apps-enterprise-integration-create-integration-account.md)が含まれています。 リージョンごとにいずれかの統合アカウントを所有し、最大 10 件の契約と 25 のマップを格納できます。 所有してアップロードできるパートナー、スキーマ、および証明書の数に制限はありません。

Logic Apps は、Logic Apps SLA によってサポートされている基本統合アカウントと標準統合アカウントも提供します。 メッセージの処理のみを使用する場合、または大規模なビジネス エンティティと取引パートナー関係がある小規模なビジネス パートナーとして機能する場合は、基本統合アカウントを使用できます。 標準統合アカウントは、より複雑な B2B 関係をサポートし、管理できるエンティティの数が大きくなります。 詳しくは、「[Logic Apps の価格](https://azure.microsoft.com/pricing/details/logic-apps)」をご覧ください。

## <a name="pricing"></a>価格
詳細については、「[Logic Apps の価格](https://azure.microsoft.com/pricing/details/logic-apps)」を参照してください。

## <a name="next-steps"></a>次のステップ
* [Logic Apps の概要][whatis]
* [初めてのロジック アプリの作成][create]

[pricing]: https://azure.microsoft.com/pricing/details/logic-apps/
[whatis]: logic-apps-what-are-logic-apps.md
[create]: logic-apps-create-a-logic-app.md

