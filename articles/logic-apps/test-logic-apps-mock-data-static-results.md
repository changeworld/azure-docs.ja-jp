---
title: モック テストのワークフロー
description: 運用環境に影響を与えずに Azure Logic Apps でワークフローをテストするためのモック データを設定します。
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, azla
ms.topic: how-to
ms.date: 10/08/2021
ms.openlocfilehash: 4167dcffe0a1b4db50f6d1580ad6284f2cf9321d
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2021
ms.locfileid: "129712570"
---
# <a name="test-workflows-with-mock-data-in-azure-logic-apps-preview"></a>Azure Logic Apps でモック データを使用してワークフローをテストする (プレビュー)

> [!NOTE]
> この機能はプレビュー段階にあり、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」が適用されます。

稼働中のアプリ、データ、サービス、またはシステムを実際に呼び出したりアクセスしたりせずにワークフローをテストするには、アクションからモック値を設定して返すことができます。 たとえば、さまざまな条件に基づいて異なるアクション パスをテストしたり、強制的にエラーを発生させたり、特定のメッセージ応答本文を指定したり、一部のステップをスキップしたりすることができます。 アクションでモック データのテストを設定してもアクションは実行されず、代わりにモック データが返されます。

たとえば、Outlook 365 のメール送信アクションにモック データを設定した場合、Azure Logic Apps は、Outlook を呼び出してメールを送信するのではなく、提供したモック データだけを返します。

この記事では、[**Logic App (従量課金プラン)** および **Logic App (Standard)** リソース タイプ](logic-apps-overview.md#resource-environment-differences)において、ワークフロー内のアクションでモック データを設定する方法について説明します。 これらのモック データを使用した過去のワークフロー実行を検索し、既存のアクション出力をモック データとして再利用することができます。

## <a name="prerequisites"></a>前提条件

* Azure アカウントとサブスクリプション。 サブスクリプションをお持ちでない場合には、<a href="https://azure.microsoft.com/free/?WT.mc_id=A261C142F" target="_blank">無料の Azure アカウントにサインアップ</a>してください。

* モック データを設定するロジック アプリのリソースとワークフロー。 この記事では、**Recurrence** トリガーと **HTTP** アクションをワークフローの例として使用しています。

  ロジック アプリを初めて使用する場合は、「[Azure Logic Apps とは](logic-apps-overview.md)」と「[クイックスタート: 初めてのロジック アプリ ワークフローの作成](quickstart-create-first-logic-app-workflow.md)」を参照してください。

<a name="enable-mock-data"></a>

## <a name="enable-mock-data-output"></a>モック データの出力を有効にする

### <a name="consumption"></a>[従量課金プラン](#tab/consumption)

1. [Azure portal](https://portal.azure.com) で、ロジック アプリ ワークフローをデザイナーで開きます。

1. モック データを返す先となるアクションで、以下の手順を実行します。

   1. アクションの右上隅にある省略記号 ( *...* ) ボタンを選択してから、 **[Testing]\(テスト\)** を選択します。例は以下のとおりです。

      ![Azure portal のワークフロー デザイナーを示すスクリーンショット。アクションのショートカット メニューと "テスト" が選択されています。](./media/test-logic-apps-mock-data-static-results/select-testing.png)

   1. **[Testing]\(テスト\)** ペインで、 **[静的な結果の有効化 (プレビュー)]** を選択します。 アクションの必須 (*) プロパティが表示されたら、アクションの応答として返すモック出力値を指定します。

      プロパティは、選択したアクションの種類によって異なります。 たとえば、HTTP アクションに必須なプロパティは以下のとおりです。

      | プロパティ | 説明 |
      |----------|-------------|
      | **状態** | 返されるアクションの状態 |
      | **状態コード** | 出力として返される特定の状態コード |
      | **ヘッダー** | 返されるヘッダーの内容 |
      |||

      !["静的な結果の有効化" を選択した後の "テスト" ウィンドウを示すスクリーンショット。](./media/test-logic-apps-mock-data-static-results/enable-static-result.png)

      > [!TIP]
      > JavaScript Object Notation (JSON) 形式で値を入力するには、 **[JSON モードに切り替える]** (![[JSON モードに切り替える] アイコン](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button.png)) を選択します。

   1. 省略可能なプロパティについては、 **[省略可能なフィールドを選択する]** の一覧を開いて、モックするプロパティを選択します。

      !["省略可能なフィールドを選択する" リストが表示されている "テスト" ペインのスクリーンショット。](./media/test-logic-apps-mock-data-static-results/optional-properties.png)

1. 準備ができたら、 **[完了]** を選択します。

   アクションの右上隅では、キャプション バーにテスト ビーカー アイコン (![静的な結果のアイコン](./media/test-logic-apps-mock-data-static-results/static-result-test-beaker-icon.png)) が表示されるようになり、静的な結果が有効になっていることを示します。

   ![静的な結果アイコンが付いたアクションを示すスクリーンショット。](./media/test-logic-apps-mock-data-static-results/static-result-enabled.png)

   モック データを使用するワークフローの実行を検索するには、このトピックで後述する[静的な結果を使用した実行を見つける](#find-runs-mock-data)方法に関するセクションを参照してください。

### <a name="standard"></a>[Standard](#tab/standard)

1. [Azure portal](https://portal.azure.com) で、ロジック アプリ ワークフローをデザイナーで開きます。

1. デザイナーで、アクションの詳細ペインが表示されるよう、モック データを返したいアクションを選択します。

1. アクションの詳細ペインが右側に表示されたら、 **[Testing]\(テスト\)** を選択します。

   ![Azure portal のワークフロー デザイナーを示すスクリーンショット。アクションの詳細ペインと "テスト" が選択されています。](./media/test-logic-apps-mock-data-static-results/select-testing-standard.png)

1. **[Testing]\(テスト\)** タブで、 **[静的な結果の有効化 (プレビュー)]** を選択します。 アクションの必須 (*) プロパティが表示されたら、アクションの応答として返すモック出力値を指定します。

   プロパティは、選択したアクションの種類によって異なります。 たとえば、HTTP アクションに必須なプロパティは以下のとおりです。

   | プロパティ | 説明 |
   |----------|-------------|
   | **状態** | 返されるアクションの状態 |
   | **状態コード** | 出力として返される特定の状態コード |
   | **ヘッダー** | 返されるヘッダーの内容 |
   |||

   !["静的な結果の有効化" を選択した後の "テスト" タブを示すスクリーンショット。](./media/test-logic-apps-mock-data-static-results/enable-static-result-standard.png)

   > [!TIP]
   > JavaScript Object Notation (JSON) 形式で値を入力するには、 **[JSON モードに切り替える]** (![[JSON モードに切り替える] アイコン](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button.png)) を選択します。

1. 省略可能なプロパティについては、 **[省略可能なフィールドを選択する]** の一覧を開いて、モックするプロパティを選択します。

   !["省略可能なフィールドを選択する" リストが表示されている "テスト" ペインのスクリーンショット。](./media/test-logic-apps-mock-data-static-results/optional-properties-standard.png)

1. 準備ができたら、 **[完了]** を選択します。

   アクションの右下隅には、テスト ビーカー アイコン (![静的な結果のアイコン](./media/test-logic-apps-mock-data-static-results/static-result-test-beaker-icon.png)) が表示されるようになり、静的な結果が有効になっていることを示します。

   ![デザイナー上の静的な結果アイコンが付いたアクションを示すスクリーンショット。](./media/test-logic-apps-mock-data-static-results/static-result-enabled-standard.png)

   モック データを使用するワークフローの実行を検索するには、このトピックで後述する[静的な結果を使用した実行を見つける](#find-runs-mock-data)方法に関するセクションを参照してください。

---

<a name="find-runs-mock-data"></a>

## <a name="find-runs-that-use-mock-data"></a>モック データを使用する実行を検索する

### <a name="consumption"></a>[従量課金プラン](#tab/consumption)

アクションでモック データが使用されている以前のワークフローの実行を見つけるには、そのワークフローの実行履歴を確認します。

1. [Azure portal](https://portal.azure.com) で、ロジック アプリ ワークフローをデザイナーで開きます。

1. ロジック アプリのリソース メニューで、 **[概要]** を選択します。

1. まだ選択されていない場合は、 **[Essentials]\(要点\)** セクションの **[実行の履歴]** を選択します。

1. **[実行の履歴]** テーブルの下で **[静的な結果]** 列を見つけます。

   モック データの出力を伴ったアクションを含むどの実行でも、たとえば次のように、 **[静的な結果]** 列が **[有効]** に設定されています。

   !["静的な結果" 列を含むワークフローの実行履歴を示すスクリーンショット。](./media/test-logic-apps-mock-data-static-results/run-history.png)

1. モック データを使用したアクションを実行内で表示するには、 **[静的な結果]** 列が **[有効]** に設定されている目的の実行を選択します。

   静的な結果を使用したアクションには、たとえば次のように、テスト ビーカー (![静的な結果のアイコン](./media/test-logic-apps-mock-data-static-results/static-result-test-beaker-icon.png)) が表示されます。

   ![静的な結果を使用するアクションが含まれているワークフローの実行履歴を示すスクリーンショット。](./media/test-logic-apps-mock-data-static-results/static-result-enabled-run-details.png)

### <a name="standard"></a>[Standard](#tab/standard)

アクションでモック データが使用されている他のワークフローの実行を検索するには、各実行を確認する必要があります。

1. [Azure portal](https://portal.azure.com) で、ロジック アプリ ワークフローをデザイナーで開きます。

1. ワークフロー メニューで、 **[概要]** を選択します。

1. まだ選択されていない場合は、 **[Essentials]\(要点\)** セクションの **[実行履歴]** を選択します。

1. **[実行履歴]** テーブルで、確認する実行を選択します。

   ![ワークフローの実行履歴を示すスクリーンショット。](./media/test-logic-apps-mock-data-static-results/select-run-standard.png)

1. [実行の詳細] ペインで、テスト ビーカー (![静的な結果のアイコン](./media/test-logic-apps-mock-data-static-results/static-result-test-beaker-icon.png)) が表示されているアクションがあるかどうかを確認します。

   ![静的な結果を使用するアクションが含まれているワークフローの実行履歴を示すスクリーンショット。](./media/test-logic-apps-mock-data-static-results/run-history-static-result-standard.png)

---

<a name="reuse-sample-outputs"></a>

## <a name="reuse-previous-outputs-as-mock-data"></a>前の出力をモック データとして再利用する

以前に実行したワークフローに出力がある場合、その実行から出力をコピーして貼り付けることで、その出力をモック データとして再利用することができます。

### <a name="consumption"></a>[従量課金プラン](#tab/consumption)

1. [Azure portal](https://portal.azure.com) で、ロジック アプリ ワークフローをデザイナーで開きます。

1. ロジック アプリのリソース メニューで、 **[概要]** を選択します。

1. まだ選択されていない場合は、 **[Essentials]\(要点\)** セクションの **[実行の履歴]** を選択します。 表示されるリストから、目的のワークフローの実行を選択します。

   ![ワークフローの実行履歴を示すスクリーンショット。](./media/test-logic-apps-mock-data-static-results/select-run.png)

1. 実行の結果ペインが開いたら、目的の出力を含むアクションを展開します。

1. **[出力]** セクションで、 **[未加工出力の表示]** を選択します。

1. **[出力]** ペインに、完全な JavaScript Object Notation (JSON) オブジェクトか、出力セクションやヘッダー セクションなどの使用する特定のサブセクションのどちらかをコピーします。

1. アクションで [モック アップ データを設定する](#enable-mock-data)方法に関する前述のセクションを確認し、手順に従ってアクションの **[Testing]\(テスト\)** ペインを開きます。

1. **[Testing]\(テスト\)** ペインが開いたら、次のどちらかの手順を選択します。

   * 完全な JSON オブジェクトを貼り付けるには、 **[Testing]\(テスト\)** ラベルの横にある **[JSON モードに切り替える]** (![[JSON モードに切り替える] のアイコン](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button.png)) を選択します。

     ![完全な JSON オブジェクトを貼り付ける [JSON モードに切り替える] アイコンが選択されているスクリーンショット。](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button-complete.png)

   * JSON のセクションだけを貼り付けるには、そのセクションのラベル (**出力** や **ヘッダー** など) の横にある、**JSON モードに切り替える** を選択します。たとえば、以下のとおりです。

     ![JSON オブジェクトのセクションを貼り付けるための [JSON モードに切り替える] アイコンが選択されているスクリーンショット。](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button-output.png)

1. JSON エディターで、以前にコピーした JSON を貼り付けます。

   ![エディターに貼り付けられた JSON を示すスクリーンショット。](./media/test-logic-apps-mock-data-static-results/json-editing-mode.png)

1. 完了したら、 **[完了]** をクリックします。 または、デザイナーに戻るには、 **[エディター モードを切り替える]** (![[エディター モードを切り替える] アイコン](./media/test-logic-apps-mock-data-static-results/switch-editor-mode-button.png)) を選択します。

### <a name="standard"></a>[Standard](#tab/standard)

1. [Azure portal](https://portal.azure.com) で、ロジック アプリ ワークフローをデザイナーで開きます。

1. ワークフロー メニューで、 **[概要]** を選択します。

1. まだ選択されていない場合は、 **[Essentials]\(要点\)** セクションの **[実行履歴]** を選択します。

1. **[実行履歴]** テーブルで、確認する実行を選択します。

   ![ワークフローの実行履歴を示すスクリーンショット。](./media/test-logic-apps-mock-data-static-results/select-run-standard.png)

1. 実行の結果ペインが開いたら、目的の出力を含むアクションを選択します。

1. **[出力]** セクションで、 **[未加工出力の表示]** を選択します。

1. **[出力]** ペインに、完全な JavaScript Object Notation (JSON) オブジェクトか、出力セクションやヘッダー セクションなどの使用する特定のサブセクションのどちらかをコピーします。

1. アクションで [モック アップ データを設定する](#enable-mock-data)方法に関する前述のセクションを確認し、手順に従ってアクションの **[Testing]\(テスト\)** タブを開きます。

1. **[Testing]\(テスト\)** タブが開いたら、次のどちらかの手順を選択します。

   * 完全な JSON オブジェクトを貼り付けるには、 **[Testing]\(テスト\)** ラベルの横にある **[JSON モードに切り替える]** (![[JSON モードに切り替える] のアイコン](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button.png)) を選択します。

     ![完全な JSON オブジェクトを貼り付ける [JSON モードに切り替える] アイコンが選択されているスクリーンショット。](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button-complete-standard.png)

   * JSON のセクションだけを貼り付けるには、そのセクションのラベル (**出力** や **ヘッダー** など) の横にある、**JSON モードに切り替える** を選択します。たとえば、以下のとおりです。

     ![JSON オブジェクトのセクションを貼り付けるための [JSON モードに切り替える] アイコンが選択されているスクリーンショット。](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button-output-standard.png)

1. JSON エディターで、以前にコピーした JSON を貼り付けます。

   ![エディターに貼り付けられた JSON を示すスクリーンショット。](./media/test-logic-apps-mock-data-static-results/json-editing-mode-standard.png)

1. 完了したら、 **[完了]** をクリックします。 または、デザイナーに戻るには、 **[エディター モードを切り替える]** (![[エディター モードを切り替える] アイコン](./media/test-logic-apps-mock-data-static-results/switch-editor-mode-button.png)) を選択します。

---

## <a name="disable-mock-data"></a>モック データを無効にする

アクションで静的な結果をオフにしても、前回の設定の値は削除されません。 そのため、同じアクションで静的な結果を再度有効にした場合は、前の値を引き続き使用することができます。

### <a name="consumption"></a>[従量課金プラン](#tab/consumption)

1. [Azure portal](https://portal.azure.com) で、ロジック アプリ ワークフローをデザイナーで開きます。 モック データを無効にするアクションを探します。

1. アクションの右上隅から、テスト ビーカー アイコンを探します: (![静的な結果のアイコン](./media/test-logic-apps-mock-data-static-results/static-result-test-beaker-icon.png)).

   ![アクションを示すスクリーンショット。テスト ビーカー アイコンが選択されています。](./media/test-logic-apps-mock-data-static-results/disable-static-result.png)

1. **[静的な結果の無効化]**  >  **[完了]** の順に選択します。

   ![[静的な結果の無効化] が選択されているスクリーンショット。](./media/test-logic-apps-mock-data-static-results/disable-static-result-button.png)

### <a name="standard"></a>[Standard](#tab/standard)

1. [Azure portal](https://portal.azure.com) で、ロジック アプリ ワークフローをデザイナーで開きます。 モック データを無効にするアクションを選択します。

1. アクションの詳細ペインで、 **[Testing]\(テスト\)** タブを選択します。

1. **[静的な結果の無効化]**  >  **[完了]** の順に選択します。

   ![Standard に対して [静的な結果の無効化] が選択されているスクリーンショット。](./media/test-logic-apps-mock-data-static-results/disable-static-result-button-standard.png)

---

## <a name="reference"></a>リファレンス

基になるワークフロー定義でのこの設定の詳細については、「[Static results - Schema reference for Workflow Definition Language (静的結果 - ワークフロー定義言語のスキーマ参照)](logic-apps-workflow-definition-language.md#static-results)」と「[runtimeConfiguration.staticResult - Runtime configuration settings (runtimeConfiguration.staticResult - ランタイム構成設定)](logic-apps-workflow-actions-triggers.md#runtime-configuration-settings)」を参照してください

## <a name="next-steps"></a>次のステップ

* [Azure Logic Apps](logic-apps-overview.md) について学習します