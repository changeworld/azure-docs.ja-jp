---
title: Azure Cosmos DB エミュレーター ビルド タスクを使用して CI/CD パイプラインを設定する
description: Visual Studio Team Services (VSTS) で Cosmos DB エミュレーター ビルド タスクを使用してビルドとリリースのワークフローを設定する方法のチュートリアル
services: cosmos-db
keywords: Azure Cosmos DB Emulator
author: deborahc
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: tutorial
ms.date: 8/28/2018
ms.author: dech
ms.openlocfilehash: 37bb43435c34f14145b3642aa12c5cb0f16d780c
ms.sourcegitcommit: e2348a7a40dc352677ae0d7e4096540b47704374
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/05/2018
ms.locfileid: "43783876"
---
# <a name="set-up-a-cicd-pipeline-with-the-azure-cosmos-db-emulator-build-task-in-visual-studio-team-services"></a>Visual Studio Team Services で Azure Cosmos DB エミュレーター ビルド タスクを使用して CI/CD パイプラインを設定する

Azure Cosmos DB エミュレーターでは、Azure Cosmos DB サービスを開発目的でエミュレートするローカル環境を利用できます。 エミュレーターを使用すれば、Azure サブスクリプションを作成したりコストをかけたりせずに、アプリケーションをローカルで開発してテストできます。 

Visual Studio Team Services (VSTS) 用の Azure Cosmos DB エミュレーター ビルド タスクでは、CI 環境で同じことができます。 ビルド タスクを使用すると、ビルドとリリースのワークフローの一環として、エミュレーターに対してテストを実行できます。 タスクによって、既に実行中のエミュレーターが含まれた Docker コンテナーが起動され、ビルド定義の残りの部分で使用されるエンドポイントが提供されます。 エミュレーターのインスタンスは必要な数だけ作成して開始できます。これらのインスタンスはそれぞれ、別のコンテナーで実行されます。 

この記事では、VSTS で ASP.NET アプリケーション用の CI パイプラインを設定し、Cosmos DB エミュレーター ビルド タスクを使用してテストを実行する方法について説明します。 

## <a name="install-the-emulator-build-task"></a>エミュレーター ビルド タスクのインストール

ビルド タスクを使用するには、最初にそれを VSTS 組織にインストールする必要があります。 [マーケットプレース](https://marketplace.visualstudio.com/items?itemName=azure-cosmosdb.emulator-public-preview)で拡張機能 **Azure Cosmos DB エミュレーター**を見つけて、**[Get it free]\(無料で入手\)** をクリックします。

![VSTS マーケットプレースで Azure Cosmos DB エミュレーター ビルド タスクを見つけてインストールする](./media/tutorial-setup-ci-cd/addExtension_1.png)

次に、拡張機能のインストール先となる組織を選択します。 

> [!NOTE]
> 拡張機能を VSTS 組織にインストールするには、アカウント所有者またはプロジェクト コレクション管理者である必要があります。 アクセス許可はないものの、アカウント メンバーである場合は、代わりに拡張機能を要求できます。 [詳細情報。](https://docs.microsoft.com/vsts/marketplace/faq-extensions?view=vsts#install-request-assign-and-access-extensions) 

![拡張機能のインストール先となる VSTS 組織を選択する](./media/tutorial-setup-ci-cd/addExtension_2.png)

## <a name="create-a-build-definition"></a>ビルド定義の作成

拡張機能をインストールしたら、次はそれを[ビルド定義](https://docs.microsoft.com/vsts/pipelines/get-started-designer?view=vsts&tabs=new-nav)に追加する必要があります。 既存のビルド定義を変更するか、または新しいものを作成できます。 既存のビルド定義がある場合、[ビルド定義へのエミュレーター ビルド タスクの追加](#addEmulatorBuildTaskToBuildDefinition)に関するセクションまでスキップしてかまいません。

新しいビルド定義を作成するには、VSTS で **[ビルドとリリース]** タブに移動します。 **[+新規]** を選択します。

![新しいビルド定義を作成する](./media/tutorial-setup-ci-cd/CreateNewBuildDef_1.png) ビルドの有効化に使用したいチーム プロジェクト、リポジトリ、ブランチを選択します。 

![ビルド定義のチーム プロジェクト、リポジトリ、ブランチを選択する ](./media/tutorial-setup-ci-cd/CreateNewBuildDef_2.png)

最後に、ビルド定義に使用したいテンプレートを選択します。 このチュートリアルでは、**ASP.NET** テンプレートを選択します。 

![使用したいビルド定義テンプレートを選択する ](./media/tutorial-setup-ci-cd/CreateNewBuildDef_3.png)

これで、Azure Cosmos DB エミュレーター ビルド タスクを使用するよう設定できるビルド定義を用意できました。このビルド定義は以下のようになります。 

![ASP.NET ビルド定義テンプレート](./media/tutorial-setup-ci-cd/CreateNewBuildDef_4.png)

## <a name="addEmulatorBuildTaskToBuildDefinition"></a>ビルド定義へのタスクの追加

エミュレーター ビルド タスクを追加するには、検索ボックスで「**cosmos**」を検索して、**[追加]** を選択します。 ビルド タスクでは、既に実行中の Cosmos DB エミュレーターのインスタンスが含まれているコンテナーが起動されます。そのため、このタスクは、エミュレーターが実行されていることを前提とするその他のタスクの前に配置する必要があります。

![エミュレーター ビルド タスクをビルド定義に追加する](./media/tutorial-setup-ci-cd/addExtension_3.png) このチュートリアルでは、テストの実行前にエミュレーターを使用できるようにするために、[フェーズ 1] の冒頭にタスクを追加します。
ここで、完成したビルド定義は次のようになります。 

![ASP.NET ビルド定義テンプレート](./media/tutorial-setup-ci-cd/CreateNewBuildDef_5.png)

## <a name="configure-tests-to-use-the-emulator"></a>エミュレーターを使用するテストの構成
次に、エミュレーターを使用するテストを構成します。 エミュレーター ビルド タスクによって、"CosmosDbEmulator.Endpoint" という環境変数がエクスポートされます。ビルド パイプライン内の後続のタスクは、このエンドポイントに対して要求を発行できます。 

このチュートリアルでは、[Visual Studio Test タスク](https://github.com/Microsoft/vsts-tasks/blob/master/Tasks/VsTestV2/README.md)を使用して、**.runsettings** ファイルを通じて構成された単体テストを実行します。 単体テストの設定の詳細については、[ドキュメント](https://docs.microsoft.com/visualstudio/test/configure-unit-tests-by-using-a-dot-runsettings-file?view=vs-2017)を参照してください。

以下は、アプリケーションの単体テストに渡されるパラメーターを定義する **.runsettings** ファイルの例です。 使用されている `authKey` 変数が、エミュレーターの[既知のキー](https://docs.microsoft.com/azure/cosmos-db/local-emulator#authenticating-requests)であることに注意してください。 この `authKey` はエミュレーター ビルド タスクで必要なキーであり、**.runsettings** ファイルで定義する必要があります。

```csharp
<RunSettings>
    <TestRunParameters>
    <Parameter name="endpoint" value="https://localhost:8081" />
    <Parameter name="authKey" value="C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==" />
    <Parameter name="database" value="ToDoListTest" />
    <Parameter name="collection" value="ItemsTest" />
  </TestRunParameters>
</RunSettings>
```
`TestRunParameters` のこれらのパラメーターは、アプリケーションのテスト プロジェクトに含まれている `TestContext` プロパティを介して参照されます。 Cosmos DB に対して実行するテストの例を次に示します。 

```csharp
namespace todo.Tests
{
    [TestClass]
    public class TodoUnitTests
    {
        public TestContext TestContext { get; set; }

        [TestInitialize()]
        public void Initialize()
        {
            string endpoint = TestContext.Properties["endpoint"].ToString();
            string authKey = TestContext.Properties["authKey"].ToString();
            Console.WriteLine("Using endpoint: ", endpoint);
            DocumentDBRepository<Item>.Initialize(endpoint, authKey);
        }
        [TestMethod]
        public async Task TestCreateItemsAsync()
        {
            var item = new Item
            {
                Id = "1",
                Name = "testName",
                Description = "testDescription",
                Completed = false,
                Category = "testCategory"
            };

            // Create the item
            await DocumentDBRepository<Item>.CreateItemAsync(item);
            // Query for the item
            var returnedItem = await DocumentDBRepository<Item>.GetItemAsync(item.Id, item.Category);
            // Verify the item returned is correct.
            Assert.AreEqual(item.Id, returnedItem.Id);
            Assert.AreEqual(item.Category, returnedItem.Category);
        }

        [TestCleanup()]
        public void Cleanup()
        {
            DocumentDBRepository<Item>.Teardown();
        }
    }
}
```

Visual Studio Test タスクの [実行オプション] に移動します。 **[設定ファイル]** オプションで、**.runsettings** ファイルを使用して構成されたテストを指定します。 **[テスト実行パラメーターのオーバーライド]** オプションで、「` -endpoint $(CosmosDbEmulator.Endpoint)`」と入力します。 これを行うことで、**.runsettings** ファイルで定義されたエンドポイントではなく、エミュレーター ビルド タスクのエンドポイントを参照するよう、Test タスクが構成されます。  

![エミュレーター ビルド タスクのエンドポイントを使ってエンドポイント変数をオーバーライドする](./media/tutorial-setup-ci-cd/addExtension_5.png)

## <a name="run-the-build"></a>ビルドを実行します
次に、ビルドを保存してキューに登録します。 

![ビルドを保存して実行する](./media/tutorial-setup-ci-cd/runBuild_1.png)

ビルドが開始されたら、Cosmos DB エミュレーター タスクによって、エミュレーターがインストールされた Docker イメージのプルが開始されたことを確認します。 

![ビルドを保存して実行する](./media/tutorial-setup-ci-cd/runBuild_4.png)

ビルドが完了したら、ビルド タスクの後に Cosmos DB エミュレーターに対してすべてのテストが実行され、成功することを確認します。

![ビルドを保存して実行する](./media/tutorial-setup-ci-cd/buildComplete_1.png)

## <a name="next-steps"></a>次の手順

エミュレーターを使用して開発とテストをローカルで行う方法の詳細については、「[ローカルの開発とテストでの Azure Cosmos DB Emulator の使用](https://docs.microsoft.com/azure/cosmos-db/local-emulator)」を参照してください。

エミュレーター SSL 証明書のエクスポートについては、「[Java、Python、および Node.js で使用する Azure Cosmos DB Emulator 証明書のエクスポート](https://docs.microsoft.com/azure/cosmos-db/local-emulator-export-ssl-certificates)」を参照してください。
