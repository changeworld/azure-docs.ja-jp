---
title: Azure Functions のトリガーとバインドで ID ベースの接続を使用する
ms.service: azure-functions
description: Azure Functions を使用して Service Bus キューに接続する場合に、接続文字列の代わりに ID ベースの接続を使用する方法について説明します。
ms.topic: tutorial
ms.date: 10/20/2021
ms.openlocfilehash: 5a3eb4dc6006d9072abac95b7940692e8af737a5
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130271267"
---
# <a name="tutorial-use-identity-based-connections-instead-of-secrets-with-triggers-and-bindings"></a>チュートリアル: トリガーとバインドでシークレットの代わりに ID ベースの接続を使用する

このチュートリアルでは、関数アプリの設定に格納されているシークレットの代わりにマネージド ID を使用して Azure Service Bus キューに接続するように Azure Functions を構成する方法について説明します。 このチュートリアルは、チュートリアル「[既定のストレージ シークレットが定義に含まれていない関数アプリを作成する][前のチュートリアル]」の続きです。 ID ベースの接続の詳細については、「[ID ベースの接続を構成する](functions-reference.md#configure-an-identity-based-connection)」を参照してください。

ここで示す手順は通常すべての言語に有効ですが、このチュートリアルでは現在、特に Windows 上の C# クラス ライブラリ関数をサポートしています。 

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
>
> * Service Bus の名前空間とキューを作成します。
> * マネージド ID を使用して関数アプリを構成する
> * Service Bus キューから読み取るためのアクセス許可をその ID に付与するロールの割り当てを作成する
> * Service Bus トリガーを使用して関数アプリを作成してデプロイする
> * Service Bus への ID ベースの接続を検証する

## <a name="prerequisite"></a>前提条件

前のチュートリアル 「[既定のストレージ シークレットが定義に含まれていない関数アプリを作成する][前のチュートリアル]」を完了します。

## <a name="create-a-service-bus-and-queue"></a>サービス バスとキューを作成する

1. [Azure portal](https://portal.azure.com)で、 **[リソースの作成] (+)** を選択します。

1. **[リソースの作成]** ページで、 **[統合]**  >  **[Service Bus]** の順に選択します。

1. **[基本]** ページで、次の表を使用して Service Bus 名前空間の設定を構成します。 残りのオプションについては既定値を使用します。

    | オプション      | 推奨値  | 説明      |
    | ------------ | ---------------- | ---------------- |
    | **サブスクリプション** | 該当するサブスクリプション | リソースが作成されるサブスクリプション。 |
    | **[リソース グループ](../azure-resource-manager/management/overview.md)**  | myResourceGroup | 関数アプリで作成したリソース グループ。 |
    | **名前空間名** | グローバルに一意の名前 | 関数をトリガーするインスタンスの名前空間。 名前空間はパブリックにアクセスできるため、Azure 全体でグローバルに一意の名前を使用する必要があります。 また、名前の長さは 6 から 50 文字で、英数字とダッシュのみを含める必要があり、数字で始めることはできません。 |
    | **[場所](https://azure.microsoft.com/regions/)** | myFunctionRegion | 関数アプリを作成したリージョン。 |
    | **価格レベル** | Basic | 基本の Service Bus サービス レベル。 |

1. **[Review + create]\(レビュー + 作成\)** を選択します。 検証が終了したら、 **[作成]** を選択します。

1. デプロイが完了したら、**[リソースに移動]** を選択します。

1. 新しい Service Bus 名前空間で、 **[+ キュー]** を選択してキューを追加します。

1. 新しいキューの名前として「`myinputqueue`」と入力し、 **[作成]** を選択します。

キューが作成されたので、関数アプリのマネージド ID にロールの割り当てを追加します。

## <a name="configure-your-service-bus-trigger-with-a-managed-identity"></a>マネージド ID を使用して Service Bus トリガーを構成する

ID ベースの接続で Service Bus トリガーを使用するには、**Azure Service Bus のデータ受信者** ロールの割り当てを関数アプリのマネージド ID に追加する必要があります。 このロールは、マネージド ID を使用して Service Bus 名前空間からトリガーする場合に必要です。 このロールに独自のアカウントを追加することもできます。これにより、ローカル テスト時に Service Bus 名前空間に接続できます。

> [!NOTE]
> ID ベースの接続を使用するためのロール要件は、サービスと、それに接続する方法によって異なります。 ニーズは、トリガー、入力バインド、出力バインドによって異なります。 特定のロール要件の詳細については、サービスのトリガーとバインドのドキュメントを参照してください。

1. 作成した Service Bus 名前空間で、 **[アクセス制御 (IAM)]** を選択します。 ここで、リソースにアクセスできるユーザーを表示および構成できます。  

1. **[追加]** をクリックし、 **[ロールの割り当ての追加]** を選択します。

1. **[Azure Service Bus のデータ受信者]** を検索し、それを選択して、 **[次へ]** を選択します。

1. **[メンバー]** タブの **[アクセスの割り当て先]** で、 **[マネージド ID]** を選択します

1. **[メンバーの選択]** をクリックして、 **[マネージド ID の選択]** パネルを開きます。

1. **[サブスクリプション]** が、前にリソースを作成したものであることを確認します。

1. **[マネージド ID]** セレクターで、 **[システム割り当てマネージド ID]** カテゴリから **[関数アプリ]** を選びます。 ラベル "関数アプリ" の横のかっこ内に、サブスクリプション内でシステム割り当て ID を持っているアプリの数を示す値が含まれている場合があります。

1. 入力フィールドの下の一覧に、自分のアプリが表示されているはずです。 表示されていない場合は、 **[選択]** ボックスを使って、アプリの名前で結果をフィルター処理できます。

1. 自分のアプリケーションをクリックします。 それが、下の **[選択したメンバー]** セクションに移動します。 <bpt id="p1">**</bpt>[選択]<ept id="p1">**</ept> をクリックします。

1. **[ロールの割り当てを追加]** 画面に戻り、 **[レビューと割り当て]** をクリックします。 構成を確認し、 **[レビューと割り当て]** をクリックします。

マネージド ID を使用して、関数アプリに、Service Bus 名前空間へのアクセスを許可しました。

## <a name="connect-to-service-bus-in-your-function-app"></a>関数アプリで Service Bus に接続する

1. portal で、[前のチュートリアル]で作成した関数アプリを検索するか、 **[関数アプリ]** ページで参照します。

1. 関数アプリの **[設定]** で、 **[構成]** を選択します。

1. **[アプリケーション設定]** で、 **[+ 新しいアプリケーション設定]** を選択して、次の表に示す設定を新規に作成します。

    | 名前      | [値]  | 説明 |
    | ------------ | ---------------- | ----------- |
    | **ServiceBusConnection__fullyQualifiedNamespace** | <SERVICE_BUS_NAMESPACE>.servicebus.windows.net | この設定により、関数アプリが Service Bus に接続され、シークレットの代わりに ID ベースの接続が使用されます。 |

1. 2 つの設定を作成したら、 **[保存]**  >  **[確認]** の順に選択します。

マネージド ID を使用して Service Bus 名前空間に接続する関数アプリを準備したので、Service Bus トリガーを使用する新しい関数をローカル プロジェクトに追加できます。

## <a name="add-a-service-bus-triggered-function"></a>Service Bus でトリガーされる関数を追加する

1. 次のように `func init` コマンドを実行して、特定のランタイムを含んだ LocalFunctionProj という名前のフォルダーに関数プロジェクトを作成します。

    ```csharp
    func init LocalFunctionProj --dotnet
    ```

1. プロジェクト フォルダーに移動します。

    ```console
    cd LocalFunctionProj
    ```

1. ルート プロジェクト フォルダーで、次のコマンドを実行します。

    ```command
    dotnet remove package Microsoft.Azure.Webjobs.Extensions.ServiceBus
    dotnet add package Microsoft.Azure.Webjobs.Extensions.ServiceBus --prerelease
    ```

    これにより、Service Bus 拡張パッケージの既定のバージョンが、マネージド ID をサポートするバージョンに置き換えられます。

1. 次のコマンドを実行して、Service Bus によってトリガーされる関数をプロジェクトに追加します。

    ```csharp
    func new --name ServiceBusTrigger --template ServiceBusQueueTrigger 
    ```

    これにより、新しい Service Bus トリガーのコードと拡張パッケージへの参照が追加されます。 このトリガーの Service Bus 名前空間接続設定を追加する必要があります。

1. 新しい ServiceBusTrigger.cs プロジェクト ファイルを開き、`ServiceBusTrigger` クラスを次のコードに置き換えます。

    ```csharp
    public static class ServiceBusTrigger
    {
        [FunctionName("ServiceBusTrigger")]
        public static void Run([ServiceBusTrigger("myinputqueue", 
            Connection = "ServiceBusConnection")]string myQueueItem, ILogger log)
        {
            log.LogInformation($"C# ServiceBus queue trigger function processed message: {myQueueItem}");
        }
    }
    ```

    このコード サンプルでは、キュー名が `myinputqueue` に更新されます。これは、前に作成したキューと同じ名前です。 また、Service Bus 接続の名前が `ServiceBusConnection` に設定されます。 これは、ID ベースの接続で使用される Service Bus 名前空間 (portal で構成した `ServiceBusConnection__fullyQualifiedNamespace`) です。

> [!NOTE]  
> この時点では、`func start` を使用して関数を実行しようとすると、エラーが発生します。 これは、ID ベースの接続をローカルで定義していないためです。 関数をローカルで実行する場合、[前のセクション](#connect-to-service-bus-in-your-function-app)で行ったとおりに、`local.settings.json` でアプリ設定 `ServiceBusConnection__fullyQualifiedNamespace` を設定します。 さらに、ロールを開発者 ID に割り当てる必要があります。 詳細については、[ID ベースの接続によるローカル開発のドキュメント](./functions-reference.md#local-development-with-identity-based-connections)を参照してください。

## <a name="publish-the-updated-project"></a>更新したプロジェクトを公開する

1. 次のコマンドを実行して、展開パッケージに必要なファイルをローカルに生成します。

    ```console
    dotnet publish --configuration Release
    ```

1. `\bin\Release\netcoreapp3.1\publish` サブフォルダーを参照し、その内容から .zip ファイルを作成します。

1. 次のコマンドを実行して .zip ファイルを公開し、必要に応じて `FUNCTION_APP_NAME`、`RESOURCE_GROUP_NAME`、`PATH_TO_ZIP` パラメーターを置き換えます。

    ```azurecli
    az functionapp deploy -n FUNCTION_APP_NAME -g RESOURCE_GROUP_NAME --src-path PATH_TO_ZIP
    ```

新しいトリガーで関数アプリを更新したので、ID を使用して機能することを確認できます。

## <a name="validate-your-changes"></a>変更を検証する

1. portal の **[サービス]** で、`Application Insights` を検索し、 **[Application Insights]** を選択します。  

1. **[Application Insights]** で、名前付きインスタンスを参照または検索します。

1. インスタンスで、 **[調査]** の下の **[ライブ メトリック]** を選択します。

1. 前のタブを開いたままにして、新しいタブで Azure portal を開きます。新しいタブで、Service Bus 名前空間に移動し、左側のブレードから **[キュー]** を選択します。

1. `myinputqueue` という名前のキューを選択します。

1. 左側のブレードから **[Service Bus Explorer]** を選択します。

1. テスト メッセージを送信します。

1. 開いている **[ライブ メトリック]** タブを選択し、Service Bus キューの実行を確認します。

お疲れさまでした。 これで、マネージド ID を使用して Service Bus キュー トリガーが正常に設定されました。

[!INCLUDE [clean-up-section-portal](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、ID ベースの接続を使用して関数アプリを作成しました。

ID ベースの接続を使用する Azure Functions の詳細については、次のリンクを使用してください。

- [Azure Functions でのマネージド ID](../app-service/overview-managed-identity.md)
- [Azure Functions での ID ベースの接続](./functions-reference.md#configure-an-identity-based-connection)
- [ローカル開発のための関数のドキュメント](./functions-reference.md#local-development-with-identity-based-connections)

[前のチュートリアル]: ./functions-identity-based-connections-tutorial.md
