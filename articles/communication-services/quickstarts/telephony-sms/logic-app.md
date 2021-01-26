---
title: クイックスタート - Azure Communication Services を使用して Azure Logic Apps で携帯ショートメール (SMS) メッセージを送信する
titleSuffix: An Azure Communication Services quickstart
description: このクイックスタートでは、Azure Logic Apps ワークフローで、Azure Communication Services コネクタを使用して携帯ショートメール (SMS) メッセージを送信する方法について説明します。
author: tophpalmer
manager: anvalent
services: azure-communication-services
ms.author: chpalm
ms.date: 10/06/2020
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: ed9af178c10d033806b05f73372a0642bb28cab4
ms.sourcegitcommit: 23aa0cf152b8f04a294c3fca56f7ae3ba562d272
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/07/2020
ms.locfileid: "91801659"
---
# <a name="quickstart-send-sms-messages-in-azure-logic-apps-with-azure-communication-services"></a>クイックスタート: Azure Communication Services を使用して Azure Logic Apps で携帯ショートメール (SMS) メッセージを送信する

[Azure Communication Services SMS](../../overview.md) コネクタと [Azure Logic Apps](../../../logic-apps/logic-apps-overview.md) を使用すると、携帯ショートメール (SMS) メッセージを送信できる自動化されたワークフロー ("*ロジック アプリ*") を作成できます。 このクイックスタートでは、ロジック アプリ ワークフローの最初のステップであるトリガー イベントに反応する形で自動的に携帯ショートメール (SMS) を送信する方法を説明します。 トリガー イベントは、受信電子メール メッセージ、定期実行スケジュール、[Azure Event Grid](../../../event-grid/overview.md) リソース イベントなど、[Azure Logic Apps でサポートされるトリガー](/connectors/connector-reference/connector-reference-logicapps-connectors)であれば何でもかまいません。

:::image type="content" source="./media/logic-app/azure-communication-services-connector.png" alt-text="Azure portal をロジック アプリ デザイナーから開いた画面のスクリーンショット。Azure Communication Services コネクタの携帯ショートメール (SMS) 送信アクションを使用するロジック アプリの例。":::

このクイックスタートでは、コネクタを使用してトリガーに応答する方法を中心に説明していますが、コネクタを使用して他のアクション (ワークフローの中でトリガーに続くステップ) に応答することもできます。 Logic Apps を初めて使用する方は、最初に [Azure Logic Apps の概要](../../../logic-apps/logic-apps-overview.md)に関するページをご覧ください。

> [!NOTE]
> このクイックスタートを完了すると、ご利用の Azure アカウントでわずかな (数セント未満の) コストが発生します。

## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションを含む Azure アカウント。または、[アカウントを無料で作成](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)してください。

- アクティブな Azure Communication Services リソース。または、[Communication Services リソース](../create-communication-resource.md)を作成してください。

- アクティブな Logic Apps リソース (ロジック アプリ)。または、[空のロジック アプリを作成して、使用するトリガーを追加](../../../logic-apps/quickstart-create-first-logic-app-workflow.md)してください。 現在、Azure Communication Services の SMS コネクタで提供されるのはアクションのみです。そのためロジック アプリには、最低でもトリガーが必要となります。

  このクイックスタートでは、[Office 365 Outlook コネクタ](/connectors/office365/)で提供される、"**新しい電子メールが届いたとき**" というトリガーを使用しています。

- SMS 対応の電話番号。または、[電話番号を取得](./get-phone-number.md)してください。

## <a name="add-an-sms-action"></a>SMS アクションを追加する

Azure Communication Services SMS コネクタを使用して、ワークフローに新しいステップとして**携帯ショートメール (SMS) 送信**アクションを追加するには、[Azure portal](https://portal.azure.com) で次の手順に従います。その際、ロジック アプリ デザイナーでロジック アプリ ワークフローを開いておいてください。

1. デザイナーで、新しいアクションの追加先となるステップで **[新しいステップ]** を選択します。 ステップとステップの間に新しいアクションを追加するには、それらのステップ間の矢印にポインターを合わせて、正符号 ( **+** ) を選択し、 **[アクションの追加]** を選択します。

1. **[操作の選択]** 検索ボックスに「`Azure Communication Services`」と入力します。 アクションの一覧から **[Send SMS]\(携帯ショートメール (SMS) 送信\)** を選択します。

   :::image type="content" source="./media/logic-app/select-send-sms-action.png" alt-text="Azure portal をロジック アプリ デザイナーから開いた画面のスクリーンショット。Azure Communication Services コネクタの携帯ショートメール (SMS) 送信アクションを使用するロジック アプリの例。":::

1. Communication Services リソースとの接続を作成します。

   1. 接続の名前を入力します。

   1. Azure Communication Services リソースを選択します。

   1. **［作成］** を選択します

   :::image type="content" source="./media/logic-app/send-sms-configuration.png" alt-text="Azure portal をロジック アプリ デザイナーから開いた画面のスクリーンショット。Azure Communication Services コネクタの携帯ショートメール (SMS) 送信アクションを使用するロジック アプリの例。":::

1. **携帯ショートメール (SMS) 送信**アクションで、次の情報を入力します。 

   * 送信元と送信先の電話番号。 テスト用途では、送信先電話番号としてご自身の電話番号を使用してください。

   * 送信するメッセージの内容。たとえば「Hello from Logic Apps!」と入力します。

   **携帯ショートメール (SMS) 送信**アクションの情報の入力例を次に示します。

   :::image type="content" source="./media/logic-app/send-sms-action.png" alt-text="Azure portal をロジック アプリ デザイナーから開いた画面のスクリーンショット。Azure Communication Services コネクタの携帯ショートメール (SMS) 送信アクションを使用するロジック アプリの例。":::

1. 操作が完了したら、デザイナーのツールバーで、 **[保存]** を選択します。

次に、テストのためにロジック アプリを実行します。

## <a name="test-your-logic-app"></a>ロジック アプリをテストする

ロジック アプリを手動で開始するには、デザイナーのツール バーの **[実行]** を選択します。 または、ロジック アプリがトリガーされるまで待ってください。 どちらの場合も、指定した送信先電話番号に、ロジック アプリから携帯ショートメール (SMS) メッセージが送信されます。 ロジック アプリの実行について詳しくは、[ロジック アプリの実行方法](../../../logic-apps/quickstart-create-first-logic-app-workflow.md#run-your-logic-app)に関するセクションを確認してください。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

Communication Services サブスクリプションを削除するには、Communication Services リソースまたはリソース グループを削除します。 リソース グループを削除すると、そのグループに含まれている他のリソースもすべて削除されます。 詳細については、[Communication Services リソースのクリーンアップ方法](../create-communication-resource.md#clean-up-resources)に関するセクションを確認してください。

ロジック アプリのワークフローおよび関連するリソースのクリーンアップについては、[Logic Apps リソースのクリーンアップ方法](../../../logic-apps/quickstart-create-first-logic-app-workflow.md#clean-up-resources)に関するセクションを参照してください。

## <a name="next-steps"></a>次の手順

このクイックスタートでは、Azure Logic Apps と Azure Communication Services を使用して携帯ショートメール (SMS) メッセージを送信する方法について説明しました。 引き続き、SMS イベントのサブスクライブについて見ていきましょう。

> [!div class="nextstepaction"]
> [SMS イベントをサブスクライブする](./handle-sms-events.md)

Azure Communication Services の SMS について詳しくは、次の記事を参照してください。

- [SMS に関する概念](../../concepts/telephony-sms/concepts.md)
- [テレフォニーと SMS ソリューションの計画](../../concepts/telephony-sms/plan-solution.md)
- [SMS SDK](../../concepts/telephony-sms/sdk-features.md)
