---
title: .NET Core と Visual Studio を使用する Azure Dev Spaces でのチーム開発 | Microsoft Docs
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.custom: vs-azure
ms.workload: azure-vs
ms.component: azds-kubernetes
author: ghogen
ms.author: ghogen
ms.date: 07/09/2018
ms.topic: tutorial
description: Azure のコンテナーとマイクロサービスを使用した迅速な Kubernetes 開発
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, コンテナー
manager: douge
ms.openlocfilehash: 1b603db552edd14dab2c86e6b005184eb88e74bb
ms.sourcegitcommit: fab878ff9aaf4efb3eaff6b7656184b0bafba13b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/22/2018
ms.locfileid: "42154817"
---
# <a name="team-development-with-azure-dev-spaces"></a>Azure Dev Spaces を使用したチーム開発

このチュートリアルでは、複数の開発スペースを使用して異なる開発環境で同時に作業し、個別の作業を同じクラスター内の独立した開発スペースに分離させておく方法を説明します。

## <a name="call-another-container"></a>別のコンテナーを呼び出す
このセクションでは、2 つ目のサービス (`mywebapi`) を作成し、`webfrontend` でそのサービスを呼び出します。 各サービスは別々のコンテナーで実行されます。 その後、両方のコンテナーでデバッグします。

![](media/common/multi-container.png)

### <a name="download-sample-code-for-mywebapi"></a>*mywebapi* のサンプル コードをダウンロードする
時間を節約するために、GitHub リポジトリからサンプル コードをダウンロードしましょう。 https://github.com/Azure/dev-spaces に移動し、**[Clone or download]** をクリックして GitHub リポジトリをダウンロードします。 このセクションのコードは、`samples/dotnetcore/getting-started/mywebapi` にあります。

### <a name="run-mywebapi"></a>*mywebapi* を実行する
1. "*別の Visual Studio ウィンドウ*" で `mywebapi` プロジェクトを開きます。
1. 前に `webfrontend` プロジェクトで行ったように、起動設定ドロップダウンから **[Azure Dev Spaces]** を選択します。 今回は新しい AKS クラスターを作成するのではなく、作成済みのものを選択します。 前述のように、[スペース] は既定の `default` のままにし、**[OK]** をクリックします。 [出力] ウィンドウを見ると、デバッグを開始したときの作業速度を上げるために、Visual Studio によって開発空間でこの新しいサービスの "ウォーム アップ" が開始されたことがわかります。
1. F5 キーを押し、サービスがビルドされ、展開されるまで待ちます。 準備ができると、Visual Studio のステータス バーがオレンジ色に変わります。
1. **[出力]** ウィンドウの **[Azre Dev Spaces for AKS]** ウィンドウに表示されるエンドポイント URL を書き留めます。 これは、http://localhost:\<portnumber\> のように表示されます。 コンテナーはローカルで実行されているように見えますが、実際には Azure の開発空間で実行されています。
2. `mywebapi` の準備ができたら、ブラウザーで localhost アドレスを開き、URL に `/api/values` を追加して `ValuesController` の既定の GET API を呼び出します。 
3. すべての手順が正常に完了すると、次のような `mywebapi` サービスからの応答を確認できます。

    ![](media/get-started-netcore-visualstudio/WebAPIResponse.png)

### <a name="make-a-request-from-webfrontend-to-mywebapi"></a>*webfrontend* から *mywebapi* に対して要求を行う
次に、`mywebapi` に対して要求を行う `webfrontend` のコードを記述しましょう。 `webfrontend` プロジェクトがある Visual Studio ウィンドウに切り替えます。 `HomeController.cs` ファイルで、About メソッドのコードを次のコードに*置き換えます*。

   ```csharp
   public async Task<IActionResult> About()
   {
      ViewData["Message"] = "Hello from webfrontend";

      using (var client = new System.Net.Http.HttpClient())
            {
                // Call *mywebapi*, and display its response in the page
                var request = new System.Net.Http.HttpRequestMessage();
                request.RequestUri = new Uri("http://mywebapi/api/values/1");
                if (this.Request.Headers.ContainsKey("azds-route-as"))
                {
                    // Propagate the dev space routing header
                    request.Headers.Add("azds-route-as", this.Request.Headers["azds-route-as"] as IEnumerable<string>);
                }
                var response = await client.SendAsync(request);
                ViewData["Message"] += " and " + await response.Content.ReadAsStringAsync();
            }

      return View();
   }
   ```

前述のコード例は、`azds-route-as` ヘッダーを受信要求から送信要求に転送します。 これにより、チーム シナリオでの生産性の高い開発エクスペリエンスがどのように促進されるのかについては、後ほど説明します。

### <a name="debug-across-multiple-services"></a>複数のサービスでデバッグする
1. この時点で、`mywebapi` は引き続きデバッガーがアタッチされた状態で実行されています。 そうでない場合は、`mywebapi` プロジェクトで F5 キーを押します。
1. `api/values/{id}` の GET 要求を処理する、`Controllers/ValuesController.cs` ファイルの `Get(int id)` メソッドにブレークポイントを設定します。
1. 上記のコードを貼り付けた `webfrontend` プロジェクトで、GET 要求を `mywebapi/api/values` に送信する直前にブレークポイントを設定します。
1. `webfrontend` プロジェクトで F5 キーを押します。 Visual Studio はブラウザーで適切な localhost ポートを再度開き、Web アプリが表示されます。
1. ページの上部にある **[About]** リンクをクリックすると、`webfrontend` プロジェクトのブレークポイントがトリガーされます。 
1. F10 キーを押して続行します。 `mywebapi` プロジェクトのブレークポイントがトリガーされます。
1. F5 キーを押して続行し、`webfrontend` プロジェクトのコードに戻ります。
1. F5 キーをもう一度押すと要求が完了し、ブラウザーでページが返されます。 Web アプリの About ページに、2 つのサービスによって連結されたメッセージ ("Hello from webfrontend and Hello from mywebapi") が表示されます。

お疲れさまでした。 これで、各コンテナーを個別に開発して展開できる、複数コンテナー アプリケーションが作成されました。

## <a name="learn-about-team-development"></a>チーム開発について学ぶ

ここまでは、アプリの開発に取り組んでいる開発者が 1 人だけであるかのようにアプリケーションのコードを実行していました。 このセクションでは、Azure Dev Spaces によるチーム開発の効率化について説明します。
* 共有開発空間で、または必要に応じて個別の開発空間で作業することで、同じ環境の中で開発者チームが作業できるようにします。
* 各開発者が他の開発者の妨げになることを心配せずに、単独でコードを反復処理できるようにします。
* モックを作成したり、依存関係をシミュレートしたりする必要なく、コードをコミットする前に、エンド ツー エンドでコードをテストする。

### <a name="challenges-with-developing-microservices"></a>マイクロサービスの開発に伴う課題
現時点では、サンプル アプリケーションはそれほど複雑ではありません。 しかし、実際の開発では、さらに多くのサービスが追加され、開発チームが拡大するにつれて、すぐに課題が生じます。

他の多くのサービスと対話するサービスの開発に取り組む自分の姿を想像してみてください。

- 開発のためにすべてをローカルで実行するのは非現実的になる可能性があります。 開発用コンピューターに、アプリ全体を実行できるだけのリソースがない場合があります。 また、アプリに、パブリックに到達できる必要があるエンドポイントがある場合もあります (アプリで SaaS アプリからの webhook に応答するなど)。
- 依存するサービスだけを実行するよう試みることはできますが、その場合、依存関係の完全なクロージャ (依存関係の依存関係など) を把握しておく必要があります。 あるいは、依存関係を扱っていなかったために、依存関係を構築し、実行する方法が簡単にはわからないという問題が生じます。
- サービスの依存関係の多くをシミュレートしたり、モックを作成したりする開発者もいます。 これが役立つ場合もありますが、モックの管理に開発労力を費やすことになる可能性があります。 さらに、開発環境が運用環境と異なって見えるようになり、微妙なバグが入り込む可能性もあります。
- その結果、あらゆる種類のエンド ツー エンド テストの実行が困難になります。 実際には、統合テストはコミット後にのみ行われる可能性があります。つまり、開発サイクルの後半に問題が発生します。

    ![](media/common/microservices-challenges.png)

### <a name="work-in-a-shared-dev-space"></a>共有開発空間で作業する
Azure Dev Spaces を使用して、Azure に "*共有*" 開発空間をセットアップできます。 各開発者は、アプリケーションの自分の担当部分だけに集中でき、自分のシナリオが依存する他のすべてのサービスとクラウド リソースが既に含まれている開発空間で、"*事前コミット コード*" を繰り返し開発できます。 依存関係は常に最新の状態であり、開発者は運用環境を反映した方法で作業を行います。

### <a name="work-in-your-own-space"></a>自分のスペースで作業する
サービスのコードを開発するときには、チェックインできる状態になるまで、多くの場合、コードは良好な状態ではありません。 引き続きコードの調整とテストを繰り返し、ソリューションを試します。 Azure Dev Spaces では、**スペース**という概念を導入しています。このスペースにより、チーム メンバーの妨げになることを心配せずに、単独で作業を行うことができます。

次の手順を実行して、`webfrontend` サービスと `mywebapi` サービスが、どちらも **`default` 開発空間**で実行されていることを確認します。
1. 両方のサービスの F5/デバッグ セッションをすべて閉じます。ただし、Visual Studio ウィンドウでプロジェクトを開いたままにしておきます。
2. `mywebapi` プロジェクトがある Visual Studio ウィンドウに切り替え、Ctrl + F5 キーを押してデバッガーをアタッチせずにサービスを実行します。
3. `webfrontend` プロジェクトがある Visual Studio ウィンドウに切り替え、Ctrl + F5 キーを押してこのサービスも同様に実行します。

> [!Note]
> Ctrl + F5 キーを押して Web ページが最初に表示された後に、ブラウザーを更新することが必要な場合があります。

パブリック URL を開き、Web アプリに移動するすべてのユーザーは、既定の `default` スペースを使用して両方のサービスを介して実行される、作成済みのコード パスを呼び出します。 `mywebapi` の開発を続けるとします。開発空間を使用している他の開発者の作業を妨げずに続行するにはどうすればよいのでしょうか。 それには、独自のスペースを設定することです。

### <a name="create-a-new-dev-space"></a>新しい開発空間を作成する
Visual Studio 内から、F5 キーまたは Ctrl + F5 キーを押してサービスを実行するときに使用される追加のスペースを作成できます。 スペースには任意の名前を付けることができます。内容に合わせて自由に名前を付けることができます (例:  `sprint4`、`demo`)。

新しいスペースを作成するには、次の手順を実行します。
1. `mywebapi` プロジェクトがある Visual Studio ウィンドウに切り替えます。
2. **ソリューション エクスプローラー**でプロジェクトを右クリックし、**[プロパティ]** を選択します。
3. 左側の **[デバッグ]** タブを選択して、Azure Dev Spaces の設定を表示します。
4. ここから、F5 キーまたは Ctrl + F5 キーを押したときに使用されるクラスターやスペースを変更または作成できます。 *以前に作成した Azure Dev Space が選択されていることを確認します。*
5. [スペース] ドロップダウンで、**[<Create New Space…>]\(<新しいスペースの作成…>\)** を選択します。

    ![](media/get-started-netcore-visualstudio/Settings.png)

6. **[空間の追加]** ダイアログで、親の空間を **[default]\(既定\)** に設定し、新しい空間の名前を入力します。 誰が作業しているスペースであるかを同僚がわかるように、新しいスペースに自分の名前 (例: "scott") を使用できます。 Click **OK**.

    ![](media/get-started-netcore-visualstudio/AddSpace.png)

7. これで、AKS クラスターと、プロジェクトのプロパティ ページで選択した新しいスペースが表示されます。

    ![](media/get-started-netcore-visualstudio/Settings2.png)

### <a name="update-code-for-mywebapi"></a>*mywebapi* のコードを更新する

1. `mywebapi` プロジェクトで、`Controllers/ValuesController.cs` ファイルの `string Get(int id)` メソッドのコードを次のように変更します。
 
    ```csharp
    [HttpGet("{id}")]
    public string Get(int id)
    {
        return "mywebapi now says something new";
    }
    ```

2. この更新されたコード ブロックにブレークポイントを設定します (既に設定されている場合があります)。
3. F5 キーを押して、`mywebapi` サービスを開始します。 これにより、選択したスペース (ここでは `scott`) を使用してクラスターでサービスが開始されます。

さまざまなスペースのしくみを理解するうえで役立つ図を次に示します。 紫色のパスは、`default` スペースを使用した要求を示しています。この既定のパスは、URL の先頭にスペースが追加されていない場合に使用されます。 ピンク色のパスは、`default/scott` スペースを使用した要求を示しています。

![](media/common/Space-Routing.png)

Azure Dev Spaces のこの組み込み機能を使用すると、共有環境でコードをエンド ツー エンドでテストできます。各開発者が自分のスペースにサービスの完全なスタックを再作成する必要はありません。 このガイドの以前の手順で示したように、このルーティングではアプリ コードで伝達ヘッダーを転送する必要があります。

### <a name="test-code-running-in-the-defaultscott-space"></a>`default/scott` スペースで実行されているコードをテストする
新しいバージョンの `mywebapi` を `webfrontend` と共にテストするには、ブラウザーで `webfrontend` のパブリック アクセス ポイントの URL (例: http://webfrontend.123456abcdef.eastus.aksapp.io) を開き、About ページに移動します。 "Hello from webfrontend and Hello from mywebapi" という元のメッセージが表示されます。

URL に "scott.s." の部分を追加して、 http://scott.s.webfrontend.123456abcdef.eastus.aksapp.io になるようにし、ブラウザーを更新します。 `mywebapi` プロジェクトで設定したブレークポイントに到達します。 F5 キーを押して続行すると、"Hello from webfrontend and mywebapi now says something new" という新しいメッセージがブラウザーに表示されます。 これは、`mywebapi` で更新したコードのパスが `default/scott` スペースで実行されているためです。

[!INCLUDE[](includes/well-done.md)]

[!INCLUDE[](includes/clean-up.md)]
