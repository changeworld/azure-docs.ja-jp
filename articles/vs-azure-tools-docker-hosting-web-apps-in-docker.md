<properties
   pageTitle="Docker での Web アプリのホスト | Microsoft Azure"
   description="Visual Studio を使用して、Docker コンテナーで Web アプリケーションをホストする方法について説明します。"
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="02/10/2016"
   ms.author="tarcher" />

# Docker での Web アプリのホスト

[Docker](https://www.docker.com/whatisdocker/) は軽量のコンテナー エンジンで、アプリケーションとサービスをホストするために使用できる仮想マシンにいくつかの点で似ています。Visual Studio は、Ubuntu、CoreOS、および Windows での Docker をサポートしています。

この例では、**Visual Studio 2015 Tools for Docker** 拡張機能を使用して、Azure 上の Ubuntu Linux 仮想マシン (ここでは Docker ホストと呼ぶ) に、ASP.NET 5 アプリケーションを発行する方法を示します。同じ方法を利用して、Windows コンテナーに発行することができます。

Docker ホストへのアプリケーションの発行後、Docker コマンドライン ツールを使用して、アプリケーションの発行先コンテナーと対話できます。

## 新しい Docker コンテナーの作成と発行

以下のセクションで、新しい ASP.NET 5 Web アプリケーション プロジェクトを作成し、コンテナー ホストを作成してから、Docker コンテナー内に Web アプリケーション プロジェクトを構築して実行します。初めに、[Visual Studio 2015 Tools for Docker](https://aka.ms/DockerToolsForVS) をダウンロードし、インストールします。

### ASP.NET 5 Web アプリケーション プロジェクトの追加

1. Visual Studio のメニューで、**[ファイル]、[新規作成]、[プロジェクト]** の順に選択します。 

1. **[新しいプロジェクト]** ダイアログ ボックスの **[テンプレート]** セクションで、**[Visual C#]**、**[Web]** の順に選択します。

1. **[ASP.NET Web アプリケーション]** を選択し、新しいプロジェクトに名前を付けます(この記事のスクリーンショットでは既定の名前である **WebApplication1** が使用されています)。

1. **[OK]** をタップします。

1. Web アプリケーションは Docker でホストされ、実行されるため、**[クラウドでのホスト]** チェック ボックスが選択されている場合はオフにしてから、**[OK]** をタップします。

  ![][0]

  この時点で、一般に Web アプリケーションにコードを追加して、何らかの有益なことを実行しますが、この例では、Docker に集中するため既定の設定のままにしておきます(既存の ASP.NET 5 Web アプリケーションを使用するように選択することもできます)。

### プロジェクトの発行
プロジェクトが作成されたら (または既存のプロジェクトを開いたら)、次の手順に従って、Azure で Docker コンテナーにプロジェクトを発行します。

1. Visual Studio のソリューション エクスプローラーで、プロジェクトを右クリックし、コンテキスト メニューの **[発行]** を選択します。

1. **[Web の発行]** ダイアログ ボックスの **[発行先の選択]** セクションで、**[Docker コンテナー]** をタップします。

    [Docker コンテナー] オプションが表示されない場合は、Visual Studio 2015 Tools for Docker をインストールしており、前のセクションで、ASP.NET 5 Web サイト テンプレートを選択していることを確認してください。

    ![][1]

    **[Docker Virtual Machine の選択]** ダイアログ ボックスで、プロジェクトを発行する Docker ホストを指定できます。新しい Docker ホストを作成することも、Azure または他の場所でホストされている既存の VM を選択することもできます。この例の後で、新しい Azure Docker ホストを作成します。

1. Azure アカウントに既にログインしている場合は、手順 5 にスキップします。アカウントにログインしていない場合は、**[アカウントの追加]** をタップします。

    ![][2]

1. **[Visual Studio にサインイン]** ダイアログ ボックスで、Azure サブスクリプションの電子メール アカウントを入力し、**[続行]** をタップします。

1. **[新規]** をタップして新しい Azure Docker VM を作成し、**[OK]** をタップします。

    ![][3]

    既存の Docker ホストを使用する選択もできることに注意してください。それには、**[新規]** をタップするのではなく、**[既存の Azure Docker Virtual Machines]** ドロップダウン リストで該当するホストを選択します。なお、リストにはコンテナー ホストだけでなく、Azure テナントのすべての VM が表示される点に注意してください。

    代わりに、カスタム Docker ホストを発行するように選択することもできます。詳細については、このトピックで後述する「[カスタム Docker ホストの指定](#provide-a-custom-docker-host)」セクションを参照してください。

1. **[Microsoft Azure で仮想マシンを作成します]** ダイアログ ボックスに次の情報を入力します。完了したら **[OK]** をタップします。これにより、構成済みの Docker 拡張機能で Linux 仮想マシンが作成されます。

    ![][4]

    Windows Server 2016 Technical Preview 3 (TP3) を使用して Windows コンテナー ホストを作成するオプションもあることに注意してください。

    ![][8]

	|プロパティ名|設定|
	|---|---|
	|場所|この設定をロケールに最も近いリージョンに変更します。|
	|DNS 名|仮想マシンの一意の名前を入力します。名前が Azure によって受け入れられると、右側に白いチェックマークの付いた緑の丸が表示されます。名前が受け入れられない場合は、白い x 印の付いた赤い丸が表示されます。その場合は、新しい一意の名前を入力します。|
	|イメージ|Docker ホストで使用する OS イメージがある場合は、それを選択します。この例では、Ubuntu Server イメージを選択します(使用可能なイメージの一覧で Windows Server イメージが使用できるようになったことに注意してください)。|
	|ユーザー名|仮想マシンの一意のユーザー名を入力します。|
	|パスワード|ユーザーのパスワードを入力し、それを確認します。|
	|証明書ディレクトリ |これは、Docker 証明書を格納するディレクトリを指定します。新しいディレクトリを作成したり、既存のディレクトリをポイントしたりすることができますが、既定の証明書ディレクトリ (C:\\Users\\[*ユーザー名*]\\.docker) を使用することをお勧めします。そうでないと、別のプロジェクトまたはシステムで同じホストを再利用する場合に、認証オプションを自動的に取得できません。|

1. **[証明書ディレクトリ]** エントリの横の省略記号 (...) をタップし、Docker 証明書の新しいディレクトリを作成するか、既存の Docker 証明書ディレクトリに移動します。

    VM に必要な Docker 証明書が見つからない場合、エントリの横に感嘆符のアイコンが表示され、必要な証明書が見つからず、続行すると、すべての既存の証明書が削除され、再作成されることが通知されます。

1. **[OK]** をタップして VM の作成を開始します。Azure で、仮想マシンが作成されていることを示すメッセージが表示されます。

    Visual Studio によって、Azure リソース マネージャー (ARM) テンプレート ファイル、パラメーター ファイル、および PowerShell スクリプトが作成されるため、後でコマンドを再実行できます。

    ![][7]

    操作の進捗状況は、**[出力]** ウィンドウに出力されます。Visual Studio は、VM をデプロイする PowerShell スクリプトを呼び出します。このスクリプトでは、Azure PowerShell コマンドレットを使用して、Azure リソース グループをデプロイします。後で、ホストを手動で作成した場合とまったく同じように、別の PowerShell スクリプトで、Docker コマンドを使用して発行します。

    Docker ホストのプロビジョニングには、しばらく時間がかかるため、ジョブが完了したら、Visual Studio の出力ウィンドウでステータスを確認します。

1. Azure で Docker ホストを完全にプロビジョニングしたら、Azure ポータルで自分のアカウントを確認できます。Azure ポータルの **[仮想マシン]** カテゴリで、新しい仮想マシンを確認できます。

1. Docker ホストの準備ができたら、戻って、Web アプリケーション プロジェクトを発行します。**ソリューション エクスプローラー**の Web アプリケーション プロジェクトのノードのコンテキスト メニューで、**[発行]** をタップします。Visual Studio では、作成した VM に基づいて、発行ファイルが作成されます。

1. **[Web の発行]** ダイアログ ボックスの **[接続]** タブで、**[接続の検証]** をタップして、Docker ホストの準備ができていることを確認します。接続が正常な場合は、**[発行]** をタップして、Web アプリケーションを発行します。

    初めてアプリケーションを Docker に発行する場合は、Docker ファイルで参照されている基本イメージ (**FROM** *imagename* など) のダウンロードに時間がかかります。

    Docker ファイルは、オペレーティング システムに固有であることに注意してください。別の OS に再発行することを選択した場合は、Visual Studio が対象の OS に基づいて、新しい既定のファイルを作成できるように、Docker ファイルの名前を変更する必要があります。たとえば、最初に Linux コンテナーに発行し、後で Windows に発行することにした場合、Docker ファイルの名前を DockerLinux などのわかりやすい一意の名前に変更する必要があります。それにより、Windows に再発行すると、Visual Studio によって、Windows 用の既定の Docker ファイルが再作成されます。後で、いずれかのファイルを再発行するときに、その OS 用の適切な Docker ファイルを選択するだけです。

## カスタム Docker ホストの指定

前のセクションで、Azure でホストされる Docker 仮想マシンを作成する方法を説明しました。ただし、既にどこかに Docker ホストが存在している場合は、Azure の代わりにそのホストに発行するように選択できます。

### カスタム Docker ホストを指定する方法

1. **[Docker 仮想マシンの選択]** ダイアログ ボックスで、**[カスタム Docker ホスト]** チェック ボックスをオンにします。

    ![][5]

1. **[OK]** をタップします。

1. **[Web の発行]** ダイアログ ボックスで、**[CustomDockerHost]** セクションの設定に値 (サーバーの URL、イメージ名、Dockerfile の場所、ホストとコンテナーのポート番号など) を追加します。

    **[Docker の詳細オプション]** セクションで、認証オプション、実行オプション、および Docker コマンド ラインを表示したり、変更したりすることができます。

    ![][6]

1. 必要な値をすべて入力したら、**[接続の検証]** をタップし、Docker ホストへの接続が正常に機能していることを確認します。

1. 接続が正常に機能している場合は、**[次へ]** をタップすると、発行されるコンポーネントの一覧を表示できます。また、**[発行]** をタップすると、すぐにプロジェクトを発行できます。

## Docker ホストのテスト

これでプロジェクトは Azure 上の Docker ホストに発行されたので、その設定を確認してテストできます。Docker コマンド ライン ツールは Visual Studio 拡張機能と共にインストールされるため、Windows コマンド プロンプトから直接 Docker にコマンドを発行できます。

次の手順は、Azure にデプロイされた Docker ホストと通信するためのものです。

### Docker ホストをテストする方法

1. Windows コマンド プロンプトを開きます。

1. Docker ホストを割り当て、環境変数を検証します。そのためには、コマンド プロンプトに次のコマンドを入力します(*NameofAzureVM* は Docker ホストの名前に、*Region* は Docker ホストを作成したリージョンに置き換えます)。

    ```
    Set DOCKER_HOST=tcp://[NameOfAzureVM].[Region].cloudapp.azure.com:2376
    Set DOCKER_TLS_VERIFY=1
    ```

    これらの環境変数を設定しておくと、Docker コマンドを発行するたびにこの情報を手動で指定する必要がなくなります。

1. ここで、次のようなコマンドを発行して、Docker ホストが存在し、機能しているかどうかをテストできます。

	|コマンド ライン|説明|
	|---|---|
	|`docker info`|Docker バージョン情報を取得します。|
	|`docker ps`|実行中のコンテナーの一覧を取得します。|
	|`docker ps –a`|停止しているコンテナーを含むコンテナーの一覧を取得します。|
	|`docker logs <Docker container name>`|指定されたコンテナーのログを取得します。|
	|`docker images`|イメージの一覧を取得します。|

    すべての Docker コマンドの一覧については、コマンド プロンプトで `docker` コマンドを入力し、**Enter** キーを押します。詳細については、[Docker コマンド ラインに関するドキュメント](https://docs.docker.com/reference/commandline/cli/)を参照してください。

## 次のステップ

これで Docker ホストが用意できたので、Docker コマンドをそれに対して発行できます。Docker の詳細については、[Docker のドキュメント](https://docs.docker.com/)と [Docker のオンライン チュートリアル](https://www.docker.com/tryit/)を参照してください。

Azure での Linux 用 Docker VM 拡張機能の使用の詳細については、「[Azure での Linux 用 Docker 仮想マシン拡張機能](virtual-machines/virtual-machines-linux-dockerextension.md)」を参照してください。

Visual Studio での Docker の使用に関する問題については、「[Visual Studio を使用した Windows での Docker クライアント エラーのトラブルシューティング](vs-azure-tools-docker-troubleshooting-docker-errors.md)」を参照してください。

[0]: ./media/vs-azure-tools-docker-hosting-web-apps-in-docker/IC796678.png
[1]: ./media/vs-azure-tools-docker-hosting-web-apps-in-docker/IC796679.png
[2]: ./media/vs-azure-tools-docker-hosting-web-apps-in-docker/IC796680.png
[3]: ./media/vs-azure-tools-docker-hosting-web-apps-in-docker/IC796681.png
[4]: ./media/vs-azure-tools-docker-hosting-web-apps-in-docker/IC796682.png
[5]: ./media/vs-azure-tools-docker-hosting-web-apps-in-docker/IC796683.png
[6]: ./media/vs-azure-tools-docker-hosting-web-apps-in-docker/IC796684.png
[7]: ./media/vs-azure-tools-docker-hosting-web-apps-in-docker/IC796685.png
[8]: ./media/vs-azure-tools-docker-hosting-web-apps-in-docker/IC796686.png

<!---HONumber=AcomDC_0323_2016-->