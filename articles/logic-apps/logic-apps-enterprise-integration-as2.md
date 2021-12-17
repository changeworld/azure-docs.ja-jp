---
title: B2B ワークフローで AS2 メッセージを交換する
description: Azure Logic Apps と Enterprise Integration Pack を使用してワークフローを作成することにより、取引先間で AS2 メッセージを交換します。
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, azla
ms.topic: how-to
ms.date: 09/27/2021
ms.openlocfilehash: 8023073aecae3a1f97c82a16a5be952df7425186
ms.sourcegitcommit: 03e84c3112b03bf7a2bc14525ddbc4f5adc99b85
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/03/2021
ms.locfileid: "129399654"
---
# <a name="exchange-as2-messages-using-workflows-in-azure-logic-apps"></a>Azure Logic Apps でワークフローを使用して AS2 メッセージを交換する

Azure Logic Apps を使用して作成したワークフローで AS2 メッセージを送受信するには、**AS2** コネクタを使用します。これにより、AS2 (バージョン 1.2) 通信をサポートして管理するためのトリガーとアクションが提供されます。

* **Logic App (Consumption)** のリソースの種類を使用するが、追跡機能を必要としない場合、非推奨になる元の **AS2** コネクタではなく **AS2 (v2)** コネクタを使用します。

  **AS2 (v2)** を使用すると、パフォーマンスが向上し、追跡機能を除いて、元のバージョンと同じ機能が提供されます。また、これは、Azure Logic Apps ランタイムにネイティブであり、メッセージ サイズ、スループット、待機時間について大幅なパフォーマンスの向上が実現されます。 また、v2 コネクタでは、統合アカウントへの接続を作成する必要はありません。 代わりに、前提条件で説明されているように、コネクタを使用する予定のロジック アプリ リソースに統合アカウントがリンクされていることを確認してください。

* **Logic App (Standard)** のリソースの種類を使用する場合は、現在、元の **AS2** コネクタのみが使用できます。

  元の **AS2** コネクタのバージョンの技術情報については、[コネクタのリファレンス ページ](/connectors/as2/)を参照してください。ここでは、コネクタの Swagger ファイルで文書化されているトリガー、アクション、制限について説明しています。

### <a name="consumption"></a>[従量課金プラン](#tab/consumption)

次の一覧では、メッセージを送信するときにセキュリティと信頼性を確立するために **AS2 (v2)** コネクタによって提供されるアクションについて説明します。

* [**AS2 エンコード** アクション](#encode): 暗号化、デジタル署名、メッセージ処理通知 (MDN) を介した受信確認を提供します。これらは否認防止をサポートするのに役立ちます。 たとえば、このアクションでは、AS2/HTTP ヘッダーが適用され、構成されている場合は、次のタスクが実行されます。

  * 送信メッセージに署名する。
  * 送信メッセージを暗号化する。
  * メッセージを圧縮する。
  * MIME ヘッダー内のファイル名を送信する。

* [**AS2 デコード** アクション](#decode): 暗号化の解除、デジタル署名、メッセージ処理通知 (MDN) を介した受信確認を提供します。 たとえば、このアクションでは、次のタスクが実行されます。

  * AS2/HTTP ヘッダーを処理する。
  * 受信した MDN と元の送信メッセージを調整する。
  * 否認不可データベース内のレコードを更新し、関連付ける。
  * AS2 状態レポート用のレコードを書き込む。
  * ペイロードの内容を base64 でエンコードして出力する。
  * MDN が必要かどうかを判断する。 AS2 契約に基づいて、MDN を同期または非同期のどちらにするかを決定する。
  * AS2 契約に基づいて、同期または非同期の MDN を生成する。
  * MDN に対して関連付けトークンとプロパティを設定する

  このアクションでは、構成されている場合、次のタスクも実行されます。

  * 署名を確認する。
  * メッセージの暗号化を解除する。
  * メッセージの圧縮を解除する。
  * メッセージ ID の重複を確認し不許可にする。

### <a name="standard"></a>[Standard](#tab/standard)

元の **AS2** コネクタのトリガー、アクション、制限バージョンの詳細については、コネクタの Swagger ファイルで文書化されている [コネクタのリファレンス ページ](/connectors/as2/)を参照してください。

---

この記事では、AS2 エンコードおよびデコード アクションを既存のロジック アプリ ワークフローに追加する方法について説明します。 任意のトリガーを使用してワークフローを開始できますが、例では [Request](../connectors/connectors-native-reqres.md) トリガーを使用します。

## <a name="limits"></a>制限

[マルチテナント Azure Logic Apps、シングルテナント Azure Logic Apps、または統合サービス環境 (ISE)](logic-apps-overview.md#resource-environment-differences) で実行されているワークフローの AS2 コネクタの制限については、[B2B プロトコルのメッセージ サイズの制限](logic-apps-limits-and-config.md#b2b-protocol-limits)に関するページを参照してください。

## <a name="prerequisites"></a>前提条件

* Azure アカウントとサブスクリプション。 サブスクリプションをまだお持ちでない場合には、[無料の Azure アカウントにサインアップ](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)してください。

* エンタープライズ統合および B2B ワークフローで使用する成果物 (取引先、契約、証明書など) を定義して保存する[統合アカウント リソース](logic-apps-enterprise-integration-create-integration-account.md)。 このリソースでは、次の要件が満たされている必要があります。

  * ロジック アプリ リソースと同じ Azure サブスクリプションに関連付けられている。

  * ロジック アプリ リソースと同じ場所または Azure リージョンに存在する。

  * [**Logic App (Consumption)** のリソースの種類](logic-apps-overview.md#resource-environment-differences)と **AS2 (v2)** 操作を使用する場合、ロジック アプリ リソースには、統合アカウントへのリンクは必要ありません。 ただし、AS2、[X12](logic-apps-enterprise-integration-x12.md)、または [EDIFACT](logic-apps-enterprise-integration-edifact.md) 操作を使用して、取引先、契約、証明書などの成果物を保存するには、引き続きこのアカウントが必要です。 統合アカウントは、ロジック アプリ リソースと同じ Azure サブスクリプションを使用することや、ロジック アプリ リソースと同じ場所に存在することなど、他の要件も満たす必要があります。

  * [**Logic App (Standard)** のリソースの種類](logic-apps-overview.md#resource-environment-differences)と元の **AS2** 操作を使用する場合、ワークフローには、AS2 操作を追加するときにワークフローから直接作成する統合アカウントへの接続が必要です。

* 統合アカウント内の少なくとも 2 つの[取引先](logic-apps-enterprise-integration-partners.md)。 両方の取引先の定義では、同じ "*ビジネス ID*" 修飾子 (このシナリオでは **AS2Identity**) を使用する必要があります。

* ワークフローに参加する取引先間の統合アカウント内の [AS2 契約](logic-apps-enterprise-integration-agreements.md)。 契約には、ホスト パートナーとゲスト パートナーが必要です。 他の取引先との間のメッセージの内容が、契約の種類と一致している必要があります。

* AS2 操作を使用するロジック アプリ リソースとワークフロー。

  > [!NOTE]
  > **AS2 (v2)** コネクタでは、アクションのみ提供され、トリガーは提供されません。 この記事のこのコネクタの例では、[Request](../connectors/connectors-native-reqres.md) トリガーを使用します。 元の **AS2** コネクタには、トリガーとアクションが含まれています。 元の **AS2** コネクタのトリガー、アクション、制限バージョンの詳細については、コネクタの Swagger ファイルで文書化されている [コネクタのリファレンス ページ](/connectors/as2/)を参照してください。

  ロジック アプリを初めて使用する場合は、「[Azure Logic Apps とは](logic-apps-overview.md)」と[クイック スタートの初めてのロジック アプリの作成](quickstart-create-first-logic-app-workflow.md)に関するページを参照してください。

* 証明書の管理に [ Azure Key Vault](../key-vault/general/overview.md) を使用する場合は、ご利用のコンテナー キーで **暗号化** 操作および **暗号化の解除** 操作が許可されていることを確認してください。 そのようになっていない場合、エンコードとデコードのアクションは失敗します。

  1. Azure portal でキー コンテナーを開きます。 キー コンテナー メニューの **[設定]** で、 **[キー]** を選択します。

  1. **[キー]** ウィンドウで、ご自分のキーを選択します。 **[バージョン]** ウィンドウで、使用しているキーのバージョンを選択します。

  1. **[キーのバージョン]** ウィンドウの **[許可された操作]** で、 **[暗号化]** および **[暗号化解除]** の操作がオンになっていることを確認します。例を次に示します。

     ![[キー コンテナー]、[キー]、[キーのバージョン] の各ウィンドウが開かれており、[暗号化] および [暗号化解除] 操作がオンになっている Azure portal を示すスクリーンショット。](media/logic-apps-enterprise-integration-as2/key-vault-permitted-operations.png)

<a name="encode"></a>

## <a name="encode-as2-messages"></a>AS2 メッセージをエンコードする

### <a name="consumption"></a>[従量課金プラン](#tab/consumption)

1. [Azure portal](https://portal.azure.com) のデザイナーで、ロジック アプリ リソースとワークフローを開きます。

1. デザイナーで、AS2 アクションを追加するトリガーまたはアクションの下にある **[新しいステップ]** を選択します。

1. **[操作を選択してください]** の検索ボックスで、 **[すべて]** を選択します。 検索ボックスに「 `as2 encode`」と入力します。 **[AS2 エンコード]** という名前のアクションを選択します。

   ![Azure portal のワークフロー デザイナーを示すスクリーンショット。[AS2 エンコード] アクションが選択されています。](./media/logic-apps-enterprise-integration-as2/select-as2-encode.png)

1. デザイナーに AS2 操作が表示されたら、次のプロパティの情報を指定します。

   | プロパティ | 必須 | 説明 |
   |----------|----------|-------------|
   | **エンコードするメッセージ** | はい | メッセージ ペイロード |
   | **AS2 の送信元** | はい | AS2 契約で指定されているメッセージ送信者のビジネス識別子 |
   | **AS2 の宛先** | はい | AS2 契約で指定されているメッセージ受信者のビジネス識別子 |
   ||||

   たとえば、メッセージ ペイロードは、Request トリガーからの **Body** コンテンツ出力です。

   ![メッセージ エンコード プロパティが指定された [AS2 エンコード] アクションを示すスクリーンショット。](./media/logic-apps-enterprise-integration-as2/as2-message-encode-details.png)

   > [!TIP]
   > 署名または暗号化されたメッセージの送信中に問題が発生した場合は、異なる SHA256 アルゴリズム フォームを試すことを検討してください。 AS2 の仕様では SHA256 フォームに関する情報は提供されないため、各プロバイダーでは独自の実装または形式が使用されます。

### <a name="standard"></a>[Standard](#tab/standard)

1. [Azure portal](https://portal.azure.com) のデザイナーで、ロジック アプリ リソースとワークフローを開きます。

1. デザイナーで、AS2 アクションを追加するトリガーまたはアクションの下にある **[新しいステップの挿入]** (プラス記号) を選択し、 **[アクションの追加]** を選択します。

1. **[操作を選択してください]** の検索ボックスで、 **[Azure]** を選択します。 検索ボックスに「 `as2 encode`」と入力します。 **[AS2 メッセージにエンコードする]** という名前のアクションを選択します。

   ![Azure portal のワークフロー デザイナーを示すスクリーンショット。[AS2 メッセージにエンコードする] 操作が選択されています。](./media/logic-apps-enterprise-integration-as2/select-encode-as2-message.png)

1. 統合アカウントへの接続を作成するように求めるメッセージが表示されたら、次の情報を入力します。

   | プロパティ | 必須 | 説明 |
   |----------|----------|-------------|
   | **接続名** | はい | 接続の名前 |
   | **統合アカウント** | はい | 使用可能な統合アカウントの一覧から、使用するアカウントを選択します。 |
   ||||

   次に例を示します。

   ![[AS2 メッセージにエンコードする] 接続ウィンドウを示すスクリーンショット。](./media/logic-apps-enterprise-integration-as2/create-as2-encode-connection-standard.png)

1. 完了したら **[作成]** を選択します。

1. デザイナーに AS2 詳細ウィンドウが表示されたら、次のプロパティの情報を指定します。

   | プロパティ | 必須 | 説明 |
   |----------|----------|-------------|
   | **エンコードするメッセージ** | はい | メッセージ ペイロード |
   | **AS2 の送信元** | はい | AS2 契約で指定されているメッセージ送信者のビジネス識別子 |
   | **AS2 の宛先** | はい | AS2 契約で指定されているメッセージ受信者のビジネス識別子 |
   ||||

   たとえば、メッセージ ペイロードは、Request トリガーからの **Body** コンテンツ出力です。

   ![メッセージ エンコード プロパティが指定された [AS2 エンコード] 操作を示すスクリーンショット。](./media/logic-apps-enterprise-integration-as2/encode-as2-message-details.png)

   > [!TIP]
   > 署名または暗号化されたメッセージの送信中に問題が発生した場合は、異なる SHA256 アルゴリズム フォームを試すことを検討してください。 AS2 の仕様では SHA256 フォームに関する情報は提供されないため、各プロバイダーでは独自の実装または形式が使用されます。

---

<a name="decode"></a>

## <a name="decode-as2-messages"></a>AS2 メッセージをデコードする

### <a name="consumption"></a>[従量課金プラン](#tab/consumption)

1. [Azure portal](https://portal.azure.com) のデザイナーで、ロジック アプリ リソースとワークフローを開きます。

1. デザイナーで、AS2 アクションを追加するトリガーまたはアクションの下にある **[新しいステップ]** を選択します。 この例では、[Request](../connectors/connectors-native-reqres.md) トリガーを使用します。

1. **[操作を選択してください]** の検索ボックスで、 **[すべて]** を選択します。 検索ボックスに「 `as2 decode`」と入力します。 **[AS2 デコード]** という名前のアクションを選択します。

   ![Azure portal のワークフロー デザイナーを示すスクリーンショット。"AS2 デコード" 操作が選択されています。](media/logic-apps-enterprise-integration-as2/select-as2-decode.png)

1. AS2 操作シェイプで、前のトリガーまたはアクションの出力から、 **[エンコードするメッセージ]** および **[メッセージ ヘッダー]** プロパティの値を選択します。

   この例では、Request トリガーからの出力を選択できます。

   ![Azure portal のワークフロー デザイナー、[AS2 デコード] 操作を示すスクリーンショット。Request トリガーからの "Body" および "Headers" 出力が選択されています。](media/logic-apps-enterprise-integration-as2/as2-message-decode-details.png)

### <a name="standard"></a>[Standard](#tab/standard)

1. [Azure portal](https://portal.azure.com) のデザイナーで、ロジック アプリ リソースとワークフローを開きます。

1. デザイナーで、AS2 アクションを追加するトリガーまたはアクションの下にある **[新しいステップの挿入]** (プラス記号) を選択し、 **[アクションの追加]** を選択します。

1. **[操作を選択してください]** の検索ボックスで、 **[Azure]** を選択します。 検索ボックスに「 `as2 decode`」と入力します。 **[AS2 メッセージをデコードする]** という名前のアクションを選択します。

   ![Azure portal のワークフロー デザイナーを示すスクリーンショット。[AS2 メッセージをデコードする] 操作が選択されています。](./media/logic-apps-enterprise-integration-as2/select-decode-as2-message.png)

1. 統合アカウントへの接続を作成するように求めるメッセージが表示されたら、次の情報を入力します。

   | プロパティ | 必須 | 説明 |
   |----------|----------|-------------|
   | **接続名** | はい | 接続の名前 |
   | **統合アカウント** | はい | 使用可能な統合アカウントの一覧から、使用するアカウントを選択します。 |
   ||||

   次に例を示します。

   ![[AS2 メッセージをデコードする] 接続ウィンドウを示すスクリーンショット。](./media/logic-apps-enterprise-integration-as2/create-as2-decode-connection-standard.png)

1. 完了したら **[作成]** を選択します。

1. AS2 詳細ペインで、前のトリガーまたはアクションの出力から、 **[エンコードするメッセージ]** および **[メッセージ ヘッダー]** プロパティの値を選択します。

   この例では、Request トリガーからの出力を選択できます。

   ![Azure portal のワークフロー デザイナー、[AS2 メッセージをデコードする] 操作を示すスクリーンショット。Request トリガーから "Body" と "Headers" の出力が選択されています。](media/logic-apps-enterprise-integration-as2/decode-as2-message-details.png)

---

## <a name="sample"></a>サンプル

完全に動作するロジック アプリとサンプルの AS2 (v2) シナリオのデプロイを試すには、[AS2 (v2) ロジック アプリのテンプレートとシナリオ](https://azure.microsoft.com/resources/templates/logic-app-as2-send-receive/)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

* [Azure Logic Apps の他のコネクタ](../connectors/apis-list.md)の詳細情報
