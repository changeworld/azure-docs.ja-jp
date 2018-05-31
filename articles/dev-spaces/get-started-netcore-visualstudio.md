---
title: .NET Core と Visual Studio を使用してクラウドに Kubernetes 開発環境を作成する | Microsoft Docs
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: ghogen
ms.author: ghogen
ms.date: 05/11/2018
ms.topic: tutorial
description: Azure のコンテナーとマイクロサービスを使用した迅速な Kubernetes 開発
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, コンテナー
manager: douge
ms.openlocfilehash: 012efcbd3fa87268f3a68fdac524ce8310d10120
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/20/2018
ms.locfileid: "34362058"
---
# <a name="get-started-on-azure-dev-spaces-with-net-core-and-visual-studio"></a>Azure Dev Spaces での .NET Core と Visual Studio の使用

このガイドでは、以下の方法について説明します。

- 開発に最適化された Kubernetes ベースの環境を Azure に作成する。
- Visual Studio を使用して、コンテナーでコードを繰り返し開発する。
- 2 つのサービスを個別に開発し、Kubernetes の DNS サービス検索を使用して別のサービスを呼び出す。
- チーム環境でコードを生産的に開発してテストする。

[!INCLUDE[](includes/see-troubleshooting.md)]

[!INCLUDE[](includes/portal-aks-cluster.md)]

## <a name="get-the-visual-studio-tools"></a>Visual Studio Tools を入手する
1. 最新バージョンの [Visual Studio 2017](https://www.visualstudio.com/vs/) をインストールします。
1. Visual Studio インストーラーで、次のワークロードが選択されていることを確認します。
    * ASP.NET および Web の開発
1. [Azure Dev Spaces 用 Visual Studio 拡張機能](https://aka.ms/get-azds-visualstudio)をインストールします。

これで、Visual Studio を使用して ASP.NET Web アプリを作成する準備ができました。

## <a name="create-an-aspnet-web-app"></a>ASP.NET Web アプリを作成する

Visual Studio 2017 内から新しいプロジェクトを作成します。 現時点では、このプロジェクトは **ASP.NET Core Web アプリケーション**である必要があります。 プロジェクトに "**webfrontend**" という名前を付けます。

![](media/get-started-netcore-visualstudio/NewProjectDialog1.png)

**[Web アプリケーション (モデル ビュー コントローラー)]** テンプレートを選択し、ダイアログの上部にある 2 つのドロップダウンで **.NET Core** と **ASP.NET Core 2.0** が対象になっていることを確認します。 **[OK]** をクリックしてプロジェクトを作成します。

![](media/get-started-netcore-visualstudio/NewProjectDialog2.png)


## <a name="create-a-dev-environment-in-azure"></a>Azure に開発環境を作成する

Azure Dev Spaces を使用すると、Azure によって完全に管理され、開発に最適化された、Kubernetes ベースの開発環境を作成できます。 先ほど作成したプロジェクトを開いた状態で、次のように、起動設定ドロップダウンから **[Azure Dev Spaces]** を選択します。

![](media/get-started-netcore-visualstudio/LaunchSettings.png)

次に表示されるダイアログで、適切なアカウントでサインインしていることを確認し、既存の Kubernetes クラスターを選択します。

![](media/get-started-netcore-visualstudio/Azure-Dev-Spaces-Dialog.PNG)

現時点では、**[スペース]** ドロップダウンは既定の `default` のままにしておきます。 このオプションについては、後ほど詳しく説明します。 パブリック エンドポイント経由で Web アプリにアクセスできるように、**[Publicly Accessible]\(パブリックにアクセス可能\)** チェック ボックスをオンにします。 この設定は必須ではありませんが、このチュートリアルの後半でいくつかの概念を示す際に役立ちます。 いずれにせよ、Visual Studio を使用して Web サイトをデバッグできるので心配はありません。

![](media/get-started-netcore-visualstudio/Azure-Dev-Spaces-Dialog2.png)

**[OK]** をクリックして、クラスターを選択または作成します。

Azure Dev Spaces を使用できないクラスターを選択すると、クラスターを構成するかどうかをたずねるメッセージが表示されます。

![](media/get-started-netcore-visualstudio/Add-Azure-Dev-Spaces-Resource.png)

**[OK]** をクリックします。

 これを実行するためにバックグラウンド タスクが開始されます。 このタスクが完了するまで数分かかります。 まだ作成中かどうかを確認するには、次の画像に示すように、ステータス バーの左下隅にある **[バックグラウンド タスク]** アイコンをポイントします。

![](media/get-started-netcore-visualstudio/BackgroundTasks.PNG)

> [!Note]
> 開発環境が正常に作成されるまで、アプリケーションをデバッグすることはできません。

## <a name="look-at-the-files-added-to-project"></a>プロジェクトに追加されたファイルを確認する
開発環境が作成されるのを待っている間に、開発環境の使用を選択したときにプロジェクトに追加されたファイルを確認しましょう。

まず、`charts` という名前のフォルダーが追加されており、このフォルダー内にアプリケーションの [Helm チャート](https://docs.helm.sh)がスキャフォールディングされていることがわかります。 これらのファイルは、アプリケーションを開発環境に展開するために使用されます。

`Dockerfile` という名前のファイルが追加されています。 このファイルには、標準の Docker 形式でアプリケーションをパッケージ化するために必要な情報が含まれています。 `HeaderPropagation.cs` ファイルも作成されています。このファイルについては、このチュートリアルで後ほど説明します。 

最後に、`azds.yaml` という名前のファイルがあります。このファイルには、開発環境で必要な構成情報 (パブリック エンドポイント経由でアプリケーションにアクセス可能かどうかなど) が含まれています。

![](media/get-started-netcore-visualstudio/ProjectFiles.png)

## <a name="debug-a-container-in-kubernetes"></a>Kubernetes でコンテナーをデバッグする
開発環境が正常に作成されたら、アプリケーションをデバッグできます。 コードにブレークポイントを設定します。たとえば、`HomeController.cs` ファイルの 20 行目 (`Message` 変数が設定されている行) に設定します。 **F5** キーを押してデバッグを開始します。 

Visual Studio は開発環境と通信し、アプリケーションをビルドして展開した後、実行中の Web アプリでブラウザーを開きます。 コンテナーはローカルで実行されているように見えますが、実際には Azure の開発環境で実行されています。 localhost アドレスである理由は、Azure Dev Spaces では Azure で実行されているコンテナーへの一時的な SSH トンネルが作成されるためです。

ページの上部にある **[About]** リンクをクリックすると、ブレークポイントがトリガーされます。 コードがローカルで実行されている場合と同様に、デバッグ情報 (呼び出し履歴、ローカル変数、例外情報など) にフル アクセスできます。

## <a name="call-another-container"></a>別のコンテナーを呼び出す
このセクションでは、2 つ目のサービス (`mywebapi`) を作成し、`webfrontend` でそのサービスを呼び出します。 各サービスは別々のコンテナーで実行されます。 その後、両方のコンテナーでデバッグします。

![](media/common/multi-container.png)

## <a name="download-sample-code-for-mywebapi"></a>*mywebapi* のサンプル コードをダウンロードする
時間を節約するために、GitHub リポジトリからサンプル コードをダウンロードしましょう。 https://github.com/Azure/dev-spaces に移動し、**[Clone or download]** をクリックして GitHub リポジトリをダウンロードします。 このセクションのコードは、`samples/dotnetcore/getting-started/mywebapi` にあります。

## <a name="run-mywebapi"></a>*mywebapi* を実行する
1. "*別の Visual Studio ウィンドウ*" で `mywebapi` プロジェクトを開きます。
1. 前に `webfrontend` プロジェクトで行ったように、起動設定ドロップダウンから **[Azure Dev Spaces]** を選択します。 今回は新しい開発環境を作成するのではなく、作成済みのものを選択します。 前述のように、[スペース] は既定の `default` のままにし、**[OK]** をクリックします。 [出力] ウィンドウを見ると、デバッグを開始したときの作業速度を上げるために、Visual Studio によって開発環境でこの新しいサービスの "ウォーム アップ" が開始されたことがわかります。
1. F5 キーを押し、サービスがビルドされ、展開されるまで待ちます。 準備ができると、Visual Studio のステータス バーがオレンジ色に変わります。
1. **[出力]** ウィンドウの **[Azre Dev Spaces for AKS]** ウィンドウに表示されるエンドポイント URL を書き留めます。 これは、http://localhost:\<portnumber\> のように表示されます。 コンテナーはローカルで実行されているように見えますが、実際には Azure の開発環境で実行されています。
2. `mywebapi` の準備ができたら、ブラウザーで localhost アドレスを開き、URL に `/api/values` を追加して `ValuesController` の既定の GET API を呼び出します。 
3. すべての手順が正常に完了すると、次のような `mywebapi` サービスからの応答を確認できます。

    ![](media/get-started-netcore-visualstudio/WebAPIResponse.png)

## <a name="make-a-request-from-webfrontend-to-mywebapi"></a>*webfrontend* から *mywebapi* に対して要求を行う
次に、`mywebapi` に対して要求を行う `webfrontend` のコードを記述しましょう。 `webfrontend` プロジェクトがある Visual Studio ウィンドウに切り替えます。 `HomeController.cs` ファイルで、About メソッドのコードを次のコードに*置き換えます*。

   ```csharp
   public async Task<IActionResult> About()
   {
      ViewData["Message"] = "Hello from webfrontend";

      // Use HeaderPropagatingHttpClient instead of HttpClient so we can propagate
      // headers in the incoming request to any outgoing requests
      using (var client = new HeaderPropagatingHttpClient(this.Request))
      {
          // Call *mywebapi*, and display its response in the page
          var response = await client.GetAsync("http://mywebapi/api/values/1");
          ViewData["Message"] += " and " + await response.Content.ReadAsStringAsync();
      }

      return View();
   }
   ```

サービスを `http://mywebapi` として参照するために、Kubernetes の DNS サービス検索がどのように使用されるのかに注意してください。 **開発環境のコードは、運用環境で実行される場合と同様に実行されます。**

上記のコード例では、`HeaderPropagatingHttpClient` クラスも使用しています。 このヘルパー クラスは、Azure Dev Spaces を使用するようにプロジェクトを構成したときに追加された `HeaderPropagation.cs` ファイルです。 `HeaderPropagatingHttpClient` は、既知の `HttpClient` クラスから派生したものです。このクラスにより、既存の ASP.NET HttpRequest オブジェクトから送信 HttpRequestMessage オブジェクトに特定のヘッダーを伝達する機能が追加されます。 これにより、チーム シナリオでの生産性の高い開発エクスペリエンスがどのように促進されるのかについては、後ほど説明します。

## <a name="debug-across-multiple-services"></a>複数のサービスでデバッグする
1. この時点で、`mywebapi` は引き続きデバッガーがアタッチされた状態で実行されています。 そうでない場合は、`mywebapi` プロジェクトで F5 キーを押します。
1. `api/values/{id}` の GET 要求を処理する、`ValuesController.cs` ファイルの `Get(int id)` メソッドにブレークポイントを設定します。
1. 上記のコードを貼り付けた `webfrontend` プロジェクトで、GET 要求を `mywebapi/api/values` に送信する直前にブレークポイントを設定します。
1. `webfrontend` プロジェクトで F5 キーを押します。 Visual Studio はブラウザーで適切な localhost ポートを再度開き、Web アプリが表示されます。
1. ページの上部にある **[About]** リンクをクリックすると、`webfrontend` プロジェクトのブレークポイントがトリガーされます。 
1. F10 キーを押して続行します。 `mywebapi` プロジェクトのブレークポイントがトリガーされます。
1. F5 キーを押して続行し、`webfrontend` プロジェクトのコードに戻ります。
1. F5 キーをもう一度押すと要求が完了し、ブラウザーでページが返されます。 Web アプリの About ページに、2 つのサービスによって連結されたメッセージ ("Hello from webfrontend and Hello from mywebapi") が表示されます。

お疲れさまでした。 これで、各コンテナーを個別に開発して展開できる、複数コンテナー アプリケーションが作成されました。

## <a name="learn-about-team-development"></a>チーム開発について学ぶ

ここまでは、アプリの開発に取り組んでいる開発者が 1 人だけであるかのようにアプリケーションのコードを実行していました。 このセクションでは、Azure Dev Spaces によるチーム開発の効率化について説明します。
* 開発者のチームが同じ開発環境で作業できるようにする。
* 各開発者が、他の開発者の作業の妨げとなることを心配せずに、単独で各自のコードを反復処理できるようにする。
* モックを作成したり、依存関係をシミュレートしたりする必要なく、コードをコミットする前に、エンド ツー エンドでコードをテストする。

### <a name="challenges-with-developing-microservices"></a>マイクロサービスの開発に伴う課題
現時点では、サンプル アプリケーションはそれほど複雑ではありません。 しかし、実際の開発では、さらに多くのサービスが追加され、開発チームが拡大するにつれて、すぐに課題が生じます。

他の多くのサービスと対話するサービスの開発に取り組む自分の姿を想像してみてください。

- 開発のためにすべてをローカルで実行するのは非現実的になる可能性があります。 開発用コンピューターに、アプリ全体を実行できるだけのリソースがない場合があります。 また、アプリに、パブリックに到達できる必要があるエンドポイントがある場合もあります (アプリで SaaS アプリからの webhook に応答するなど)。
- 依存するサービスだけを実行するよう試みることはできますが、その場合、依存関係の完全なクロージャ (依存関係の依存関係など) を把握しておく必要があります。 あるいは、依存関係を扱っていなかったために、依存関係を構築し、実行する方法が簡単にはわからないという問題が生じます。
- サービスの依存関係の多くをシミュレートしたり、モックを作成したりする開発者もいます。 これが役立つ場合もありますが、モックの管理に開発労力を費やすことになる可能性があります。 さらに、開発環境と運用環境が異なって見えるようになり、微妙なバグが入り込む可能性もあります。
- その結果、あらゆる種類のエンド ツー エンド テストの実行が困難になります。 実際には、統合テストはコミット後にのみ行われる可能性があります。つまり、開発サイクルの後半に問題が発生します。

    ![](media/common/microservices-challenges.png)

### <a name="work-in-a-shared-development-environment"></a>共有開発環境で作業する
Azure Dev Spaces を使用すると、Azure に "*共有*" 開発環境を設定できます。 各開発者は、アプリケーションの自分の担当部分だけに集中できます。また、自分のシナリオが依存する他のすべてのサービスとクラウド リソースが既に含まれている環境で、"*事前コミット コード*" を繰り返し開発できます。 依存関係は常に最新の状態であり、開発者は運用環境を反映した方法で作業を行います。

### <a name="work-in-your-own-space"></a>自分のスペースで作業する
サービスのコードを開発するときには、チェックインできる状態になるまで、多くの場合、コードは良好な状態ではありません。 引き続きコードの調整とテストを繰り返し、ソリューションを試します。 Azure Dev Spaces では、**スペース**という概念が導入されています。スペースを使用すると、開発者はチーム メンバーの作業の妨げとなることを心配せずに、単独で作業を行うことができます。

次の手順を実行して、`webfrontend` サービスと `mywebapi` サービスが、どちらも開発環境と **`default` スペース**で実行されていることを確認します。
1. 両方のサービスの F5/デバッグ セッションをすべて閉じます。ただし、Visual Studio ウィンドウでプロジェクトを開いたままにしておきます。
2. `mywebapi` プロジェクトがある Visual Studio ウィンドウに切り替え、Ctrl + F5 キーを押してデバッガーをアタッチせずにサービスを実行します。
3. `webfrontend` プロジェクトがある Visual Studio ウィンドウに切り替え、Ctrl + F5 キーを押してこのサービスも同様に実行します。

> [!Note]
> Ctrl + F5 キーを押して Web ページが最初に表示された後に、ブラウザーを更新することが必要な場合があります。

パブリック URL を開き、Web アプリに移動するすべてのユーザーは、既定の `default` スペースを使用して両方のサービスを介して実行される、作成済みのコード パスを呼び出します。 `mywebapi` の開発を続けるとします。開発環境を使用している他の開発者の作業を妨げずに続行するにはどうすればよいのでしょうか。 そのためには、独自のスペースを設定します。

### <a name="create-a-new-space"></a>新しいスペースを作成する
Visual Studio 内から、F5 キーまたは Ctrl + F5 キーを押してサービスを実行するときに使用される追加のスペースを作成できます。 スペースには任意の名前を付けることができます。内容に合わせて自由に名前を付けることができます (例:  `sprint4`、`demo`)。

新しいスペースを作成するには、次の手順を実行します。
1. `mywebapi` プロジェクトがある Visual Studio ウィンドウに切り替えます。
2. **ソリューション エクスプローラー**でプロジェクトを右クリックし、**[プロパティ]** を選択します。
3. 左側の **[デバッグ]** タブを選択して、Azure Dev Spaces の設定を表示します。
4. ここから、F5 キーまたは Ctrl + F5 キーを押したときに使用されるクラスターやスペースを変更または作成できます。 *以前に作成した Azure Dev Space が選択されていることを確認します。*
5. [スペース] ドロップダウンで、**[<Create New Space…>]\(<新しいスペースの作成…>\)** を選択します。

    ![](media/get-started-netcore-visualstudio/Settings.png)

6. **[スペースの追加]** ダイアログでスペースの名前を入力し、**[OK]** をクリックします。 誰が作業しているスペースであるかを同僚がわかるように、新しいスペースに自分の名前 (例: "scott") を使用できます。

    ![](media/get-started-netcore-visualstudio/AddSpace.png)

7. これで、開発環境と、プロジェクトのプロパティ ページで選択した新しいスペースが表示されます。

    ![](media/get-started-netcore-visualstudio/Settings2.png)

### <a name="update-code-for-mywebapi"></a>*mywebapi* のコードを更新する

1. `mywebapi` プロジェクトで、`ValuesController.cs` ファイルの `string Get(int id)` メソッドのコードを次のように変更します。
 
    ```csharp
    [HttpGet("{id}")]
    public string Get(int id)
    {
        return "mywebapi now says something new";
    }
    ```

2. この更新されたコード ブロックにブレークポイントを設定します (既に設定されている場合があります)。
3. F5 キーを押して、`mywebapi` サービスを開始します。 これにより、選択したスペース (ここでは `scott`) を使用して開発環境でサービスが開始されます。

さまざまなスペースのしくみを理解するうえで役立つ図を次に示します。 青色のパスは、`default` スペースを使用した要求を示しています。この既定のパスは、URL の先頭にスペースが追加されていない場合に使用されます。 緑色のパスは、`scott` スペースを使用した要求を示しています。

![](media/common/Space-Routing.png)

Azure Dev Spaces のこの組み込み機能を使用すると、共有環境でコードをエンド ツー エンドでテストできます。各開発者が自分のスペースにサービスの完全なスタックを再作成する必要はありません。 このガイドの以前の手順で示したように、このルーティングではアプリ コードで伝達ヘッダーを転送する必要があります。

### <a name="test-code-running-in-the-scott-space"></a>`scott` スペースで実行されているコードをテストする
新しいバージョンの `mywebapi` を `webfrontend` と共にテストするには、ブラウザーで `webfrontend` のパブリック アクセス ポイントの URL (例: http://webfrontend-teamenv.123456abcdef.eastus.aksapp.io) を開き、About ページに移動します。 "Hello from webfrontend and Hello from mywebapi" という元のメッセージが表示されます。

URL に "scott.s." の部分を追加して、 http://scott.s.webfrontend-teamenv.123456abcdef.eastus.aksapp.io になるようにし、ブラウザーを更新します。 `mywebapi` プロジェクトで設定したブレークポイントに到達します。 F5 キーを押して続行すると、"Hello from webfrontend and mywebapi now says something new" という新しいメッセージがブラウザーに表示されます。 これは、`mywebapi` で更新したコードのパスが `scott` スペースで実行されているためです。

[!INCLUDE[](includes/well-done.md)]

[!INCLUDE[](includes/clean-up.md)]
