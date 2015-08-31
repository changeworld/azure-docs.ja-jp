<properties
   pageTitle="Docker での Web Apps のホスト | Microsoft Azure"
   description="Visual Studio を使用して、Docker コンテナーで Web アプリケーションをホストする方法について説明します。"
   services="visual-studio-online"
   documentationCenter="na"
   authors="kempb"
   manager="douge"
   editor="tglee" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="08/17/2015"
   ms.author="kempb" />

# Docker での Web Apps のホスト

[Docker](https://www.docker.com/whatisdocker/) は軽量のコンテナー エンジンで、アプリケーションとサービスをホストするために使用できる仮想マシンにいくつかの点で似ています。Visual Studio は、Ubuntu、CoreOS、および Windows での Docker をサポートしています。

この例では、Visual Studio 2015 Tools for Docker 拡張機能を使用して、ASP.NET 5 Web アプリケーションと共に Docker 拡張機能がインストールされている Azure 上の Ubuntu Linux 仮想マシン (ここでは Docker ホストと呼ぶ) に、ASP.NET 5 アプリケーションを発行する方法を示します。同じ方法を利用して、Windows コンテナーに発行することができます。

アプリケーションは、Azure でホストされる新しい Docker ホストに発行することも、**[カスタム ホスト]** 設定を使用して、オンプレミス サーバー、Hyper-V、または Boot2Docker ホストに発行することもできます。Docker ホストへのアプリケーションの発行後、Docker コマンドライン ツールを使用して、アプリケーションの発行先コンテナーと対話できます。

## 新しい Docker コンテナーの作成と発行

以下の手順で、新しい ASP.NET 5 Web アプリケーション プロジェクトを作成し、コンテナー ホストを作成してから、Docker コンテナー内に Web アプリケーション プロジェクトを構築して実行します。初めに、[Visual Studio 2015 Tools for Docker](aka.ms/DockerToolsForVS) をダウンロードして、インストールします。

### ASP.NET 5 Web アプリケーション プロジェクトの追加

1. 新しい ASP.NET Web アプリケーション プロジェクトを作成します。メイン メニューで、**[ファイル]**、**[新しいプロジェクト]** の順に選択します。**[C\#]**、**[Web]** で、**[ASP.NET Web アプリケーション]** を選択します。

1. **[ASP.NET 5 プレビュー テンプレート]** のリストの **[Web サイト]** を選択します。

1. Web アプリケーションは Docker でホストされ、実行されるため、**[クラウドでのホスト]** チェック ボックスが選択されている場合はオフにしてから、**[OK]** ボタンを選択します。

  ![][0]

  この時点で、一般に Web アプリケーションにコードを追加して、何らかの有益なことを実行しますが、この例では、単に既定の設定のままにしておきます(既存の ASP.NET 5 Web Apps を使用するように選択することもできます)。

### プロジェクトの発行

1. ASP.NET プロジェクトのコンテキスト メニューで、**[発行]** を選択します。

1. **[Web の発行]** ダイアログ ボックスの **[発行先の選択]** セクションで、**[Docker コンテナー]** ボタンを選択します。

    [Docker コンテナー] オプションが表示されない場合は、Visual Studio 2015 Tools for Docker をインストールしており、前の手順で、ASP.NET 5 Web サイト テンプレートを選択していることを確認してください。

    ![][1]

    **[Docker Virtual Machine の選択]** ダイアログ ボックスが表示されます。これにより、プロジェクトを発行する Docker ホストを指定できます。新しい Docker ホストを作成することも、Azure または他の場所でホストされている既存の VM を選択することもできます。この例の後で、新しい Azure Docker ホストを作成します。

1. Azure アカウントに既にログインしている場合は、手順 5 にスキップします。アカウントにログインしていない場合は、**[アカウントの追加]** ボタンを選択します。

    ![][2]

1. **[Visual Studio にサインイン]** ダイアログ ボックスで、Azure サブスクリプションの電子メール アカウントを入力し、**[続行]** ボタンを選択します。

1. **[新規]** ボタンを選択して、新しい Azure Docker VM を作成し、**[OK]** ボタンを選択します。

    ![][3]

    既存の Docker ホストを使用する選択もできることに注意してください。そうするには、**[新規]** ボタンをクリックするのではなく、**[既存の Azure Docker Virtual Machines]** ドロップダウン リストでそれを選択します。このリストには、コンテナー ホストのみが表示されるのではなく、Azure テナントのすべての VM が一覧表示されます。

    代わりに、カスタム Docker ホストを発行するように選択することもできます。詳細については、このトピックで後述する[「カスタム Docker ホストの指定」](#BKMK_CustomHost)を参照してください。

1. **[Microsoft Azure で仮想マシンを作成します**] ダイアログ ボックスに次の情報を入力します。終了したら、**[OK]** ボタンを選択します。これにより、構成済みの Docker 拡張機能で Linux 仮想マシンが作成されます。

    ![][4]

    Windows Server 2016 Technical Preview 3 (TP3) を使用して Windows Container HOST を作成するオプションもあることに注意してください。

    ![][8]

    |プロパティ名|設定|
    |---|---|
    |場所|この設定をロケールに最も近いリージョンに変更します。|
    |DNS 名|仮想マシンの一意の名前を入力します。名前が Azure によって受け入れられると、右側に白いチェックマークの付いた緑の丸が表示されます。名前が受け入れられない場合は、白い x 印の付いた赤い丸が表示されます。その場合は、新しい一意の名前を入力します。|
    |イメージ|Docker ホストで使用する OS イメージがある場合は、それを選択します。この例では、Ubuntu Server イメージを選択します(使用可能なイメージの一覧で Windows Server イメージが使用できるようになったことに注意してください)。|
    |ユーザー名|仮想マシンの一意のユーザー名を入力します。|
    |パスワード|ユーザーのパスワードを入力し、それを確認します。|
    |証明書ディレクトリ |これは、Docker 証明書を格納するフォルダーを指定します。新しいフォルダーを作成したり、既存のフォルダーをポイントしたりすることができますが、既定の証明書フォルダー (C:\\Users\\[*username*]\\.docker) を使用することをお勧めします。そうでないと、別のプロジェクトまたはシステムで同じホストを再利用する場合に、認証オプションを自動的に取得できません。|

1. **[証明書ディレクトリ]** エントリの横の省略記号 (...) ボタンを選択、し、Docker 証明書の新しいフォルダーを作成するか、既存の Docker 証明書フォルダーに移動します。

    VM に必要な Docker 証明書が見つからない場合、エントリの横に感嘆符のアイコンが表示され、必要な証明書が見つからず、続行すると、すべての既存の証明書が削除され、再作成されることが通知されます。

1. **[OK]** ボタンを選択して、VM の作成を開始します。Azure で、仮想マシンが作成されていることを示すメッセージが表示されます。

    Visual Studio によって、Azure リソース マネージャー (ARM) テンプレート ファイル、パラメーター ファイル、および PowerShell スクリプトが作成されるため、後でコマンドを再実行できます。

    ![][7]

    Visual Studio は操作の進捗状況を **[出力]** ウィンドウに出力します。Visual Studio は、VM をデプロイする PowerShell スクリプトを呼び出します。このスクリプトでは、Azure PowerShell コマンドレットを使用して、Azure リソース グループをデプロイします。後で、ホストを手動で作成した場合とまったく同じように、別の PowerShell スクリプトで、Docker コマンドを使用して発行します。

    Docker ホストのプロビジョニングには、しばらく時間がかかるため、ジョブが完了したら、[出力] ウィンドウでステータスを確認します。

1. Azure で Docker ホストを完全にプロビジョニングしたら、Azure ポータルで自分のアカウントを確認できます。Azure ポータルの **[仮想マシン]** カテゴリの下で新しい仮想マシンを確認できるはずです。

1. Docker ホストの準備ができたら、戻って、Web アプリケーション プロジェクトを発行します。**ソリューション エクスプローラー**の Web アプリケーション プロジェクトのノードのコンテキスト メニューで、**[発行]** を選択します。Visual Studio では、作成した VM に基づいて、発行ファイルが作成されます。

1. **[Web の発行]** ダイアログ ボックスの **[接続]** タブで、**[接続の検証]** ボックスを選択して、Docker ホストの準備ができていることを確認します。接続が正常な場合は、**[発行]** ボタンを選択して、Web アプリケーションを発行します。

    初めてアプリケーションを Docker に発行する場合は、Docker ファイルで参照されている基本イメージ (**FROM** *imagename* など) のダウンロードに時間がかかります。

    Docker ファイルは、オペレーティング システムに固有であることに注意してください。別の OS に再発行することを選択した場合は、Visual Studio が対象の OS に基づいて、新しい既定のファイルを作成できるように、Docker ファイルの名前を変更する必要があります。たとえば、最初に Linux コンテナーに発行し、後で Windows に発行することにした場合、Docker ファイルの名前を DockerLinux などの一意の名前に変更する必要があります。それにより、Windows に再発行すると、Visual Studio によって、Windows 用の既定の Docker ファイルが再作成されます。後で、いずれかのファイルを再発行するときに、その OS 用の適切な Docker ファイルを選択するだけです。

## カスタム Docker ホストの指定

前の手順で、Azure でホストされる Docker 仮想マシンを作成しました。ただし、既にどこかに Docker ホストが存在している場合は、Azure の代わりにそこに発行するように選択できます。

### カスタム Docker ホストを指定する方法

1. **[Docker Virtual Machine の選択]** ダイアログ ボックスで、 **[カスタム Docker ホスト]** チェック ボックスを選択します。

    ![][5]

1. **[OK]** ボタンを選択します。

1. **[Web の発行** ] ダイアログ ボックスで、**[CustomDockerHost]** セクションの設定に値 (サーバーの URL、イメージ名、Dockerfile の場所、およびホストとコンテナーのポート番号など) を追加します。

    **[Docker の詳細オプション]** セクションでは、認証および実行オプションと Docker コマンド ラインを表示したり、変更したりすることができます。

    ![][6]

1. 必要なすべての値を入力したら、**[接続の検証]** ボタンをクリックして、Docker ホストへの接続が正常に機能していることを確認します。

1. 接続が正常に機能している場合は、**[次へ]** ボタンを選択して、発行されるコンポーネントの一覧を表示するか、**[発行]** ボタンを選択してすぐにプロジェクトを発行できます。

## Docker ホストのテスト

これでプロジェクトは Azure 上の Docker ホストに発行されたので、その設定を確認してテストできます。Docker コマンド ライン ツールは Visual Studio 拡張機能と共にインストールされるため、Windows コマンド プロンプトから直接 Docker にコマンドを発行できます。

次の手順は、Azure にデプロイされた Docker ホストと通信するためのものです。

### Docker ホストをテストする方法

1. Windows コマンド プロンプトを開きます。

1. 環境変数に Docker ホストを割り当て、検証します。そのためには、コマンド プロンプトに次のコマンドを入力します(*NameofAzureVM* を Docker ホストの名前に置き換えます)。

    ```
    Set DOCKER_HOST=tcp://<NameofAzureVM>.cloudapp.net:2376
    Set DOCKER_TLS_VERIFY=1
    ```

    これらのコマンドを呼び出すことで、発行するコマンドごとに `–H (Host) tcp://<NameofAzureVM>.cloudapp.net:2376` と `--TLSVERIFY` を追加する必要がなくなります。

1. ここで、これらのようなコマンドを発行して、Docker ホストが存在し、機能しているかどうかをテストできます。

    |コマンド ライン|説明|
    |---|---|
    |docker info|Docker バージョン情報を取得します。|
    |docker ps|実行中のコンテナーの一覧を取得します。|
    |docker ps –a|停止しているコンテナーを含むコンテナーの一覧を取得します。|
    |docker logs <Docker container name>|指定されたコンテナーのログを取得します。|
    |docker images|イメージの一覧を取得します。|

    Docker コマンドの完全な一覧については、コマンド プロンプトで、単に `docker` コマンドを入力します。詳細については、「[Docker コマンド ライン](https://docs.docker.com/reference/commandline/cli/)」を参照してください。

## 次のステップ

これで Docker ホストが用意できたので、Docker コマンドをそれに対して発行できます。Docker の詳細については、[Docker のドキュメント](https://docs.docker.com/)と [Docker のオンライン チュートリアル](https://www.docker.com/tryit/)を参照してください。

Visual Studio での Docker の使用に関する問題については、[「Docker エラーのトラブルシューティング」](vs-docker-troubleshooting-docker-errors.md)を参照してください。

[0]: ./media/vs-azure-tools-docker-hosting-web-apps-in-docker/IC796678.png
[1]: ./media/vs-azure-tools-docker-hosting-web-apps-in-docker/IC796679.png
[2]: ./media/vs-azure-tools-docker-hosting-web-apps-in-docker/IC796680.png
[3]: ./media/vs-azure-tools-docker-hosting-web-apps-in-docker/IC796681.png
[4]: ./media/vs-azure-tools-docker-hosting-web-apps-in-docker/IC796682.png
[5]: ./media/vs-azure-tools-docker-hosting-web-apps-in-docker/IC796683.png
[6]: ./media/vs-azure-tools-docker-hosting-web-apps-in-docker/IC796684.png
[7]: ./media/vs-azure-tools-docker-hosting-web-apps-in-docker/IC796685.png
[8]: ./media/vs-azure-tools-docker-hosting-web-apps-in-docker/IC796686.png

<!---HONumber=August15_HO8-->