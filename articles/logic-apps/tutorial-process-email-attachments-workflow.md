---
title: メールと添付ファイルを処理するワークフローの作成 - Azure Logic Apps | Microsoft Docs
description: このチュートリアルでは、Azure Logic Apps、Azure Storage、Azure Functions を使ってメールと添付ファイルを処理する自動化されたワークフローの作成方法について説明します。
author: ecfan
manager: jeconnoc
editor: ''
services: logic-apps
documentationcenter: ''
ms.assetid: ''
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 01/12/2018
ms.author: LADocs; estfan
ms.openlocfilehash: 3d6d66dca06c1f34a31155a27c32bbe3e48c8aa3
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/11/2018
ms.locfileid: "35300635"
---
# <a name="process-emails-and-attachments-with-a-logic-app"></a>ロジック アプリでメールと添付ファイルを処理する

Azure Logic Apps を使うと、Azure サービスや Microsoft サービスを初めとする各種の SaaS (サービスとしてのソフトウェア) アプリとオンプレミス システムの垣根を越えてワークフローを自動化したりデータを統合したりすることができます。 このチュートリアルでは、受信メールと添付ファイルを処理する[ロジック アプリ](../logic-apps/logic-apps-overview.md)の作成方法を紹介します。 このロジック アプリは、対象のコンテンツを処理して Azure Storage に保存し、その内容の確認依頼通知を送信するものです。 

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * [Azure Storage](../storage/common/storage-introduction.md) と Storage Explorer を設定します (保存済みメールと添付ファイルのチェックに使用)。
> * メールから HTML を削除する [Azure 関数](../azure-functions/functions-overview.md)を作成します。 その関数に必要なコードは、このチュートリアルに含まれています。
> * 空のロジック アプリを作成します。
> * メールの添付ファイルを監視するトリガーを追加します。
> * メールにファイルが添付されているかどうかをチェックする条件を追加します。
> * メールにファイルが添付されていたときに Azure 関数を呼び出すアクションを追加します。
> * メールと添付ファイルのストレージ BLOB を作成するアクションを追加します。
> * メール通知を送信するアクションを追加します。

完成したロジック アプリの大まかなワークフローは、次のようになります。

![完成したロジック アプリの概観](./media/tutorial-process-email-attachments-workflow/overview.png)

Azure サブスクリプションがない場合は、始める前に<a href="https://azure.microsoft.com/free/" target="_blank">無料の Azure アカウントにサインアップ</a>してください。 

## <a name="prerequisites"></a>前提条件

* Logic Apps がサポートするメール プロバイダー (Office 365 Outlook、Outlook.com、Gmail など) のメール アカウント。 その他のプロバイダーについては、[こちらのコネクタ一覧を参照](https://docs.microsoft.com/connectors/)してください。

  このロジック アプリでは、Office 365 Outlook アカウントを使います。 
  別のメール アカウントを使う場合、おおよその手順は変わりませんが、UI の表示がやや異なることがあります。

* <a href="http://storageexplorer.com/" target="_blank">無料の Microsoft Azure Storage Explorer</a> のダウンロードとインストール。 ストレージ コンテナーが正しく設定されているかどうかをこのツールでチェックすることができます。

## <a name="sign-in-to-the-azure-portal"></a>Azure ポータルにサインインします。

Azure アカウントの資格情報で <a href="https://portal.azure.com" target="_blank">Azure Portal</a> にサインインします。

## <a name="set-up-storage-to-save-attachments"></a>添付ファイルの保存先ストレージを設定する

受信したメールと添付ファイルは、[Azure Storage コンテナー](../storage/common/storage-introduction.md)に BLOB として保存することができます。 

1. ストレージ コンテナーを作成する前に、次の設定で[ストレージ アカウントを作成](../storage/common/storage-create-storage-account.md#create-a-storage-account)します。

   | Setting | 値 | [説明] | 
   | ------- | ----- | ----------- | 
   | **名前** | attachmentstorageacct | ストレージ アカウントの名前 | 
   | **デプロイ モデル** | Resource Manager | リソースのデプロイを管理するための[デプロイメント モデル](../azure-resource-manager/resource-manager-deployment-model.md) | 
   | **アカウントの種類** | 汎用 | [ストレージ アカウントの種類](../storage/common/storage-introduction.md#types-of-storage-accounts) | 
   | **パフォーマンス** | 標準 | サポートされるデータの種類とデータの保存メディアは、この設定で指定します。 「[ストレージ アカウントの種類](../storage/common/storage-introduction.md#types-of-storage-accounts)」を参照してください。 | 
   | **レプリケーション** | ローカル冗長ストレージ (LRS) | データのコピー、保存、管理、同期の方法は、この設定で指定します。 「[レプリケーション](../storage/common/storage-introduction.md#replication)」を参照してください。 | 
   | **安全な転送が必須** | 無効 | 接続先からの要求には、この設定でセキュリティを確保することができます。 [安全な転送の義務付け](../storage/common/storage-require-secure-transfer.md)に関するページを参照してください。 | 
   | **サブスクリプション** | <*Azure サブスクリプションの名前*> | Azure サブスクリプションの名前 | 
   | **[リソース グループ]** | LA-Tutorial-RG | [Azure リソース グループ](../azure-resource-manager/resource-group-overview.md)の名前。関連するリソースをまとめて管理する目的で使われます。 <p>**注:** リソース グループは、特定のリージョン内に存在します。 このチュートリアルで使う項目が、一部のリージョンでは利用できない場合もありますが、可能な限り同じリージョンを使うようにしてください。 | 
   | **場所** | 米国東部 2 | ストレージ アカウントに関する情報の保存先となるリージョン | 
   | **仮想ネットワークの構成** | 無効 | このチュートリアルでは **[無効]** のままにしてください。 | 
   |||| 

   [Azure PowerShell](../storage/common/storage-quickstart-create-storage-account-powershell.md) または [Azure CLI](../storage/common/storage-quickstart-create-storage-account-cli.md) を使うこともできます。
  
2. 設定したストレージ アカウントが Azure によってデプロイされると、そのアクセス キーが送られてきます。

   1. ストレージ アカウントのメニューの **[設定]** から **[アクセス キー]** を選択します。 
   2. **[既定のキー]** の **[key1]** とストレージ アカウント名を探します。

      ![ストレージ アカウントの名前とキーをコピーして保存](./media/tutorial-process-email-attachments-workflow/copy-save-storage-name-key.png)

   [Azure PowerShell](https://docs.microsoft.com/powershell/module/azurerm.storage/get-azurermstorageaccountkey) または [Azure CLI](https://docs.microsoft.com/cli/azure/storage/account/keys?view=azure-cli-latest.md#az_storage_account_keys_list) を使うこともできます。 

3. メールの添付ファイルのストレージ コンテナーを作成します。
   
   1. ストレージ アカウント メニューの **[概要]** ウィンドウで、**[サービス]** から **[BLOB]** を選択し、**[+コンテナー]** を選択します。

   2. コンテナー名として「attachments」を入力します。 **[パブリック アクセス レベル]** の **[コンテナー (コンテナーと BLOB の匿名読み取りアクセス)]** を選択し、**[OK]** を選択します。

   [Azure PowerShell](https://docs.microsoft.com/powershell/module/azure.storage/new-azurestoragecontainer) または [Azure CLI](https://docs.microsoft.com/cli/azure/storage/container?view=azure-cli-latest#az_storage_container_create) を使うこともできます。 
   完了後、Azure Portal でストレージ アカウントを見ると、完成したストレージ コンテナーが見つかります。

   ![完成したストレージ コンテナー](./media/tutorial-process-email-attachments-workflow/created-storage-container.png)

次に、このストレージ アカウントに Storage Explorer を接続します。

## <a name="set-up-storage-explorer"></a>Storage Explorer を設定する

今度は、ロジック アプリによって添付ファイルが BLOB として正しくストレージ コンテナーに保存されたかどうかを確認できるよう、先ほどのストレージ アカウントに Storage Explorer を接続します。

1. Microsoft Azure Storage Explorer を開きます。 Storage Explorer から Azure Storage への接続を求めるメッセージが表示されたら、**[Use a storage account name and key]\(ストレージ アカウントの名前とキーを使用\)** > **[次へ]** の順に選択します。
メッセージが表示されなかった場合は、エクスプローラー ツール バーの **[アカウントの追加]** を選択します。

2. **[Attach using Name and Key]\(名前とキーを使用して接続\)** に、先ほど保存しておいたストレージ アカウントの名前とアクセス キーを入力します。 **[次へ]** > **[接続]** の順に選択します。

3. 自分のストレージ アカウントとコンテナーが Storage Explorer に正しく表示されることを確認します。

   1. **[エクスプローラー]** で **[(Local and Attached)]\((ローカルと接続)\)** > 
    **[ストレージ アカウント]** > **[attachmentstorageaccount]** > 
    **[BLOB コンテナー]** の順に展開します。

   2. "attachments" コンテナーが表示されていることを確認します。 
   例: 

      ![Storage Explorer - ストレージ コンテナーの確認](./media/tutorial-process-email-attachments-workflow/storage-explorer-check-contianer.png)

次に、受信メールから HTML を削除する [Azure 関数](../azure-functions/functions-overview.md)を作成します。

## <a name="create-a-function-to-clean-html"></a>HTML を削除する関数を作成する

今度は、この手順で紹介するコード スニペットを使って、個々の受信メールから HTML を削除する Azure 関数を作成します。 こうすることでメールから余分な要素が取り除かれ、コンテンツが処理しゃすくなります。 この関数は、ロジック アプリから呼び出すことができます。

1. 関数を作成する前に、次の設定で[関数アプリを作成](../azure-functions/functions-create-function-app-portal.md)します。

   | Setting | 値 | 説明 | 
   | ------- | ----- | ----------- | 
   | **アプリ名** | CleanTextFunctionApp | 関数アプリの名前。グローバルに一意で、かつわかりやすい名前を付けます。 | 
   | **サブスクリプション** | <*Azure サブスクリプションの名前*> | 先ほど使用したものと同じ Azure サブスクリプション | 
   | **リソース グループ** | LA-Tutorial-RG | 先ほど使用したものと同じ Azure リソース グループ | 
   | **ホスティング プラン** | 従量課金プラン | 関数アプリを実行するためのリソース (計算処理能力など) の割り当てとスケールの方法は、この設定によって決まります。 [ホスティング プランの比較](../azure-functions/functions-scale.md)に関するページを参照してください。 | 
   | **場所** | 米国東部 2 | 先ほど使用したものと同じリージョン | 
   | **Storage** | cleantextfunctionstorageacct | 関数アプリに使うストレージ アカウントを作成します。 使用できるのは小文字と数字だけです。 <p>**注:** このストレージ アカウントは関数アプリを格納するものであり、先ほどメールの添付ファイルを保存する目的で作成したストレージ アカウントとは異なります。 | 
   | **Application Insights** | オフ | [Application Insights](../application-insights/app-insights-overview.md) を使ったアプリケーションの監視を有効にします。ただしこのチュートリアルでは、この設定を **[オフ]** のままにしてください。 | 
   |||| 

   デプロイ後に関数アプリが自動的に表示されない場合は、<a href="https://portal.azure.com" target="_blank">Azure Portal</a> でアプリを探してください。 Azure のメイン メニューで **[App Services]** を選択し、目的の関数アプリを選択します。

   ![作成された関数アプリ](./media/tutorial-process-email-attachments-workflow/function-app-created.png)

   Azure メニューに **[App Services]** が表示されない場合は、**[その他のサービス]** に移動してください。 検索ボックスで **[Function App]** を探して選択します。 詳細については、[関数の作成](../azure-functions/functions-create-first-azure-function.md)に関するページを参照してください。

   また、[Azure CLI](../azure-functions/functions-create-first-azure-function-azure-cli.md) のほか、[PowerShell と Resource Manager テンプレート](../azure-resource-manager/resource-group-template-deploy.md)を使用することもできます。

2. **[Function App]** の **[CleanTextFunctionApp]** を展開し、**[関数]** を選択します。 関数ツール バーの **[+ 新しい関数]** を選択します。

   ![新しい関数の作成](./media/tutorial-process-email-attachments-workflow/function-app-new-function.png)

3. **[Choose a template below or go to the quickstart]\(下のテンプレートを選択するか、クイックスタートに移動してください\)** で、**[HttpTrigger - C#]** 関数テンプレートを選択します。

   ![関数テンプレートの選択](./media/tutorial-process-email-attachments-workflow/function-select-httptrigger-csharp-function-template.png)

4. **[関数名の指定]** に「```RemoveHTMLFunction```」と入力します。 **[HTTP trigger]\(HTTP トリガー\)** > **[承認レベル]** は既定値の **Function** のままにして、**[作成]** を選択します。

   ![関数名の指定](./media/tutorial-process-email-attachments-workflow/function-provide-name.png)

5. エディターが開いたら、テンプレートのコードを次のコードで置き換えます。このコードによって HTML が削除され、その結果が呼び出し元に返されます。

   ``` CSharp
   using System.Net;
   using System.Text.RegularExpressions;

   public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
   {
      log.Info($"HttpWebhook triggered");

      // Parse query parameter
      string emailBodyContent = await req.Content.ReadAsStringAsync();

      // Replace HTML with other characters
      string updatedBody = Regex.Replace(emailBodyContent, "<.*?>", string.Empty);
      updatedBody = updatedBody.Replace("\\r\\n", " ");
      updatedBody = updatedBody.Replace(@"&nbsp;", " ");

      // Return cleaned text
      return req.CreateResponse(HttpStatusCode.OK, new { updatedBody });

   }
   ```

6. 完了したら、**[保存]** を選択します。 関数をテストするには、エディター右端の矢印 (**<**) アイコン下にある **[テスト]** を選択します。 

   ![[テスト] ウィンドウを開く](./media/tutorial-process-email-attachments-workflow/function-choose-test.png)

7. **[テスト]** ウィンドウで **[要求本文]** の下に次の行を入力し、**[実行]** を選択します。

   ```json
   {"name": "<p><p>Testing my function</br></p></p>"}
   ```

   ![関数をテストする](./media/tutorial-process-email-attachments-workflow/function-run-test.png)

   **[出力]** ウィンドウに、この関数から返された結果が次のように表示されます。

   ```json
   {"updatedBody":"{\"name\": \"Testing my function\"}"}
   ```

関数が正しく機能していることが確認できたら、ロジック アプリを作成します。 このチュートリアルは、メールから HTML を削除する関数の作成方法を紹介していますが、**HTML をテキストに変換**するコネクタは Logic Apps にも用意されています。

## <a name="create-your-logic-app"></a>ロジック アプリを作成する

1. Azure のメイン メニューで、**[リソースの作成]** > **[Enterprise Integration]** > **[Logic App]** の順に選択します。

   ![ロジック アプリを作成する](./media/tutorial-process-email-attachments-workflow/create-logic-app.png)

2. **[ロジック アプリの作成]** で、ロジック アプリに関する情報を次のように入力します。 終了したら、**[ダッシュボードにピン留めする]** > **[作成]** を選びます。

   ![ロジック アプリに関する情報の入力](./media/tutorial-process-email-attachments-workflow/create-logic-app-settings.png)

   | Setting | 値 | [説明] | 
   | ------- | ----- | ----------- | 
   | **名前** | LA-ProcessAttachment | ロジック アプリの名前 | 
   | **サブスクリプション** | <*Azure サブスクリプションの名前*> | 先ほど使用したものと同じ Azure サブスクリプション | 
   | **[リソース グループ]** | LA-Tutorial-RG | 先ほど使用したものと同じ Azure リソース グループ |
   | **場所** | 米国東部 2 | 先ほど使用したものと同じリージョン | 
   | **Log Analytics** | オフ | このチュートリアルでは **[オフ]** のままにしてください。 | 
   |||| 

3. アプリのデプロイ後、Logic Apps デザイナーが起動し、使用頻度の高いロジック アプリのパターンのテンプレートや紹介ビデオを掲載したページが表示されます。 **[テンプレート]** で **[空のロジック アプリ]** を選択します。

   ![空のロジック アプリ テンプレートを選択する](./media/tutorial-process-email-attachments-workflow/choose-logic-app-template.png)

次に、添付ファイルを含んだ受信メールをリッスンする[トリガー](../logic-apps/logic-apps-overview.md#logic-app-concepts)を追加します。 すべてのロジック アプリは必ずトリガーから起動され、トリガーは、特定のイベントが発生するか、新しいデータが特定の条件を満たしたときに起動されます。 詳細については、[初めてのロジック アプリの作成](../logic-apps/quickstart-create-first-logic-app-workflow.md)に関する記事を参照してください。

## <a name="monitor-incoming-email"></a>受信メールを監視する

1. デザイナーの検索ボックスに「メールが届いたとき」と入力します。 メール プロバイダーに対応するトリガーとして、たとえば **[<*your-email-provider*> - 新しい電子メールが届いたとき]** を選択します。

   ![メール プロバイダーの "新しい電子メールが届いたとき" トリガーを選択](./media/tutorial-process-email-attachments-workflow/add-trigger-when-email-arrives.png)

   * Azure の職場または学校アカウントには、Office 365 Outlook を選択します。 
   * 個人用 Microsoft アカウントには、Outlook.com を選択します。 

2. 資格情報を求められた場合は、お使いのメール アカウントに Logic Apps が接続できるよう、そのメール アカウントにサインインします。

3. 新しいメールをフィルター選択するためにトリガーで使う基準を指定します。

   1. メールをチェックするフォルダー、間隔、頻度を指定します。

      ![メールをチェックするフォルダー、間隔、頻度を指定](./media/tutorial-process-email-attachments-workflow/set-up-email-trigger.png)

      | Setting | 値 | 説明 | 
      | ------- | ----- | ----------- | 
      | **フォルダー** | Inbox | チェックするメール フォルダー | 
      | **間隔** | 1 | チェックの間隔 (単位数) | 
      | **頻度** | [分] | チェックの間隔に使う時間の単位 | 
      |  |  |  | 
  
   2. **[詳細オプションを表示する]** を選択し、次の設定を指定します。

      | Setting | 値 | 説明 | 
      | ------- | ----- | ----------- | 
      | **添付ファイルあり** | [はい] | ファイルが添付されているメールのみ取得します。 <p>**注:** このトリガーは、お使いのアカウントからメールを削除することはありません。新着メッセージだけをチェックし、件名フィルターに一致するメールだけを処理します。 | 
      | **添付ファイルを含める** | [はい] | 添付ファイルの有無をチェックするだけでなく、ワークフローの入力として添付ファイルを取得します。 | 
      | **件名フィルター** | ```Business Analyst 2 #423501``` | メールの件名から探すテキスト | 
      |  |  |  | 

4. ここではトリガーの詳細を非表示にするために、トリガーのタイトル バー内をクリックします。

   ![シェイプを折りたたんで詳細を非表示](./media/tutorial-process-email-attachments-workflow/collapse-trigger-shape.png)

5. ロジック アプリを保存し、 デザイナーのツール バーで、**[保存]** を選択します。

   この時点でロジック アプリは稼働していますが、メールをチェックすること以外は何もしていません。 
   次の手順で、ワークフローの続行基準を指定する条件を追加します。

## <a name="check-for-attachments"></a>添付ファイルをチェックする

1. トリガーの下で、**[+ 新しいステップ]** > **[条件の追加]** の順に選択します。

   条件のシェイプが表示されると、パラメーター リストまたは動的コンテンツ リストが既定で表示され、先行するステップからワークフローへの入力として追加できるパラメーターがすべて表示されます。 
   どちらのリストが表示されるかは、お使いのブラウザーの幅によって決まります。

2. 条件の名前をわかりやすい名前に変更します。

   1. 条件のタイトル バーにある**省略記号** (**...**) ボタンを選択し、**[名前の変更]** を選択します。

      たとえばブラウザーの表示幅を狭くしている場合は、次のように表示されます。

      ![条件の名前の変更](./media/tutorial-process-email-attachments-workflow/condition-rename.png)

      ブラウザーの表示幅を広くしていて、省略記号ボタンが動的コンテンツ リストの影に隠れてクリックできない場合は、[条件] 内の **[動的なコンテンツの追加]** を選択して、このリストを閉じてください。 
      
      ![動的コンテンツ リストを閉じる](./media/tutorial-process-email-attachments-workflow/close-dynamic-content-list.png)

   2. 条件の名前をわかりやすく「```If email has attachments and key subject phrase```」に変更します。

3. 式を入力して条件を記述します。 

   1. 条件のシェイプ内の **[詳細設定モードで編集]** を選択します。

      ![詳細設定モードで条件を編集](./media/tutorial-process-email-attachments-workflow/edit-advanced-mode.png)

   2. テキスト ボックスに次の式を入力します。

      ```@equals(triggerBody()?['HasAttachment'], bool('true'))```

      この式は、トリガー本体 (このチュートリアルではメール) の **HasAttachment** プロパティの値と Boolean オブジェクト ```True``` とを比較しています。 
      2 つの値が等しい場合、メールには少なくとも 1 つの添付ファイルが存在します。この場合、条件が成立してワークフローが続行されることになります。

      条件は次の例のようになります。

      ![条件式](./media/tutorial-process-email-attachments-workflow/condition-expression.png)

   3. **[基本モードで編集]** を選択します。 先ほどの式が、次のように解決されます。

      ![解決後の式](./media/tutorial-process-email-attachments-workflow/condition-expression-resolved.png)

      > [!NOTE]
      > 式を手動で作成するには、式ビルダーで編集できるよう、基本モードにしたうえで動的リストを開いた状態にしておく必要があります。 **[式]** では、関数を選択することができます。 それらの関数で使うパラメーターのフィールドは、**[動的なコンテンツ]** で選択できます。
      > 式を手動で作成する方法については、後でこのチュートリアルの中で説明します。

4. ロジック アプリを保存し、

### <a name="test-your-condition"></a>条件をテストする

ここで、条件が正しく機能するかどうかをテストします。

1. ロジック アプリをまだ実行していない場合は、デザイナーのツール バーの **[実行]** を選択します。

   この手順では、間隔に指定した時間が経過するまで待たずに済むよう、ロジック アプリを手動で開始します。 
   ただし、テスト メールが受信トレイに着信するまでは何も起こりません。 

2. 次の基準を満たすメールをご自身で送信してください。

   * トリガーの **[件名フィルター]** に指定したテキスト (```Business Analyst 2 #423501```) をメールの件名に含めます。

   * メールに添付ファイルを 1 つ追加します。 
   ここでは、空のテキスト ファイルを作成してメールに添付するだけでかまいません。

   メールが着信すると、添付ファイルと指定された件名テキストの存在がロジック アプリによってチェックされます。
   この条件にパスした場合、トリガーが起動し、Logic Apps エンジンによって、ロジック アプリのインスタンスが作成され、ワークフローが開始されます。 

3. トリガーが起動してロジック アプリが正常に実行されたことを確認するには、ロジック アプリのメニューから **[概要]** を選択します。

   ![トリガーと実行履歴の確認](./media/tutorial-process-email-attachments-workflow/checkpoint-run-history.png)

   ロジック アプリのトリガーが起動しなかった場合や、トリガーが正常に起動したにもかかわらずロジック アプリが実行されなかった場合は、[ロジック アプリのトラブルシューティング](../logic-apps/logic-apps-diagnosing-failures.md)に関するページを参照してください。

次に、**[true の場合]** 分岐で実行するアクションを定義します。 添付ファイルと一緒にメールを保存するために、メール本文から HTML をすべて削除したうえで、メールと添付ファイル用の BLOB をストレージ コンテナーに作成します。

> [!NOTE]
> メールにファイルが 1 つも添付されていなかったときの **[false の場合]** の分岐処理は、このロジック アプリには必要ありません。 **[false の場合]** の分岐には、このチュートリアルの後、補足的な演習として適宜アクションを追加してみてください。

## <a name="call-the-removehtmlfunction"></a>RemoveHTMLFunction を呼び出す

1. ロジック アプリのメニューから **[ロジック アプリ デザイナー]** を選択します。 **[true の場合]** 分岐の **[アクションの追加]** を選択します。

2. "azure functions" を検索し、**[Azure Functions - Azure 関数を選択する]** のアクションを選択します。

   !["Azure Functions - Azure 関数を選択する" のアクションを選択](./media/tutorial-process-email-attachments-workflow/add-action-azure-function.png)

3. あらかじめ作成しておいた関数アプリ **CleanTextFunctionApp** を選択します。

   ![Azure 関数アプリを選択](./media/tutorial-process-email-attachments-workflow/add-action-select-azure-function-app.png)

4. **RemoveHTMLFunction** 関数を選択します。

   ![Azure 関数を選択](./media/tutorial-process-email-attachments-workflow/add-action-select-azure-function.png)

5. 関数シェイプの名前を「```Call RemoveHTMLFunction to clean email body```」のように変更します。 

6. 関数で処理する入力内容を関数シェイプに入力します。 以下の説明に従ってメールの本文を指定してください。

   ![関数に渡す要求本文を指定](./media/tutorial-process-email-attachments-workflow/add-email-body-for-function-processing.png)

   1. **[要求本文]** に次のテキストを入力します。 
   
      ```{ "emailBody": ``` 

      次の手順で入力内容が確定するまで、無効な JSON に関するエラーが表示されます。
      最初にこの関数をテストしたとき、関数に渡す入力として JavaScript Object Notation (JSON) 形式を用いました。 
      したがって要求本文にも同じ形式を用いる必要があります。 

   2. パラメーター リストまたは動的コンテンツ リストから、**[新しい電子メールが届いたとき]** の **[Body]\(本文\)** フィールドを選択します。
   **[Body]\(本文\)** フィールドに続けて、閉じ中かっこ (```}```) を追加します。

      ![関数に渡す要求本文を指定](./media/tutorial-process-email-attachments-workflow/add-email-body-for-function-processing2.png)

      ロジック アプリの定義には、この入力内容が次の形式で表示されます。

      ```{ "emailBody": "@triggerBody()?['Body']" }```

7. ロジック アプリを保存し、

次に、メールの本文を保存するための BLOB をストレージ コンテナーに作成するアクションを追加します。

## <a name="create-blob-for-email-body"></a>メールの本文に使う BLOB を作成する

1. Azure 関数のシェイプで **[アクションの追加]** を選択します。 

2. **[アクションを選択してください]** で "blob" を検索し、**[Azure Blob Storage - BLOB の作成]** アクションを選択します。

   ![メールの本文に使う BLOB の作成アクションを追加](./media/tutorial-process-email-attachments-workflow/create-blob-action-for-email-body.png)

3. まだ Azure Storage アカウントに接続していない場合は、次の設定に従って必要なストレージ アカウントへの接続を作成します。 操作が完了したら、**[作成]** を選択します。

   ![ストレージ アカウントへの接続を作成](./media/tutorial-process-email-attachments-workflow/create-storage-account-connection-first.png)

   | Setting | 値 | [説明] | 
   | ------- | ----- | ----------- | 
   | **Connection Name** | AttachmentStorageConnection | 接続の名前。わかりやすい名前を付けます。 | 
   | **ストレージ アカウント** | attachmentstorageacct | 添付ファイルの保存用に作成しておいたストレージ アカウントの名前 | 
   |||| 

4. **[BLOB の作成]** アクションの名前をわかりやすく「```Create blob for email body```」に変更します。

5. **[BLOB の作成]** アクションに、次の情報を入力し、BLOB を作成するために必要な各パラメーターを選択します。次の例を参照してください。

   ![メールの本文に使う BLOB 情報の指定](./media/tutorial-process-email-attachments-workflow/create-blob-for-email-body.png)

   | Setting | 値 | 説明 | 
   | ------- | ----- | ----------- | 
   | **フォルダー パス** | /attachments | あらかじめ作成しておいたコンテナーのパスと名前。 コンテナーを参照して選択することもできます。 | 
   | **BLOB 名** | **[差出人]** フィールド | BLOB 名としてメールの送信者名を渡します。 パラメーター リストまたは動的コンテンツ リストから、**[新しい電子メールが届いたとき]** の **[差出人]** を選択します。 | 
   | **BLOB コンテンツ** | **[コンテンツ]** フィールド | HTML を除去したメールの本文を BLOB コンテンツとして渡します。 パラメーター リストまたは動的コンテンツ リストから、**[Call RemoveHTMLFunction to clean email body]** の **[Body]\(本文\)** を選択します。 |
   |||| 

6. ロジック アプリを保存し、 

### <a name="check-attachment-handling"></a>添付ファイルの処理を確認する

指定したとおりにロジック アプリでメールが処理されるかどうかのテストを行います。

1. ロジック アプリをまだ実行していない場合は、デザイナーのツール バーの **[実行]** を選択します。

2. 次の基準を満たすメールをご自身で送信してください。

   * トリガーの **[件名フィルター]** に指定したテキスト (```Business Analyst 2 #423501```) をメールの件名に含めます。

   * メールに添付ファイルを少なくとも 1 つ追加します。 
   ここでは、空のテキスト ファイルを作成してメールに添付するだけでかまいません。

   * 何らかのテスト コンテンツをメールの本文に含めます。以下はその例です。 

     ```
     Testing my logic app
     ```

   ロジック アプリのトリガーが起動しなかった場合や、トリガーが正常に起動したにもかかわらずロジック アプリが実行されなかった場合は、[ロジック アプリのトラブルシューティング](../logic-apps/logic-apps-diagnosing-failures.md)に関するページを参照してください。

3. ロジック アプリによって適切なストレージ コンテナーにメールが保存されたことを確認します。 

   1. Storage Explorer で **[(Local and Attached)]\((ローカルと接続)\)**> 
    **[ストレージ アカウント]** > **[attachmentstorageacct (External)]\(attachmentstorageacct (外部)\)** > 
    **[BLOB コンテナー]** > **[添付ファイル]** の順に展開します。

   2. メールの **attachments** コンテナーを確認します。 

      現時点では、コンテナー内のメールが表示されるだけです。これは添付ファイルがまだロジック アプリで処理されていないためです。

      ![保存されているメールの存在を Storage Explorer で確認](./media/tutorial-process-email-attachments-workflow/storage-explorer-saved-email.png)

   3. ここまで終えたら Storage Explorer からメールを削除します。

4. **[false の場合]** 分岐については、現時点では何も実行されません。必要に応じて、基準を満たさないメールを送信して、この分岐をテストしてください。

次に、メールの添付ファイルをすべて処理するためのループを追加します。

## <a name="process-attachments"></a>添付ファイルを処理する

このロジック アプリでは、**for each** ループを使って、メールの添付ファイルを 1 つずつ処理します。

1. **[Create blob for email body]** シェイプの **[… More]\(さらに表示\)** を選択し、**[For Each の追加]** コマンドを選択します。

   !["for each" ループを追加](./media/tutorial-process-email-attachments-workflow/add-for-each-loop.png)

2. ループの名前をわかりやすく「```For each email attachment```」に変更します。

3. ループで処理するデータを指定します。 **[以前の手順から出力を選択]** ボックス内をクリックします。 パラメーター リストまたは動的コンテンツ リストから **[添付ファイル]** を選択します。 

   ![[添付ファイル] を選択](./media/tutorial-process-email-attachments-workflow/select-attachments.png)

   メールに添付されているすべてのファイルの配列が、**[添付ファイル]** フィールドから渡されます。 
   その配列で渡された各要素に対するアクションが、**For each** ループで反復実行されます。

4. ロジック アプリを保存し、

次に、個々の添付ファイルを BLOB として **attachments** ストレージ コンテナーに保存するアクションを追加します。

## <a name="create-blobs-for-attachments"></a>添付ファイルに使う BLOB を作成する

1. 検出された各添付ファイルに対して実行するタスクを指定するために、**For each** ループの **[アクションの追加]** を選択します。

   ![ループへのアクションの追加](./media/tutorial-process-email-attachments-workflow/for-each-add-action.png)

2. **[アクションを選択してください]** で "blob" を検索し、**[Azure Blob Storage - BLOB の作成]** アクションを選択します。

   ![BLOB の作成アクションを追加](./media/tutorial-process-email-attachments-workflow/create-blob-action-for-attachments.png)

3. **[Create blob 2]\(BLOB の作成 2\)** アクションの名前をわかりやすく「```Create blob for each email attachment```」に変更します。

4. **[Create blob for each email attachment]** アクションに次の情報を入力し、個々の BLOB を作成するためのパラメーターを選択します。次の例を参照してください。

   ![BLOB 情報の指定](./media/tutorial-process-email-attachments-workflow/create-blob-per-attachment.png)

   | Setting | 値 | 説明 | 
   | ------- | ----- | ----------- | 
   | **フォルダー パス** | /attachments | あらかじめ作成しておいたコンテナーのパスと名前。 コンテナーを参照して選択することもできます。 | 
   | **BLOB 名** | **[名前]** フィールド | BLOB 名には、パラメーター リストまたは動的コンテンツ リストから **[名前]** を選択し、添付ファイルの名前を渡します。 | 
   | **BLOB コンテンツ** | **[コンテンツ]** フィールド | BLOB コンテンツには、パラメーター リストまたは動的コンテンツ リストから **[コンテンツ]** を選択し、添付ファイルの名前を渡します。 |
   |||| 

5. ロジック アプリを保存し、 

### <a name="check-attachment-handling"></a>添付ファイルの処理を確認する

次に、指定したとおりにロジック アプリで添付ファイルが処理されるかどうかのテストを行います。

1. ロジック アプリをまだ実行していない場合は、デザイナーのツール バーの **[実行]** を選択します。

2. 次の基準を満たすメールをご自身で送信してください。

   * トリガーの **[件名フィルター]** に指定したテキスト (```Business Analyst 2 #423501```) をメールの件名に含めます。

   * メールには、添付ファイルを少なくとも 2 つ含めます。 
   ここでは、空のテキスト ファイルを 2 つ作成してメールに添付するだけでかまいません。

   ロジック アプリのトリガーが起動しなかった場合や、トリガーが正常に起動したにもかかわらずロジック アプリが実行されなかった場合は、[ロジック アプリのトラブルシューティング](../logic-apps/logic-apps-diagnosing-failures.md)に関するページを参照してください。

3. ロジック アプリによって、適切なストレージ コンテナーにメールと添付ファイルが保存されたことを確認します。 

   1. Storage Explorer で **[(Local and Attached)]\((ローカルと接続)\)**> 
    **[ストレージ アカウント]** > **[attachmentstorageacct (External)]\(attachmentstorageacct (外部)\)** > 
    **[BLOB コンテナー]** > **[添付ファイル]** の順に展開します。

   2. メールと添付ファイルが両方とも存在することを **attachments** コンテナーで確認します。

      ![保存されたメールと添付ファイルの確認](./media/tutorial-process-email-attachments-workflow/storage-explorer-saved-attachments.png)

   3. ここまで終えたら Storage Explorer からメールと添付ファイルを削除します。

次に、添付ファイルの確認依頼メールをロジック アプリから送信するためのアクションを追加します。

## <a name="send-email-notifications"></a>メール通知を送信する

1. **[true の場合]** 分岐の **[For each email attachment]** ループで **[アクションの追加]** を選択します。 

   !["for each" ループでのアクションの追加](./media/tutorial-process-email-attachments-workflow/add-action-send-email.png)

2. **[アクションを選択してください]** で「メールの送信」を検索し、目的のメール プロバイダーの "メールの送信" アクションを選択します。 アクション一覧をフィルタリングしてサービスを絞り込み、**[コネクタ]** の一番上に表示されるコネクタを選択してください。

   ![該当するメール プロバイダーの "メールの送信" アクションを選択](./media/tutorial-process-email-attachments-workflow/add-action-select-send-email.png)

   * Azure の職場または学校アカウントには、Office 365 Outlook を選択します。 
   * 個人用 Microsoft アカウントには、Outlook.com を選択します。 

3. 資格情報を求められた場合は、お使いのメール アカウントへの接続が Logic Apps によって作成されるように、そのメール アカウントにサインインします。

4. **[電子メールの送信]** アクションの名前をわかりやすく「```Send email for review```」に変更します。

5. 次の例に従って、このアクションの情報を入力し、メールに含めるフィールドを選択します。 編集ボックスで空白行を追加するには、Shift + Enter キーを押します。  

   動的コンテンツ リストを使って編集する場合の例を次に示します。

   ![メール通知を送信](./media/tutorial-process-email-attachments-workflow/send-email-notification.png)

   必要なフィールドがリストに見つからない場合は、動的コンテンツ リストの **[新しい電子メールが届いたとき]** の横またはパラメーター リストの最後にある **[See more]\(詳細表示\)** を選択します。

   | Setting | 値 | メモ | 
   | ------- | ----- | ----- | 
   | **To** | <*recipient-email-address*> | テスト目的で自分の電子メール アドレスを使用できます。 | 
   | **[件名]**  | ```ASAP - Review applicant for position: ``` **件名** | メールに付ける件名。 パラメーター リストまたは動的コンテンツ リストから、**[新しい電子メールが届いたとき]** の **[件名]** フィールドを選択します。 | 
   | **本文** | ```Please review new applicant:``` <p>```Applicant name: ``` **差出人** <p>```Application file location: ``` **パス** <p>```Application email content: ``` **本文** | メールの本文の内容。 パラメーター リストまたは動的コンテンツ リストから次のフィールドを選択します。 <p>- **[新しい電子メールが届いたとき]** の **[差出人]** フィールド </br>- **[Create blob for email body]** の **[パス]** フィールド </br>- **[Call RemoveHTMLFunction to clean email body]** の **[Body]\(本文\)** フィールド | 
   |||| 

   **[コンテンツ]** (添付ファイルを保持する配列) など、配列が格納されているフィールドを選択すると、そのフィールドを参照するアクションに "For each" ループが自動的に追加されます。 
   こうすることで、ロジック アプリは、そのアクションを各配列項目に対して実行できます。 
   ループを削除するには、配列のフィールドを削除し、その参照アクションをループの外に移動して、ループのタイトル バーにある省略記号 (**...**) を選択し、**[削除]** を選択します。
     
6. ロジック アプリを保存し、 

次に、ロジック アプリをテストします。この時点で、ロジック アプリは次のようになっています。

![完成したロジック アプリ](./media/tutorial-process-email-attachments-workflow/complete.png)

## <a name="run-your-logic-app"></a>ロジック アプリを実行する

1. 次の基準を満たすメールをご自身で送信してください。

   * トリガーの **[件名フィルター]** に指定したテキスト (```Business Analyst 2 #423501```) をメールの件名に含めます。

   * メールに添付ファイルを少なくとも 1 つ追加します。 
   先ほどのテストで使った空のテキスト ファイルを再利用してかまいません。 
   より実用的な使い方を想定するならば、履歴書のファイルを添付します。

   * メールの本文には次のテキストを記述します。これをコピーして貼り付けてください。

     ```
     Name: Jamal Hartnett   
     
     Street address: 12345 Anywhere Road   
     
     City: Any Town   
     
     State or Country: Any State   
     
     Postal code: 00000   
     
     Email address: jamhartnett@outlook.com   
     
     Phone number: 000-000-0000   
     
     Position: Business Analyst 2 #423501   

     Technical skills: Dynamics CRM, MySQL, Microsoft SQL Server, JavaScript, Perl, Power BI, Tableau, Microsoft Office: Excel, Visio, Word, PowerPoint, SharePoint, and Outlook   

     Professional skills: Data, process, workflow, statistics, risk analysis, modeling; technical writing, expert communicator and presenter, logical and analytical thinker, team builder, mediator, negotiator, self-starter, self-managing  
     
     Certifications: Six Sigma Green Belt, Lean Project Management   
     
     Language skills: English, Mandarin, Spanish   
     
     Education: Master of Business Administration   
     ```

2. ロジック アプリを実行する 正常に実行された場合、次のようなメールがロジック アプリから送信されます。

   ![ロジック アプリから送信されたメール通知](./media/tutorial-process-email-attachments-workflow/email-notification.png)

   メールが届かない場合は、迷惑メール フォルダーを確認してください。 
   この種のメールは、迷惑メール フィルターによってリダイレクトされる場合があります。 
   また、ロジック アプリが正しく動作しているかどうかわからない場合は、[ロジック アプリのトラブルシューティング](../logic-apps/logic-apps-diagnosing-failures.md)に関するページを参照してください。

お疲れさまでした。各種 Azure サービスの垣根を越えてタスクを自動化し、カスタム コードを呼び出すロジック アプリを作成し、実行することができました。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

必要がなくなったら、ロジック アプリと関連リソースを含んだリソース グループを削除しましょう。 Azure のメイン メニューから **[リソース グループ]** に移動し、対象のロジック アプリのリソース グループを選択します。 **[リソース グループの削除]** を選択します。 確認のためにリソース グループ名を入力し、**[削除]** を選択します。

![ロジック アプリのリソース グループを削除](./media/tutorial-process-email-attachments-workflow/delete-resource-group.png)

## <a name="get-support"></a>サポートを受ける

* 質問がある場合は、[Azure Logic Apps フォーラム](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)にアクセスしてください。
* 機能のアイデアについて投稿や投票を行うには、[Logic Apps のユーザー フィードバック サイト](http://aka.ms/logicapps-wish)にアクセスしてください。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、Azure Storage と Azure Functions など各種 Azure サービスを統合することによって、メールの添付ファイルを処理して保存するロジック アプリを作成しました。 ロジック アプリの作成に使うことのできるコネクタは他にもあります。それらのコネクターについて詳しく見ていきましょう。

> [!div class="nextstepaction"]
> [Logic Apps 用コネクタの詳細情報](../connectors/apis-list.md)
