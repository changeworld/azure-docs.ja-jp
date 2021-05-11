---
title: Azure Logic Apps から SharePoint に接続する
description: Azure Logic Apps を使用して、SharePoint Online またはオンプレミスの SharePoint Server のリソースを監視および管理します
services: logic-apps
ms.suite: integration
ms.reviewer: logicappspm
ms.topic: article
ms.date: 04/27/2021
tags: connectors
ms.openlocfilehash: 750253d5607262614cf8576c376b261616361266
ms.sourcegitcommit: fc9fd6e72297de6e87c9cf0d58edd632a8fb2552
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/30/2021
ms.locfileid: "108285450"
---
# <a name="connect-to-sharepoint-resources-with-azure-logic-apps"></a>Azure Logic Apps を使用して SharePoint リソースに接続する

SharePoint Online やオンプレミスの SharePoint Server で、ファイル、フォルダー、リスト、項目などのリソースを監視および管理するタスクを自動化するために、Azure Logic Apps と SharePoint コネクタを使用して、自動化された統合ワークフローを作成することができます。

次の一覧は、自動化できるタスクの例を示したものです。

* ファイルや項目が作成、変更、または削除されるときを監視する。
* 項目を作成、取得、更新、または削除する。
* 添付ファイルを追加、取得、または削除する。 添付ファイルからコンテンツを取得する。
* ファイルを作成、コピー、更新、または削除する。 
* ファイルのプロパティを更新する。 ファイルのコンテンツ、メタデータ、またはプロパティを取得する。
* フォルダーを一覧表示または抽出する。
* リストまたはリスト ビューを取得する。
* コンテンツの承認状態を設定する。
* 人を解決する。
* SharePoint に HTTP 要求を送信する。
* エンティティの値を取得する。

ロジック アプリ ワークフローでは、SharePoint でイベントを監視するトリガーを使用して、出力を他のアクションで使用できるようにすることができます。 その後、アクションを使用して、SharePoint でさまざまなタスクを実行できます。 また、SharePoint アクションからの出力を使用する他のアクションを含めることもできます。 たとえば、SharePoint からファイルを定期的に取得する場合は、Office 365 Outlook コネクタまたは Outlook.com コネクタを使用して、これらのファイルやその内容に関するメールのアラートを送信できます。 ロジック アプリを初めて使用する場合は、「[Azure Logic Apps とは](../logic-apps/logic-apps-overview.md)」を参照してください。 または、この[クイックスタートを試して、最初のロジック アプリ ワークフローの例を作成してみてください](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション。 Azure サブスクリプションがない場合は、[無料の Azure アカウントにサインアップ](https://azure.microsoft.com/free/)してください。 

* SharePoint サイト アドレスとユーザーの資格情報。 ワークフローを承認して SharePoint アカウントにアクセスできるようにするには、これらの資格情報が必要です。

* オンプレミスの SharePoint サーバーへの接続のために、ローカル コンピューターに[オンプレミス データ ゲートウェイをインストールし](../logic-apps/logic-apps-gateway-install.md)、[Azure で既に作成されているデータ ゲートウェイ リソースを設定する](../logic-apps/logic-apps-gateway-connection.md)必要があります。

  その後、ワークフローから SharePoint サーバー接続を作成するときに使用するゲートウェイ リソースを選択できます。

* SharePoint サイトまたはサーバーへのアクセスが必要なロジック アプリ ワークフロー。

  * SharePoint トリガーを使用してワークフローを開始するには、空のロジック アプリ ワークフローが必要です。
  * SharePoint アクションを追加するには、ワークフローに既にトリガーがある必要があります。

## <a name="connect-to-sharepoint"></a>SharePoint に接続する

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

## <a name="add-a-trigger"></a>トリガーの追加

1. Azure portal、Visual Studio Code、または Visual Studio から、ロジック アプリ デザイナーでロジック アプリ ワークフローを開きます (まだ開いていない場合)。

1. デザイナーの検索ボックスに、検索用語として「`sharepoint`」と入力します。 **SharePoint** コネクタを選択します。

1. **[トリガー]** 一覧から、使用するトリガーを選択します。

1. サインインして接続を作成するように求めるダイアログが表示されたら、次のいずれかのオプションを選択します。

   * SharePoint Online の場合は、 **[サインイン]** を選択し、ユーザーの資格情報を認証します。
   * SharePoint Server の場合は、 **[オンプレミスのデータ ゲートウェイ経由で接続する]** を選択します。 使用するゲートウェイ　リソースに関する要求情報、認証の種類、およびその他の必要な詳細を指定します。

1. 完了したら **[作成]** を選択します。

   ワークフローで接続が正常に作成されると、選択したトリガーが表示されます。

1. トリガーを設定するための情報を指定し、ワークフローのビルドを続行します。

## <a name="add-an-action"></a>アクションを追加する

1. Azure portal、Visual Studio Code、または Visual Studio から、ロジック アプリ デザイナーでロジック アプリ ワークフローを開きます (まだ開いていない場合)。

1. 次のいずれかのオプションを選択します。

   * 現在の最後のステップとしてアクションを追加するには、 **[新しいステップ]** を選択します。
   * ステップの間にアクションを追加するには、該当するステップ間の矢印の上にポインターを移動します。 プラス記号 ( **+** ) を選択し、 **[アクションの追加]** を選択します。

1. **[操作の選択]** で、検索ボックスに検索用語として「`sharepoint`」と入力します。 **SharePoint** コネクタを選択します。

1. **[アクション]** 一覧から、使用するアクションを選択します。

1. サインインして接続を作成するように求めるダイアログが表示されたら、次のいずれかのオプションを選択します。

   * SharePoint Online の場合は、 **[サインイン]** を選択し、ユーザーの資格情報を認証します。
   * SharePoint Server の場合は、 **[オンプレミスのデータ ゲートウェイ経由で接続する]** を選択します。 使用するゲートウェイ　リソースに関する要求情報、認証の種類、およびその他の必要な詳細を指定します。

1. 完了したら **[作成]** を選択します。

   ワークフローで接続が正常に作成されると、選択したアクションが表示されます。

1. アクションを設定するための情報を指定し、ワークフローのビルドを続行します。

## <a name="connector-reference"></a>コネクタのレファレンス

コネクタの Swagger ファイルに記述される、トリガー、アクション、制限などのこのコネクタの技術的詳細については、[コネクタの参照ページ](/connectors/sharepoint/)を参照してください。

## <a name="next-steps"></a>次のステップ

他の[Logic Apps コネクタ](../connectors/apis-list.md)を確認します。