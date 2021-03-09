---
title: クイック スタート:Visual Studio を使用して Azure で初めての関数を作成する
description: このクイックスタートでは、Visual Studio を使用して、HTTP によってトリガーされる Azure Function を作成して発行する方法について学習します。
ms.assetid: 82db1177-2295-4e39-bd42-763f6082e796
ms.topic: quickstart
ms.date: 09/30/2020
ms.custom: devx-track-csharp, mvc, devcenter, vs-azure, 23113853-34f2-4f
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 9e3144738bd259ab9be75059af00f125581bb37c
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2021
ms.locfileid: "102050122"
---
# <a name="quickstart-create-your-first-function-in-azure-using-visual-studio"></a>クイック スタート:Visual Studio を使用して Azure で初めての関数を作成する

この記事では、Visual Studio を使用して、HTTP 要求に応答する C# クラス ライブラリベースの関数を作成します。 コードをローカルでテストした後、デプロイします。 <abbr title="コードをデプロイして管理するプロセスは、ランタイムのコンピューティング環境によって省力化されています。アプリケーション開発者がサーバーの細部を意識する必要は一切ありません。">サーバーレス</abbr> 環境 <abbr title="アプリケーションのための低コストのサーバーレス コンピューティング環境を提供する Azure のサービス。">Azure Functions</abbr>.

このクイックスタートを完了すると、わずかな (数セント未満の) コストが発生します。 <abbr title="Azure の使用に関する課金情報を保持するプロファイル。">Azure アカウント</abbr>.

## <a name="1-prepare-your-environment"></a>1.環境を準備する

+ アクティブなサブスクリプションがある Azure アカウントを作成します。 <abbr title="Azure の使用に関する課金情報を保持するプロファイル。">account</abbr> with an active <abbr title="Azure 内のリソース管理に使用される基本的な組織構造。通常、組織内の部門または個人に関連付けられます。">subscription</abbr>. [無料でアカウントを作成できます](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。

+ [Visual Studio 2019](https://azure.microsoft.com/downloads/) をインストールします。インストールの過程で **[Azure の開発]** ワークロードを選択してください。 

![Azure 開発ワークロードと共に Visual Studio をインストールする](media/functions-create-your-first-function-visual-studio/functions-vs-workloads.png)

<br/>
<details>
<summary><strong>代わりに Azure Functions プロジェクトを作成する</strong></summary>
代わりに Visual Studio 2017 を使用して <abbr title="個別に存在する 1 つまたは複数の関数に使用される論理上のコンテナー。これらをまとめてデプロイして管理することができます。">Azure Functions プロジェクト</abbr> を作成する場合は、まず、[最新の Azure Functions ツール](functions-develop-vs.md#check-your-tools-version)をインストールする必要があります。
</details>

## <a name="2-create-a-function-app-project"></a>2. 関数アプリ プロジェクトを作成する

1. Visual Studio メニューで、 **[ファイル]**  >  **[新規]**  >  **[プロジェクト]** を選択します。

1. **[新しいプロジェクトの作成]** の検索ボックスに「*functions*」と入力し、**Azure Functions** テンプレートを選択してから、 **[次へ]** を選択します。

1. **[新しいプロジェクトの構成]** で、 **<abbr title="関数アプリ名は、C# 名前空間として有効である必要があります。そのため、アンダースコア、ハイフン、その他の英数字以外の文字は使用しないでください。"></abbr>** プロジェクトの [プロジェクト名] を入力し、 **[作成]** を選択します。 

1. **[新しい Azure Functions アプリケーションの作成]** 設定に次の情報を入力します。

    + **<abbr title="この値は、.NET Core 3.x をサポートする Azure Functions のバージョン 3.x ランタイムを使用する関数プロジェクトを作成します。Azure Functions 1.x では、.NET Framework がサポートされます。">[Azure Functions v3 (.NET Core)]</abbr>** を [Functions runtime]\(Functions ランタイム\) ドロップダウンから選択します。 (詳細については、「[Azure Functions ランタイム バージョンの概要](functions-versions.md)」を参照してください。)
    
    + 関数テンプレートとして **<abbr title="この値は、HTTP 要求によってトリガーされる関数を作成します。">[HTTP トリガー]</abbr>** を選択します。
    
    + **<abbr title="Azure Function にはストレージ アカウントが必要であるため、プロジェクトを Azure に発行するときに割り当てられるか、作成されます。HTTP トリガーによって、Azure Storage アカウントの接続文字列が使用されることはありません。その他のすべてのトリガーの種類には、有効な Azure Storage アカウントの接続文字列が必要です。">[ストレージ エミュレーター]</abbr>** を [ストレージ アカウント] ボックスの一覧から選択します。
        
    + [承認レベル] ボックスの一覧から **[匿名]** を選択します。 <abbr title="作成される関数を、すべてのクライアントがキーを使用せずにトリガーできます。 この承認設定により、新しい関数のテストが容易になります。">承認レベル</abbr> 。 (キーと承認の詳細については、「[承認キー](functions-bindings-http-webhook-trigger.md#authorization-keys)」と [HTTP と Webhook のバインド](functions-bindings-http-webhook.md)に関するページをご覧ください。)

    + **[作成]** を選択します。
        
## <a name="3-rename-the-function"></a>3. フォルダーの名前を変更する

`FunctionName` メソッド属性は、関数の名前を設定します。これは、既定では `Function1` として生成されます。 このツールでは、プロジェクトを作成するときに既定の関数名をオーバーライドすることはできないため、ここで関数クラス、ファイル、およびメタデータに対してより適切な名前を指定します。

1. **エクスプローラー** で Function1.cs ファイルを右クリックし、*HttpExample.cs* という名前に変更します。

1. コードで Function1 クラスの名前を "HttpExample" に変更します。

1. `Run` という名前の `HttpTrigger` メソッドで、`FunctionName` メソッド属性の名前を `HttpExample` に変更します。


## <a name="4-run-the-function-locally"></a>4.関数をローカルで実行する

1. 関数を実行するには、Visual Studio で <kbd>F5</kbd> キーを押します。

1. Azure Functions のランタイムの出力から、関数の URL をコピーします。

    ![Azure ローカル ランタイム](../../includes/media/functions-run-function-test-local-vs/functions-debug-local-vs.png)

1. HTTP 要求の URL をブラウザーのアドレス バーに貼り付けます。 この URL にクエリ文字列 **?name=<YOUR_NAME>** を追加して、要求を実行します。 

    ![ブラウザーでの関数 localhost の応答](../../includes/media/functions-run-function-test-local-vs/functions-run-browser-local-vs.png)

1. デバッグを停止するには、Visual Studio で <kbd>Shift</kbd>+<kbd>F5</kbd> キーを押します。

<br/>
<details>
<summary><strong>トラブルシューティング</strong></summary>
 ツールで HTTP 要求を処理できるように、ファイアウォールの例外を有効にすることが必要になる場合があります。 承認レベルは、ローカルで関数を実行するときには適用されません。
</details>

## <a name="5-publish-the-project-to-azure"></a>5.Azure にプロジェクトを発行する

1. **ソリューション エクスプローラー** で、プロジェクトを右クリックし、 **[発行]** を選択します。

1. **[ターゲット]** で **[Azure]** を選択します

    :::image type="content" source="../../includes/media/functions-vstools-publish/functions-visual-studio-publish-profile-step-1.png" alt-text="Azure ターゲットを選択する":::

1. **[特定のターゲット]** で **[Azure Function App (Windows)]** を選択します

    :::image type="content" source="../../includes/media/functions-vstools-publish/functions-visual-studio-publish-profile-step-2.png" alt-text="Azure Function App の選択":::

1. **[Function Instance]\(関数インスタンス\)** で **[Create a new Azure Function]\(新しい Azure 関数の作成\)** を選択し、次に示す値を使用します。

    + **[名前]** に <abbr title="新しい関数アプリを一意に識別する名前を使用します。 この名前をそのまま使用するか、新しい名前を入力します。 有効な文字は、`a-z`、`0-9`、`-` です。">グローバルに一意の名前</abbr>
    
    + ドロップダウン リストからサブスクリプションを **選択** します。
    
    + ドロップダウン リストから既存のリソース グループを **選択** するか、 <abbr title="1 つの単位として管理できる関連する Azure リソースの論理コンテナー。">resource group</abbr> または **[新規]** を選択して新しいリソース グループを作成します。
    
    + **Select** <abbr title="従量課金プランで実行される関数アプリにプロジェクトを発行する場合は、関数アプリの実行に対してのみお支払いください。 他のホスティング プランでは、コストが高くなります。">従量課金</abbr> [Play Type]\(再生の種類\) ドロップダウンで (詳細については、[従量課金プラン](consumption-plan.md)に関するページを参照してください)。
    
    + **選択**  <abbr title="リソースが割り当てられている特定の Azure データ センターの地理的な呼称。利用可能なリージョンの一覧については、[リージョン](https://azure.microsoft.com/regions/)に関するページを参照してください。">location</abbr> ドロップダウンから
    
    + ドロップダウンから Azure ストレージ アカウントを **選択** します。<abbr="Functions Runtime には Azure ストレージ アカウントが必須です。 [新規] を選択して汎用ストレージ アカウントを構成します。 または、ストレージ アカウントの要件を満たす既存のアカウントを選択することもできます。"></abbr> account from the drop-down

    ![[App Service の作成] ダイアログ](../../includes/media/functions-vstools-publish/functions-visual-studio-publish.png)

1. **[作成]** を選択します。 

1. **[Functions instance]\(関数インスタンス\)** で、 **[Run from package file]\(パッケージ ファイルから実行します\)** に必ずチェックを入れてください。 

    :::image type="content" source="../../includes/media/functions-vstools-publish/functions-visual-studio-publish-profile-step-4.png" alt-text="プロファイル作成の完了":::

    <br/>
    <details>
    <summary><strong>この設定は何を行っているのですか?</strong></summary>
    **[Run from package file]\(パッケージ ファイルから実行します\)** を使用すると、関数アプリは、[Zip Deploy](functions-deployment-technologies.md#zip-deploy) を使用して、[Run-From-Package](run-functions-from-deployment-package.md) モードが有効な状態でデプロイされます。 これは、パフォーマンスが向上するため、関数プロジェクトの推奨されるデプロイ方法です。    
    </details>   

1. **[完了]** を選択します。

1. [発行] ページで、**[発行]** を選択します。

1. [発行] ページで、関数アプリのルート URL を確認します。

1. [発行] タブで、 **[<abbr title="Cloud Explorer を使用すると、Visual Studio でサイトのコンテンツを表示し、関数アプリを開始および停止し、Azure の関数アプリ リソースを直接および Azure portal で参照できます。">Cloud Explorer</abbr> で管理する]** を選択します。
    
    :::image type="content" source="../../includes/media/functions-vstools-publish/functions-visual-studio-publish-complete.png" alt-text="発行成功のメッセージ":::
    

## <a name="6-test-your-function-in-azure"></a>6.Azure で関数をテストする

1. Cloud Explorer では、新しい関数アプリが選択されているはずです。 そうでない場合は、自分のサブスクリプションを展開し、 **[App Services]** を展開して、新しい関数アプリを選択します。

1. 関数アプリを右クリックし、 **[ブラウザーで開く]** を選択します。 これにより、関数アプリのルートが既定の Web ブラウザーで開かれ、関数アプリが実行されていることを示すページが表示されます。 

    :::image type="content" source="media/functions-create-your-first-function-visual-studio/function-app-running-azure.png" alt-text="実行されている関数アプリ":::

1. ブラウザーのアドレス バーで、ベース URL に文字列 **/api/HttpExample?name=Functions** を追加し、要求を実行します。

    HTTP トリガー関数を呼び出す URL は、次の形式になります。

    `http://<APP_NAME>.azurewebsites.net/api/HttpExample?name=Functions`

2. この URL に移動し、関数によって返されたリモート GET 要求に対する応答がブラウザーに表示されます。次の例のような内容です。

    :::image type="content" source="media/functions-create-your-first-function-visual-studio/functions-create-your-first-function-visual-studio-browser-azure.png" alt-text="ブラウザーでの関数の応答":::

## <a name="7-clean-up-resources"></a>7.リソースをクリーンアップする

不要なコストが発生しないよう、関数アプリとそのリソースを削除してください。

1. Cloud Explorer で、自分のサブスクリプションを展開して **[App Services]** を展開し、関数アプリを右クリックして、 **[ポータルで開く]** を選択します。 

1. 関数アプリのページで、 **[概要]** タブを選択してから、 **[リソース グループ]** の下にあるリンクを選択します。

   :::image type="content" source="media/functions-create-your-first-function-visual-studio/functions-app-delete-resource-group.png" alt-text="関数アプリのページで削除するリソース グループを選択する":::

1. **[リソース グループ]** ページで、含まれているリソースの一覧を確認し、削除するものであることを確認します。
 
1. **[リソース グループの削除]** を選択し、指示に従います。

    削除には数分かかることがあります。 実行されると、通知が数秒間表示されます。 ページの上部にあるベルのアイコンを選択して、通知を表示することもできます。

## <a name="next-steps"></a>次のステップ

次の記事に進み、 <abbr title="関数をストレージ キューに関連付けて、そのキューにメッセージを作成できるようにするための手段。 バインドは、関数と他のリソースとの間の宣言型の接続です。 入力バインドは関数にデータを提供し、出力バインドは関数から他のリソースにデータを提供します。"> Azure Storage キュー出力バインド</abbr> を関数に追加する方法を学習してください。

> [!div class="nextstepaction"]
> [関数に Azure Storage キュー バインドを追加する](functions-add-output-binding-storage-queue-vs.md)
