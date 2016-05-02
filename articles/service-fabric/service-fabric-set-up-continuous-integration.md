<properties
   pageTitle="Service Fabric の継続的インテグレーション | Microsoft Azure"
   description="Visual Studio Team Services (VSTS) を使用して Service Fabric アプリケーションの継続的インテグレーションをセットアップする方法の概要について説明します。"
   services="service-fabric"
   documentationCenter="na"
   authors="cawams"
   manager="timlt"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="03/29/2016"
   ms.author="cawa" />

# Visual Studio Team Services を使用して Service Fabric アプリケーションの継続的インテグレーションをセットアップする

この記事では、アプリケーションが自動的にビルド、パッケージ化、およびデプロイされるように、Visual Studio Team Services (VSTS) を使用して、Azure Service Fabric アプリケーションの継続的インテグレーションをセットアップする手順について説明します。説明する手順では、毎回、クラスターを最初から作成し直すことに注意してください。

このドキュメントは現在の手順を反映したものであり、今後変更される予定です。

## 前提条件

作業を開始するために、Visual Studio Team Services でプロジェクトをセットアップします。

1. Team Services アカウントをまだ作成していない場合は、[Microsoft アカウント](http://www.microsoft.com/account)を使用して設定します。

2. Microsoft アカウントを使用して Team Services 上で新しいプロジェクトを作成します。

3. 新規または既存の Service Fabric アプリのソースをこのプロジェクトにプッシュします。

Team Services プロジェクトの操作の詳細については、「[Visual Studio への接続](https://www.visualstudio.com/get-started/setup/connect-to-visual-studio-online)」を参照してください。

## サービス プリンシパルをセットアップする

### オートメーションのための認証をセットアップする

ビルド コンピューターをセットアップするには、ビルド エージェントが Azure への認証で使用する[サービス プリンシパル](../resource-group-create-service-principal-portal.md)を事前に作成しておく必要があります。Azure Key Vault ではサービス プリンシパル認証がサポートされていないので、証明書を作成し、それを Key Vault にアップロードすることも必要です。これらの手順はどのコンピューターからでも実行できます。開発用のコンピューターで行うことをお勧めします。

### Azure PowerShell をインストールしてサインインする

1.  PowerShellGet をインストールします。

    a.最新の更新プログラムが適用された Windows 10 を実行している場合は、この手順を省略できます (PowerShellGet はすでにインストールされています)。

    b.それ以外の場合には、PowerShellGet が付属している [Windows Management Framework 5.0](http://www.microsoft.com/download/details.aspx?id=48729) をインストールします。

2.	AzureRM モジュールをインストールし更新します。Azure PowerShell の以前のバージョンがインストールされている場合は、それを削除します。

    a.[スタート] ボタンを右クリックし、**[プログラムの追加と削除]** を選択します。

    b."Azure PowerShell" を見つけて、それをアンインストールします。

    c.PowerShell コマンド プロンプトを開きます。

    d.`Install-Module AzureRM` コマンドを使用して AzureRM モジュールをインストールします。

    e.`Update-AzureRM` コマンドを使用して AzureRM モジュールを更新します。

3.	Azure データ コレクションを無効 (または有効) にします。

    Azure コマンドレットは、ユーザーが選択を行うまで、データ コレクションを有効または無効にするよう求めるメッセージをユーザーに表示します。これらのメッセージは、ユーザー入力を待機している間、オートメーションをブロックします。これらのメッセージが表示されないようにするには、次のいずれかのコマンドを実行して事前に選択しておきます。

    - Enable-AzureRmDataCollection

    - Disable-AzureRmDataCollection

4.	Azure PowerShell にサインインします。

    a.コマンド `Login-AzureRmAccount` を実行します。

    b.表示されるダイアログ ボックスで、Azure の資格情報を入力します。

    c.コマンド `Get-AzureRmSubscription` を実行します。

    d.使用するサブスクリプションを検索します。

    e.コマンド `Select-AzureRmSubscription -SubscriptionId <ID for your subscription>` を実行します。

### サービス プリンシパルの作成

1. 以下の[手順](https://blogs.msdn.microsoft.com/visualstudioalm/2015/10/04/automating-azure-resource-group-deployment-using-a-service-principal-in-visual-studio-online-buildrelease-management/)に従って、プロジェクトのサービス プリンシパルとサービス エンドポイントを作成します。

2. スクリプトの出力の最後に出力される値にご注意ください。それらの値はビルド定義をセットアップするために必要です。

### 証明書を作成し、新しい Azure Key Vault にアップロードする

>[AZURE.NOTE] このサンプル スクリプトは、自己署名証明書を生成します。これは安全なプラクティスではなく、実験の場合にだけ許容できます。代わりに、組織のガイドラインに従って、正規の証明書を取得してください。また、以下の手順では、サーバーとクライアントの両方に 1 つの証明書が使用されます。運用環境では、サーバーとクライアントに別々の証明書を使用する必要があります。

1. [ServiceFabricContinuousIntegrationScripts.zip](https://gallery.technet.microsoft.com/Set-up-continuous-f8b251f6) をダウンロードして、このコンピューター上のフォルダーに展開します。

2. 管理者 PowerShell プロンプトで、ディレクトリ `<extracted zip>/Manual` に移動します。

3. 次のパラメーターを指定して PowerShell スクリプト `CreateAndUpload-Certificate.ps1` を実行します。

| パラメーター | 値 |
| --- | --- |
| KeyVaultLocation | 任意の値。このパラメーターは、クラスターを作成する場所と一致する必要があります。 |
| CertificateSecretName | 任意の値。 |
| CertificateDnsName | クラスターの DNS 名に一致する必要があります。例: `mycluster.westus.azure.cloudapp.net` |
| SecureCertificatePassword | 任意の値。このパラメーターは、ビルド コンピューターに証明書をインポートするときに使用されます。 |
| KeyVaultResourceGroupName | 任意の値。ただし、クラスターに使用するリソース グループ名は使用しないでください。 |
| KeyVaultName | 任意の値。 |
| PfxFileOutputPath| 任意の値。このファイルを使用して、ビルド コンピューターに証明書をインポートします。 |

スクリプトが完了すると、次の 3 つの値が出力されます。これらの値はビルド変数として使用するので、メモしておいてください。

 - `ServiceFabricCertificateThumbprint`
 - `ServiceFabricKeyVaultId`
 - `ServiceFabricCertificateSecretId`

## ビルド コンピューターをセットアップする

### Visual Studio 2015 のインストール

コンピューターのプロビジョニングが完了している場合 (または、独自に行う場合)、選択したコンピューターに [Visual Studio 2015](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx) をインストールします。

まだコンピューターが準備できていない場合は、プレインストールされた Visual Studio 2015 を使用して Azure Virtual Machine (VM) をすばやくプロビジョニングすることができます。これを行うには、次の手順を実行します。

1. [Azure ポータル](https://portal.azure.com)にサインインします。

2. 画面の左上隅にある **[新規]** コマンドを選択します。

3. **[Marketplace]** を選択します。

4. **Visual Studio 2015** を検索します。

5. **[Compute]**、**[仮想マシン]**、**[From Gallery (ギャラリーから)]** の順に選択します。

6. イメージ **[Visual Studio Enterprise 2015 Update 1 With Azure SDK 2.8 on Windows Server 2012 R2]** を選択します。

    >[AZURE.NOTE] Azure SDK は必須のコンポーネントではありませんが、Visual Studio 2015 のみがインストールされているイメージは、現在は用意されていません。

7.	ダイアログ ボックスの指示に従って VM を作成します

### Service Fabric SDK をインストールします。

コンピューターに [Service Fabric SDK](https://azure.microsoft.com/campaigns/service-fabric/) をインストールします。

### Azure PowerShell をインストールするには

Azure PowerShell をインストールするには、前のセクション「Azure PowerShell をインストールしてサインインする」の手順に従ってください。「Azure PowerShell にサインインします」の手順は省略します。

### ネットワーク サービス アカウントに Azure PowerShell モジュールを登録します。

>[AZURE.NOTE] ビルド エージェントを開始する*前*にこれを行います。そうしないと、新しい環境変数が反映されません。

1. Windows ロゴ キーと R キーを押し、「**regedit**」と入力して Enter キーを押します。

2. ノード `HKEY_Users\.Default\Environment` を右クリックし、**[新規]**、**[展開可能な文字列値]** の順に選択します。

3. 名前として「`PSModulePath`」と入力し、値として「`%PROGRAMFILES%\WindowsPowerShell\Modules`」と入力します。`%PROGRAMFILES%` を `PROGRAMFILES` 環境変数の値に置き換えます。

### Automation の証明書をインポートする

1.	ビルド コンピューターに証明書をインポートします。これを行うには、次の手順を実行します。

    a.スクリプト CreateAndUpload Certificate.ps1 によって作成された PFX ファイルをビルド コンピューターにコピーします。

    b.先ほど `CreateAndUpload-Certificate.ps1` に渡したパスワードを使用して、管理者 PowerShell プロンプトを開き、次のコマンドを実行します。

        ```
        $password = Read-Host -AsSecureString
        Import-PfxCertificate -FilePath <path/to/cert.pfx> -CertStoreLocation Cert:\LocalMachine\My -Password $password -Exportable
        ```

2.	証明書マネージャーを実行します。

    a.Windows でコントロール パネルを開きます。[スタート] ボタンを右クリックし、**[コントロール パネル]** を選択します。

    b.**証明書**を検索します。

    c.**[管理ツール]**、**[コンピューター証明書の管理]** の順に選択します。

3.	オートメーション証明書を使用するアクセス許可をネットワーク サービス アカウントに付与します。

    a.**[証明書 - ローカル コンピューター]** で、**[個人]** を展開し、**[証明書]** を選択します。

    b.一覧の中から目的の証明書を探します。

    c.証明書を右クリックして、**[すべてのタスク]**、**[秘密キーの管理]** の順に選択します。

    d.**[追加]** ボタンを選択し、「**ネットワーク サービス**」と入力し、**[名前の確認]** を選択します。

    e.**[OK]** を選択し、証明書マネージャーを閉じます。

    ![ローカル サービス アカウントのアクセス許可を付与するための手順のスクリーンショット](media/service-fabric-set-up-continuous-integration/windows-certificate-manager.png)

4.  証明書を `Trusted People` フォルダーにコピーします。

    a.証明書は**個人証明書**にインポートされていますが、 **信頼されたユーザー**にその証明書を追加する必要があります。証明書を右クリックして、**[コピー]** を選択します。**[信頼されたユーザー]** フォルダーを右クリックして、**[貼り付け]** を選択します。

### ビルド エージェントを登録する

1.	agent.zip をダウンロードします。これを行うには、次の手順を実行します。

    a.****https://[your-VSTS-account-name].visualstudio.com** などチーム プロジェクトにサインインします。

    b.画面の右上隅にある歯車アイコンを選択します。

    c.**[エージェント プール]** タブを選択します。

    d.**[エージェントのダウンロード ]** を選択して、agent.zip ファイルをダウンロードします。

    >[AZURE.NOTE] ダウンロードが開始しない場合は、ポップアップ ブロックを確認します。

    e.先ほど作成したビルド コンピューターに agent.zip をコピーします。

    f.agent.zip をビルド コンピューターの `C:\agent` (または短いパスの任意の場所) に解凍します。

    >[AZURE.NOTE] ASP.NET 5 Web サービスを使用する場合は、デプロイ中に **PathTooLongExceptions** エラーが発生するのを防ぐために、このフォルダーに対しては可能な限り短い名前を選択することをお勧めします。ASP.NET Core がリリースされたときに、この問題が軽減されます。

2.	管理者のコマンド プロンプトで、`C:\agent\ConfigureAgent.cmd` を実行します。スクリプトは次のパラメーターに対して入力を求めるメッセージを表示します。

|パラメーター|値|
|---|---|
|エージェント名|既定値 `Agent-[machine name]` をそのまま使用します。|
|TFS URL|チーム プロジェクトに `https://[your-VSTS-account-name].visualstudio.com` のような URL を入力します。|
|エージェント プール|エージェント プールの名前を入力します(エージェント プールを作成していない場合は、既定値を使用します)。|
|作業フォルダー|既定値をそのまま使用します。これは、ビルド エージェントによってアプリケーションが実際にビルドされるフォルダーです。ASP.NET 5 Web サービスを使用する場合は、デプロイ中に PathTooLongExceptions エラーが発生するのを防ぐために、フォルダーに対しては可能な限り短い名前を選択することをお勧めします。|
|Windows サービスとしてインストールしますか?|既定値は N です。値を **Y** に変更します。|
|サービスを実行するユーザー アカウント|既定値 `NT AUTHORITY\NetworkService` をそのまま使用します。|
|`NT AUTHORITY\Network Service` のパスワード|ネットワーク サービス アカウントのパスワードはありませんが、空のパスワードは拒否されます。空でないパスワード文字列を入力してください (入力内容は無視されます)。|
|既存のエージェントの構成を解除しますか?|既定値 **N** をそのまま使用します。|

3.  資格情報の指定を求められたら、チーム プロジェクトに対する権限を持つ Microsoft アカウントの資格情報を入力します。

4.  ビルド エージェントが登録されていることを確認し、その機能を構成します。これを行うには、次の手順を実行します。

    a.Web ブラウザー (`https://[your-VSTS-account-name].visualstudio.com/_admin/_AgentPool`) に戻り、ページを更新します。

    b.前に ConfigureAgent.ps1 を実行したときに選択したエージェント プールを選びます。

    c.ビルド エージェントが一覧の中に表示され、緑色で強調表示されていることを確認します。赤色で強調表示されている場合は、ビルド エージェントで Team Services への接続に関する問題が発生しています。

    ![ビルド エージェントの状態を示すスクリーンショット](media/service-fabric-set-up-continuous-integration/vso-configured-agent.png)

    d.ビルド エージェントを選択し、**[機能]** タブを選択します。

    e. **azureps** という名前の機能と任意の値を追加します。これはこのコンピューターに Azure PowerShell がインストールされていることを VSTS に示します。これは VSTS 提供ビルド タスクの一部を使用するために必要です。


## ビルド定義を作成する

>[AZURE.NOTE] 次の手順で作成するビルド定義は、別々のコンピューター上であっても、複数の同時実行ビルドをサポートしません。これは、各ビルドが同じリソース グループまたはクラスターに対して実行されるためです。複数のビルド エージェントを実行する場合は、次の手順/スクリプトを変更して前述制限を解消する必要があります。

### Service Fabric Azure Resource Manager テンプレートをアプリケーションに追加する

1. [こちらのサンプル](https://github.com/Azure/azure-quickstart-templates/tree/master/service-fabric-secure-cluster-5-node-1-nodetype-wad)から `azuredeploy.json` と `azuredeploy.parameters.json` をダウンロードします。

2. `azuredeploy.parameters.json` を開いて次のパラメーターを編集します。

    |パラメーター|値|
    |---|---|
    |clusterLocation|Key Vault の場所と一致する必要があります。例: `westus`|
    |clusterName|証明書の DNS 名に合わせる必要があります。たとえば証明書の DNS 名が `mycluster.westus.cloudapp.net` である場合、`clusterName` は `mycluster` にする必要があります。|
    |adminPassword|8 個から 123 個までの文字で、そのうちの少なくとも 3 個が大文字、小文字、数字、特殊文字です。|
    |certificateThumbprint|`CreateAndUpload-Certificate.ps1` の出力から|
    |sourceVaultValue|`CreateAndUpload-Certificate.ps1` の出力から|
    |certificateUrlvalue|`CreateAndUpload-Certificate.ps1` の出力から|

3. ソース管理に新しいファイルを追加し、VSTS にプッシュします。

### ビルド定義を作成する

1.	空のビルド定義を作成します。これを行うには、次の手順を実行します。

    a.Visual Studio Team Services でプロジェクトを開きます。

    b.**[ビルド]** タブを選択します。

    c.緑色の **[+]** 記号を選択して、新しいビルド定義を作成します。

    d.**[空]** を選択し、**[次へ]** を選択します。

    e.適切なリポジトリとブランチが選択されていることを確認します。

    f.ビルド エージェントを登録したエージェント キューを選択し、**[継続的インテグレーション]** チェック ボックスをオンにします。

2.	**[変数]** タブで、次の値を持つ変数を作成します。

    |変数|値|シークレット|キュー時の使用|
    |---|---|---|---|
    |BuildConfiguration|リリース||○|
    |BuildPlatform|x64||||

3.  ビルド定義を保存して、名前を付けます後で必要に応じてこの名前を変更できます。

### "Restore NuGet packages" ステップを追加します。

1. **[ビルド]** タブで、**[ビルド ステップの追加]** コマンドを選択します。

2. **[パッケージ]**、**[NuGet インストーラー]** の順に選択します。

3. ビルド ステップの名前のそばにある鉛筆アイコンを選択し、その名前を「**NuGet パッケージの復元**」に変更します。

4. **[ソリューション]** フィールドの横にある **[...]** ボタンを選択し、.sln ファイルを選択します。

5. ビルド定義を保存します。

### "ビルド" ステップを追加する

1.	**[ビルド]** タブで、**[ビルド ステップの追加...]** コマンドを選択します。

2.	**[ビルド]**、**[MSBuild]** の順に選択します。

3.	ビルド ステップの名前のそばにある鉛筆アイコンを選択し、その名前を「**ビルド**」に変更します。

4. これらの値を選択します。

    |設定の名前|値|
    |---|---|
    |解決策|**[…]** ボタンをクリックし、目的のソリューションの `.sln` ファイルを選択します。|
    |プラットフォーム|`$(BuildPlatform)`|
    |構成|`$(BuildConfiguration)`|

5.	ビルド定義を保存します。

### "パッケージ" ステップを追加する

1.	**[ビルド]** タブで、**[ビルド ステップの追加...]** コマンドを選択します。

2.	**[ビルド]**、**[MSBuild]** の順に選択します。

3.	ビルド ステップの名前の横にある鉛筆アイコンを選択し、その名前を「**パッケージ**」に変更します。

4. これらの値を選択します。

    |設定の名前|値|
    |---|---|
    |解決策|**[…]** ボタンをクリックし、アプリケーション プロジェクトの `.sfproj` ファイルを選択します。|
    |プラットフォーム|`$(BuildPlatform)`|
    |構成|`$(BuildConfiguration)`|
    |MSBuild 引数|`/t:Package`|

5.	ビルド定義を保存します。

### "クラスター リソース グループの削除" ステップを追加する

クリーンアップの後で以前のビルドがクリーンアップされていない場合 (クリーンアップされる前にビルドが取り消された場合など)、既存のリソース グループが新しいものと競合する可能性があります。競合を避けるためには、残されているリソース グループ (および関連付けられているリソース) をいずれもクリーンアップしてから新しいリソース グループを作成します。

1.	**[ビルド]** タブで、**[ビルド ステップの追加...]** コマンドを選択します。

2.	**[配置]**、**[Azure リソース グループの配置]** の順に選択します。

3.	ビルド ステップの名前の横にある鉛筆アイコンを選択し、その名前を「**クラスター リソース グループの削除**」に変更します。

4. これらの値を選択します。

    |設定の名前|値|
    |---|---|
    |AzureConnectionType|**Azure リソース マネージャー**|
    |Azure RM サブスクリプション|「**サービス プリンシパルの作成**」セクションで作成した接続エンドポイントを選択します。|
    |アクション|**リソース グループの削除**|
    |リソース グループ|任意の未使用の名前を入力します。次の手順で、同じ名前を使用する必要があります。|

5.	ビルド定義を保存します。

### "セキュリティで保護されたクラスターをプロビジョニングする" ステップの追加

1.	**[ビルド]** タブで、**[ビルド ステップの追加...]** コマンドを選択します。

2.	**[配置]**、**[Azure リソース グループの配置]** の順に選択します。

3.	ビルド ステップの名前の横にある鉛筆アイコンを選択し、その名前を「**セキュリティで保護されたクラスターをプロビジョニングする**」に変更します。

4. これらの値を選択します。

    |設定の名前|値|
    |---|---|
    |AzureConnectionType|**Azure リソース マネージャー**|
    |Azure RM サブスクリプション|「**サービス プリンシパルの作成**」セクションで作成した接続エンドポイントを選択します。|
    |アクション|**リソース グループの作成または更新**|
    |リソース グループ|前のステップで使用した名前に合わせてください。|
    |Location (場所)|Key Vault の場所と一致する必要があります。|
    |テンプレート|**[…]** ボタンをクリックし、`azuredeploy.json` を選択します。|
    |テンプレート パラメーター|**[…]** ボタンをクリックし、`azuredeploy.parameters.json` を選択します。|

5.	ビルド定義を保存します。

### "デプロイ" ステップの追加

1.	**[ビルド]** タブで、**[ビルド ステップの追加...]** コマンドを選択します。

2.	**[ユーティリティ]**、**[PowerShell]** の順に選択します。

3.	ビルド ステップの名前の横にある鉛筆アイコンを選択し、その名前を「**デプロイ**」に変更します。

4. これらの値を選択します。

    |設定の名前|値|
    |---|---|
    |型|**ファイル パス**|
    |スクリプトのファイル名|**[…]** ボタンをクリックし、アプリケーション プロジェクト内の **[スクリプト]** ディレクトリに移動します。`Deploy-FabricApplication.ps1` を選択します。|
    |引数|`-PublishProfileFile path/to/MySolution/MyApplicationProject/PublishProfiles/MyPublishProfile.xml -ApplicationPackagePath path/to/MySolution/MyApplicationProject/pkg/$(BuildConfiguration)`|

5.	ビルド定義を保存します。

### 試してみる

**[キュー ビルド]** を選択してビルドを開始します。ビルドは、プッシュ時またはチェックイン時にもトリガーされます。

## 代替ソリューション

上記手順は、ビルドごとに新しいクラスターを作成し、ビルドの最後にそれを削除するというものです。各ビルドでアプリケーションのアップグレード (既存のクラスターに対して) を行う場合は、次の手順を実行します。

1.	Azure ポータルまたは Azure PowerShell を使用して手動でテスト クラスターを作成します。[こちらの手順](service-fabric-cluster-creation-via-portal.md)に従ってください。

2.	アプリケーションのアップグレードをサポートするように発行プロファイルを構成します。[こちらの手順](service-fabric-visualstudio-configure-upgrade.md)を参照してください。

4.	ビルド定義から**クラスター リソース グループの削除** ビルド ステップと**クラスターのプロビジョニング** ビルド ステップを削除します。

## 次のステップ

Service Fabric アプリケーションの継続的インテグレーションに関する詳細については、次の記事を参照してください。

 - [ビルドに関するドキュメントのホームページ](https://msdn.microsoft.com/Library/vs/alm/Build/overview)
 - [ビルド エージェントのデプロイ](https://msdn.microsoft.com/Library/vs/alm/Build/agents/windows)
 - [ビルド定義の作成と構成](https://msdn.microsoft.com/Library/vs/alm/Build/vs/define-build)

<!---HONumber=AcomDC_0420_2016-->