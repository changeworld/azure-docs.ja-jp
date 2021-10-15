---
title: B2B ワークフローで EDIFACT メッセージを交換する
description: Azure Logic Apps と Enterprise Integration Pack を使用してワークフローを作成することにより、取引先間で EDIFACT メッセージを交換します。
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, azla
ms.topic: how-to
ms.date: 09/29/2021
ms.openlocfilehash: 19fea6a0405d1fcc4cfbc9b1aa7647c52b4459b6
ms.sourcegitcommit: 03e84c3112b03bf7a2bc14525ddbc4f5adc99b85
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/03/2021
ms.locfileid: "129401560"
---
# <a name="exchange-edifact-messages-using-workflows-in-azure-logic-apps"></a>Azure Logic Apps でワークフローを使用して EDIFACT メッセージを交換する

Azure Logic Apps を使用して作成したワークフローで EDIFACT メッセージを送受信するには、**EDIFACT** コネクタを使用します。これにより、EDIFACT 通信をサポートして管理するためのトリガーとアクションが提供されます。

この記事では、EDIFACT エンコードおよびデコード アクションを既存のロジック アプリ ワークフローに追加する方法について説明します。 任意のトリガーを使用してワークフローを開始できますが、例では [Request](../connectors/connectors-native-reqres.md) トリガーを使用します。 **EDIFACT** コネクタのトリガー、アクション、制限バージョンの詳細については、コネクタの Swagger ファイルで文書化されている[コネクタのリファレンス ページ](/connectors/edifact/)を参照してください。

![メッセージ デコード プロパティが指定された [EDIFACT メッセージのデコード] 操作を示す概要のスクリーンショット。](./media/logic-apps-enterprise-integration-edifact/overview-edifact-message-consumption.png)

## <a name="edifact-encoding-and-decoding"></a>EDIFACT のエンコードとデコード

次のセクションでは、EDIFACT のエンコードとデコードのアクションを使用して完了できるタスクについて説明します。

### <a name="encode-to-edifact-message-action"></a>EDIFACT メッセージへのエンコードのアクション

* 送信者の修飾子および識別子を受信者の修飾子および識別子と照合することで、契約を解決します。

* 電子データ交換 (EDI) をシリアル化します。これによって、XML エンコード メッセージがインターチェンジ内の EDI トランザクション セットに変換されます。

* トランザクション セット ヘッダーおよびトレーラ セグメントを適用します。

* 各送信インターチェンジのインターチェンジ制御番号、グループ制御番号、およびトランザクション セット制御番号を生成します。

* ペイロード データの区切り記号を置換します。

* メッセージ スキーマと照らし合わせたトランザクション セット データ要素のスキーマ、トランザクション セット データ要素、トランザクション セット データ要素に対する拡張検証など、EDI および取引先固有のプロパティを検証します。

* 各トランザクション セットの XML ドキュメントを生成します。

* 技術確認、機能確認、またはその両方を要求します (構成されている場合)。

  * 技術確認として、CONTRL メッセージはインターチェンジの受信を示します。

  * 機能確認として、CONTRL メッセージは、エラーまたはサポートされていない機能の一覧を含む、受信したインターチェンジ、グループ、またはメッセージの受理または拒否を示します。

### <a name="decode-edifact-message-action"></a>EDIFACT メッセージのデコードのアクション

* 取引先契約と照らし合わせてエンベロープを検証します。

* 送信者の修飾子および識別子を受信者の修飾子および識別子と照合することで、契約を解決します。

* トランザクションに契約の "**受信設定**" に基づく複数のトランザクション セットがある場合は、インターチェンジを複数のトランザクションに分割します。

* インターチェンジの逆アセンブル

* 電子データ交換 (EDI) と取引先固有のプロパティ (インターチェンジ エンベロープの構造、制御スキーマに照らし合わせたエンベロープ スキーマ、メッセージ スキーマに照らし合わせたトランザクション セット データ要素のスキーマ、トランザクション セット データ要素の拡張検証など) を検証します。

* 構成されている場合は、インターチェンジ、グループ、およびトランザクション セットの制御番号が重複していないことを検証します。次に例を示します。

  * インターチェンジ制御番号を、以前に受信したインターチェンジと比較して確認します。

  * インターチェンジ内の他のグループ制御番号と照らし合わせて、グループ制御番号を確認します。

  * グループ内の他のトランザクション セット制御番号と照らし合わせて、トランザクション セット制御番号を確認します。

* インターチェンジをトランザクション セットに分割するか、インターチェンジ全体を保持します。次に例を示します。

  * インターチェンジをトランザクション セットとして分割 - エラー発生時にトランザクション セットを中断します。

    デコード アクションにより、インターチェンジをトランザクション セットに分割し、各トランザクション セットを解析します。 このアクションは、検証が失敗したこれらのトランザクション セットのみを `badMessages` に出力し、残りのトランザクション セットを `goodMessages` に出力します。

  * インターチェンジをトランザクション セットとして分割 - エラー発生時にインターチェンジを中断します。

    デコード アクションにより、インターチェンジをトランザクション セットに分割し、各トランザクション セットを解析します。 インターチェンジの 1 つ以上のトランザクション セットが検証に失敗した場合、このアクションはそのインターチェンジのすべてのトランザクション セットを `badMessages` に出力します。

  * インターチェンジの保存 - エラー発生時にトランザクション セットを中断します。

    デコード アクションは、インターチェンジを保持し、バッチ インターチェンジ全体を処理します。 このアクションは、検証が失敗したこれらのトランザクション セットのみを `badMessages` に出力し、残りのトランザクション セットを `goodMessages` に出力します。

  * インターチェンジの保存 - エラー発生時にインターチェンジを中断します。

    デコード アクションは、インターチェンジを保持し、バッチ インターチェンジ全体を処理します。 インターチェンジの 1 つ以上のトランザクション セットが検証に失敗した場合、このアクションはそのインターチェンジのすべてのトランザクション セットを `badMessages` に出力します。

* 技術確認、機能確認、またはその両方を生成します (構成されている場合)。

  * 技術確認または制御確認。これは、完全に受信したインターチェンジの構文チェックの結果を報告します。

  * 受信したインターチェンジまたはグループの承認または拒否を確認する機能確認。

## <a name="connector-reference"></a>コネクタのレファレンス

**EDIFACT** コネクタの技術情報については、[コネクタのリファレンス ページ](/connectors/edifact/)を参照してください。ここでは、コネクタの Swagger ファイルで文書化されているトリガー、アクション、制限について説明しています。 また、[マルチテナント Azure Logic Apps、シングルテナント Azure Logic Apps、または統合サービス環境 (ISE)](logic-apps-overview.md#resource-environment-differences) で実行されているワークフローの [B2B プロトコルのメッセージ サイズの制限](logic-apps-limits-and-config.md#b2b-protocol-limits)に関するページも参照してください。 たとえば、[統合サービス環境 (ISE)](connect-virtual-network-vnet-isolated-environment-overview.md) の場合、このコネクタの ISE バージョンのコネクタでは、[ISE の B2B メッセージ制限](logic-apps-limits-and-config.md#b2b-protocol-limits)が使用されます。

## <a name="prerequisites"></a>前提条件

* Azure アカウントとサブスクリプション。 サブスクリプションをまだお持ちでない場合には、[無料の Azure アカウントにサインアップ](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)してください。

* エンタープライズ統合および B2B ワークフローで使用する成果物 (取引先、契約、証明書など) を定義して保存する[統合アカウント リソース](logic-apps-enterprise-integration-create-integration-account.md)。 このリソースでは、次の要件が満たされている必要があります。

  * ロジック アプリ リソースと同じ Azure サブスクリプションに関連付けられている。

  * ロジック アプリ リソースと同じ場所または Azure リージョンに存在する。

  * [**Logic App (Consumption)** のリソースの種類](logic-apps-overview.md#resource-environment-differences)と **EDIFACT** 操作を使用する場合、ロジック アプリ リソースには、統合アカウントへのリンクは必要ありません。 ただし、EDIFACT、[X12](logic-apps-enterprise-integration-x12.md)、または [AS2](logic-apps-enterprise-integration-as2.md) 操作を使用して、取引先、契約、証明書などの成果物を保存するには、引き続きこのアカウントが必要です。 統合アカウントは、ロジック アプリ リソースと同じ Azure サブスクリプションを使用することや、ロジック アプリ リソースと同じ場所に存在することなど、他の要件も満たす必要があります。

  * [**Logic App (Standard)** のリソースの種類](logic-apps-overview.md#resource-environment-differences)と **EDIFACT** 操作を使用する場合、ワークフローには、AS2 操作を追加するときにワークフローから直接作成する統合アカウントへの接続が必要です。

* 統合アカウント内の少なくとも 2 つの[取引先](logic-apps-enterprise-integration-partners.md)。 両方の取引先の定義では、同じ "*ビジネス ID*" 修飾子 (このシナリオでは **[ZZZ - 相互定義]** ) を使用する必要があります。

* ワークフローに参加する取引先間の統合アカウント内の [EDIFACT 契約](logic-apps-enterprise-integration-agreements.md)。 契約には、ホスト パートナーとゲスト パートナーが必要です。 他の取引先との間のメッセージの内容が、契約の種類と一致している必要があります。

  > [!IMPORTANT]
  > EDIFACT コネクタでは、UTF-8 文字のみがサポートされます。 出力に予期しない文字が含まれている場合は、EDIFACT メッセージで UTF-8 文字セットが使用されていることを確認します。

* EDIFACT 操作を使用するロジック アプリ リソースとワークフロー。

  ロジック アプリを初めて使用する場合は、「[Azure Logic Apps とは](logic-apps-overview.md)」と[クイック スタートの初めてのロジック アプリの作成](quickstart-create-first-logic-app-workflow.md)に関するページを参照してください。

<a name="encode"></a>

## <a name="encode-edifact-messages"></a>EDIFACT メッセージをエンコードする

### <a name="consumption"></a>[従量課金プラン](#tab/consumption)

1. [Azure portal](https://portal.azure.com) のデザイナーで、ロジック アプリ リソースとワークフローを開きます。

1. デザイナーで、EDIFACT アクションを追加するトリガーまたはアクションの下にある **[新しいステップ]** を選択します。

1. **[操作を選択してください]** の検索ボックスで、 **[すべて]** を選択します。 検索ボックスに「 `edifact encode`」と入力します。 この例では、 **[契約名で解決し EDIFACT メッセージにエンコードする]** という名前のアクションを選択します。

   ![Azure portal のワークフロー デザイナーを示すスクリーンショット。[契約名で解決し EDIFACT メッセージにエンコードする] アクションが選択されています。](./media/logic-apps-enterprise-integration-edifact/select-encode-edifact-message-consumption.png)

   > [!NOTE]
   > 代わりに **[ID で解決し EDIFACT メッセージにエンコードする]** アクションを選択することもできますが、後で EDIFACT 契約で指定されている "**送信者識別子**" や "**受信者識別子**" などの異なる値を指定する必要があります。 また、"**エンコードする XML メッセージ**" を指定する必要もあります。これは、トリガーまたは前のアクションからの出力です。

1. 統合アカウントへの接続を作成するように求めるメッセージが表示されたら、次の情報を入力します。

   | プロパティ | 必須 | 説明 |
   |----------|----------|-------------|
   | **接続名** | はい | 接続の名前 |
   | **統合アカウント** | はい | 使用可能な統合アカウントの一覧から、使用するアカウントを選択します。 |
   ||||

   次に例を示します。

   ![[契約名で解決し EDIFACT メッセージにエンコードする] 接続ウィンドウを示すスクリーンショット。](./media/logic-apps-enterprise-integration-edifact/create-edifact-encode-connection-consumption.png)

1. 完了したら **[作成]** を選択します。

1. デザイナーに EDIFACT 操作が表示されたら、この操作に固有の次のプロパティの情報を指定します。

   | プロパティ | 必須 | 説明 |
   |----------|----------|-------------|
   | **EDIFACT 契約の名前** | はい | 使用する EDIFACT 契約。 |
   | **エンコードする XML メッセージ** | はい | EDIFACT 契約で指定されているメッセージ送信者のビジネス識別子 |
   | その他のパラメーター | いいえ | この操作には、次の他のパラメーターが含まれます。 <p>- **データ要素区切り記号** <br>- **リリース インジケータ** <br>- **コンポーネントの区切り記号** <br>- **繰り返し区切り記号** <br>- **セグメント終端記号** <br>- **セグメント終端記号のサフィックス** <br>- **小数点** <p>詳細については、[EDIFACT メッセージの設定](logic-apps-enterprise-integration-edifact-message-settings.md)に関する記事を参照してください。 |
   ||||

   たとえば、XML メッセージ ペイロードは、Request トリガーからの **Body** コンテンツ出力にすることができます。

   ![メッセージ エンコード プロパティが指定された [契約名で解決し EDIFACT メッセージにエンコードする] 操作を示すスクリーンショット。](./media/logic-apps-enterprise-integration-edifact/encode-edifact-message-agreement-consumption.png)

### <a name="standard"></a>[Standard](#tab/standard)

1. [Azure portal](https://portal.azure.com) のデザイナーで、ロジック アプリ リソースとワークフローを開きます。

1. デザイナーで、EDIFACT message settings アクションを追加するトリガーまたはアクションの下にある **[新しいステップの挿入]** (プラス記号) を選択し、 **[アクションの追加]** を選択します。

1. **[操作を選択してください]** の検索ボックスで、 **[Azure]** を選択します。 検索ボックスに「 `edifact encode`」と入力します。 **[契約名で解決し EDIFACT メッセージにエンコードする]** という名前のアクションを選択します。

   ![Azure portal のワークフロー デザイナーを示すスクリーンショット。[契約名で解決し EDIFACT メッセージにエンコードする] 操作が選択されています。](./media/logic-apps-enterprise-integration-edifact/select-encode-edifact-message-standard.png)

   > [!NOTE]
   > 代わりに **[ID で解決し EDIFACT メッセージにエンコードする]** アクションを選択することもできますが、後で EDIFACT 契約で指定されている "**送信者識別子**" や "**受信者識別子**" などの異なる値を指定する必要があります。 また、"**エンコードする XML メッセージ**" を指定する必要もあります。これは、トリガーまたは前のアクションからの出力です。

1. 統合アカウントへの接続を作成するように求めるメッセージが表示されたら、次の情報を入力します。

   | プロパティ | 必須 | 説明 |
   |----------|----------|-------------|
   | **接続名** | はい | 接続の名前 |
   | **統合アカウント** | はい | 使用可能な統合アカウントの一覧から、使用するアカウントを選択します。 |
   ||||

   次に例を示します。

   ![[パラメーター名で解決し EDIFACT メッセージにエンコードする] 接続ウィンドウを示すスクリーンショット。](./media/logic-apps-enterprise-integration-edifact/create-edifact-encode-connection-standard.png)

1. 完了したら **[作成]** を選択します。

1. デザイナーに EDIFACT 詳細ウィンドウが表示されたら、次のプロパティの情報を指定します。

   | プロパティ | 必須 | 説明 |
   |----------|----------|-------------|
   | **EDIFACT 契約の名前** | はい | 使用する EDIFACT 契約。 |
   | **エンコードする XML メッセージ** | はい | EDIFACT 契約で指定されているメッセージ送信者のビジネス識別子 |
   | その他のパラメーター | いいえ | この操作には、次の他のパラメーターが含まれます。 <p>- **データ要素区切り記号** <br>- **リリース インジケータ** <br>- **コンポーネントの区切り記号** <br>- **繰り返し区切り記号** <br>- **セグメント終端記号** <br>- **セグメント終端記号のサフィックス** <br>- **小数点** <p>詳細については、[EDIFACT メッセージの設定](logic-apps-enterprise-integration-edifact-message-settings.md)に関する記事を参照してください。 |
   ||||

   たとえば、メッセージ ペイロードは、Request トリガーからの **Body** コンテンツ出力です。

   ![メッセージ エンコード プロパティが指定された [パラメーター名で解決し EDIFACT メッセージにエンコードする] 操作を示すスクリーンショット。](./media/logic-apps-enterprise-integration-edifact/encode-edifact-message-agreement-standard.png)

---

<a name="decode"></a>

## <a name="decode-edifact-messages"></a>EDIFACT メッセージをデコードする

### <a name="consumption"></a>[従量課金プラン](#tab/consumption)

1. [Azure portal](https://portal.azure.com) のデザイナーで、ロジック アプリ リソースとワークフローを開きます。

1. デザイナーで、EDIFACT アクションを追加するトリガーまたはアクションの下にある **[新しいステップ]** を選択します。

1. **[操作を選択してください]** の検索ボックスで、 **[すべて]** を選択します。 検索ボックスに「 `edifact encode`」と入力します。 **[EDIFACT メッセージをデコードする]** という名前のアクションを選択します。

1. 統合アカウントへの接続を作成するように求めるメッセージが表示されたら、次の情報を入力します。

   | プロパティ | 必須 | 説明 |
   |----------|----------|-------------|
   | **接続名** | はい | 接続の名前 |
   | **統合アカウント** | はい | 使用可能な統合アカウントの一覧から、使用するアカウントを選択します。 |
   ||||

   次に例を示します。

   ![[EDIFACT メッセージをデコードする] 接続ウィンドウを示すスクリーンショット。](./media/logic-apps-enterprise-integration-edifact/create-edifact-decode-connection-consumption.png)

1. 完了したら **[作成]** を選択します。

1. デザイナーに EDIFACT 操作が表示されたら、この操作に固有の次のプロパティの情報を指定します。

   | プロパティ | 必須 | 説明 |
   |----------|----------|-------------|
   | **デコードする EDIFACT フラット ファイル メッセージ** | はい | デコードする XML フラット ファイル メッセージ。 |
   | その他のパラメーター | いいえ | この操作には、次の他のパラメーターが含まれます。 <p>- **コンポーネントの区切り記号** <br>- **データ要素区切り記号** <br>- **リリース インジケータ** <br>- **繰り返し区切り記号** <br>- **セグメント終端記号** <br>- **セグメント終端記号のサフィックス** <br>- **小数点** <br>- **ペイロード文字セット** <br>- **セグメント終端記号のサフィックス** <br>- **インターチェンジの保存** <br>- **エラー発生時にインターチェンジを中断** <p>詳細については、[EDIFACT メッセージの設定](logic-apps-enterprise-integration-edifact-message-settings.md)に関する記事を参照してください。 |
   ||||

   たとえば、デコードする XML メッセージ ペイロードは、Request トリガーからの **Body** コンテンツ出力にすることができます。

   ![メッセージ デコード プロパティが指定された [EDIFACT メッセージのデコード] 操作を示すスクリーンショット。](./media/logic-apps-enterprise-integration-edifact/decode-edifact-message-consumption.png)

### <a name="standard"></a>[Standard](#tab/standard)

1. [Azure portal](https://portal.azure.com) のデザイナーで、ロジック アプリ リソースとワークフローを開きます。

1. デザイナーで、EDIFACT message settings アクションを追加するトリガーまたはアクションの下にある **[新しいステップの挿入]** (プラス記号) を選択し、 **[アクションの追加]** を選択します。

1. **[操作を選択してください]** の検索ボックスで、 **[Azure]** を選択します。 検索ボックスに「 `edifact encode`」と入力します。 **[EDIFACT メッセージをデコードする]** という名前のアクションを選択します。

   ![Azure portal のワークフロー デザイナーを示すスクリーンショット。[EDIFACT メッセージをデコードする] 操作が選択されています。](./media/logic-apps-enterprise-integration-edifact/select-decode-edifact-message-standard.png)

1. 統合アカウントへの接続を作成するように求めるメッセージが表示されたら、次の情報を入力します。

   | プロパティ | 必須 | 説明 |
   |----------|----------|-------------|
   | **接続名** | はい | 接続の名前 |
   | **統合アカウント** | はい | 使用可能な統合アカウントの一覧から、使用するアカウントを選択します。 |
   ||||

   次に例を示します。

   ![[EDIFACT メッセージをデコードする] 接続ウィンドウを示すスクリーンショット。](./media/logic-apps-enterprise-integration-edifact/create-edifact-decode-connection-standard.png)

1. 完了したら **[作成]** を選択します。

1. デザイナーに EDIFACT 詳細ウィンドウが表示されたら、次のプロパティの情報を指定します。

   | プロパティ | 必須 | 説明 |
   |----------|----------|-------------|
   | **EDIFACT 契約の名前** | はい | 使用する EDIFACT 契約。 |
   | **エンコードする XML メッセージ** | はい | EDIFACT 契約で指定されているメッセージ送信者のビジネス識別子 |
   | その他のパラメーター | いいえ | この操作には、次の他のパラメーターが含まれます。 <p>- **データ要素区切り記号** <br>- **リリース インジケータ** <br>- **コンポーネントの区切り記号** <br>- **繰り返し区切り記号** <br>- **セグメント終端記号** <br>- **セグメント終端記号のサフィックス** <br>- **小数点** <p>詳細については、[EDIFACT メッセージの設定](logic-apps-enterprise-integration-edifact-message-settings.md)に関する記事を参照してください。 |
   ||||

   たとえば、メッセージ ペイロードは、Request トリガーからの **Body** コンテンツ出力です。

   ![メッセージ デコード プロパティが指定された [EDIFACT メッセージのデコード] 操作を示すスクリーンショット。](./media/logic-apps-enterprise-integration-edifact/decode-edifact-message-standard.png)

---

## <a name="handle-unh25-segments-in-edifact-documents"></a>EDIFACT ドキュメント内の UNH 2.5 セグメントを処理する

EDIFACT ドキュメントでは、スキーマの検索に [UNH2.5 セグメント](logic-apps-enterprise-integration-edifact-message-settings.md#receive-settings-schemas)が使用されます。 たとえば、このサンプルの EDIFACT メッセージでは、UNH フィールドは `EAN008` です。

`UNH+SSDD1+ORDERS:D:03B:UN:EAN008`

EDIFACT ドキュメントを処理したり、UN2.5 セグメントを含む EDIFACT メッセージを処理したりするには、次の手順を実行します。

1. UNH 2.5 のルート ノード名を持つスキーマを更新またはデプロイします。

   たとえば、サンプルの UNH フィールドのスキーマ ルート名が `EFACT_D03B_ORDERS_EAN008` であるとします。 UNH2.5 セグメントが異なる `D03B_ORDERS` ごとに、個別のスキーマをデプロイする必要があります。

1. [Azure portal](https://portal.azure.com) で、統合アカウントのリソースまたはロジック アプリのリソースにスキーマを追加します。これは、それぞれ "**ロジック アプリ (従量課金)** " と "**ロジック アプリ (Standard)** " のどちらのリソースの種類を使用しているかに基づいています。

1. EDIFACT のデコードまたはエンコードのアクションを使用しているかどうかにかかわらず、スキーマをアップロードし、EDIFACT 契約の **[受信設定]** または **[送信設定]** セクションでスキーマ設定を設定します。

1. EDIFACT 契約を編集するには、 **[アグリーメント]** ペインで契約を選択します。 **[アグリーメント]** ペインのツールバーで、 **[JSON として編集]** を選択します。

   * 契約の `receiveAgreement` セクションで、`schemaReferences` セクションを見つけて、UNH2.5 の値を追加します。

     ![JSON エディターに EDIFACT 契約の "receiveAgreement" セクションがある Azure portal を示すスクリーンショット。"schemaReferences" セクションが強調表示されています。](./media/logic-apps-enterprise-integration-edifact/agreement-receive-schema-references.png)

   * 契約の `sendAgreement` セクションで、`schemaReferences` セクションを見つけて、UNH2.5 の値を追加します。

     ![JSON エディターに EDIFACT 契約の "sendAgreement" セクションがある Azure portal を示すスクリーンショット。"schemaReferences" セクションが強調表示されています。](./media/logic-apps-enterprise-integration-edifact/agreement-send-schema-references.png)

## <a name="next-steps"></a>次のステップ

* [EDIFACT メッセージの設定](logic-apps-enterprise-integration-edifact-message-settings.md)
