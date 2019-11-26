---
title: チュートリアル:Azure Event Grid での Key Vault の監視
description: チュートリアル:Azure Event Grid を使用して Key Vault のイベントをサブスクライブする
services: media-services
author: msmbaldwin
manager: rkarlin
ms.service: key-vault
ms.topic: tutorial
ms.date: 11/12/2019
ms.author: mbaldwin
ms.openlocfilehash: 5771af365b763d2152eea4ef4f662e08769b378c
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2019
ms.locfileid: "74133352"
---
# <a name="tutorial-monitoring-key-vault-with-azure-event-grid-preview"></a>チュートリアル:Azure Event Grid での Key Vault の監視 (プレビュー)

Key Vault と Event Grid の統合は現在プレビュー段階です。 それにより、キー コンテナーに格納されているシークレットの状態が変更されたときにユーザーに通知することができます。 状態の変更は、有効期限が切れる直前 (30 日以内に期限切れ) のシークレット、有効期限が切れたシークレット、または新しいバージョンが利用可能になったシークレットとして定義されます。 3 つすべての種類のシークレット (キー、証明書、シークレット) の通知がサポートされています。

アプリケーションでは、最新のサーバーレス アーキテクチャを使って、これらのイベントに対応できます。複雑なコードや、高価で非効率的なポーリング サービスは必要ありません。 イベントが [Azure Event Grid](https://azure.microsoft.com/services/event-grid/) を通してイベント ハンドラー ([Azure Functions](https://azure.microsoft.com/services/functions/)、[Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/) など) やユーザー独自の Webhook へのイベントにもプッシュされるため、支払いは使用した分だけで済みます。 価格の詳細については、[Event Grid の価格](https://azure.microsoft.com/pricing/details/event-grid/)に関する記事をご覧ください。

## <a name="key-vault-events-and-schemas"></a>Key Vault のイベントとスキーマ

Event Grid は、[イベント サブスクリプション](../event-grid/concepts.md#event-subscriptions)を使って、イベント メッセージをサブスクライバーにルーティングします。 Key Vault イベントには、データの変更に対応するために必要なすべての情報が含まれます。 Key Vault イベントは、eventType プロパティが "Microsoft.KeyVault" で始まっているので識別できます。

詳細については、 [Key Vault イベント スキーマ](../event-grid/event-schema-key-vault.md)に関するページを参照してください。

> [!NOTE]
> イベントは、サブスクリプションの設定後に作成されたシークレット バージョン (3 種類すべて) に対してのみトリガーされます。
>
> 既存のシークレットについては、新しいバージョンを生成する必要があります。

## <a name="practices-for-consuming-events"></a>イベントの使用に関する手法

Key Vault イベントを処理するアプリケーションは、いくつかの推奨される手法に従う必要があります。

* 同じイベント ハンドラーにイベントをルーティングするように、複数のサブスクリプションを構成できます。 イベントが特定のソースからのものであると思い込まず、メッセージのトピックを調べて、想定しているキー コンテナーからのものであることを確認することが重要です。
* 同様に、受信するすべてのイベントが予期した種類のものであると想定してはならず、イベントの種類が処理できるものであることを確認する必要があります。
* わからないフィールドは無視します。  この手法に従うと、将来追加されるかもしれない新しい機能に弾力的に対応できます。
* イベントを特定のイベントに制限するには、"subject" プレフィックスとサフィックスの一致を使用します。

## <a name="next-steps"></a>次の手順

- [Azure Key Vault の概要](key-vault-overview.md)
- [Azure Event Grid の概要](../event-grid/overview.md)
- 方法:[Automation Runbook への Key Vault イベントのルーティング (プレビュー)](event-grid-tutorial.md)
- 方法:[キー コンテナーのシークレットが変更されたときにメールを受信する](event-grid-logicapps.md)
- [Azure Key Vault 用の Azure Event Grid イベント スキーマ (プレビュー)](../event-grid/event-schema-key-vault.md)
- [Azure Automation の概要](../automation/index.yml)
