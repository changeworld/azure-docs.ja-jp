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
   ms.date="10/16/2015"
   ms.author="cawa" />

# Visual Studio Team Services (VSTS) を使用して Service Fabric アプリケーションの継続的インテグレーションをセットアップする

この記事では、アプリケーションを自動的にビルド、パッケージ化、およびデプロイできるように、Visual Studio Team Services (VSTS) を使用して、Service Fabric アプリケーションの継続的インテグレーション (CI) をセットアップする方法について説明します。このドキュメントは、現在のエクスペリエンスを示しており、開発の進行状況に応じて変更されることが予想されるので注意してください。また、説明する手順では、毎回、クラスターを最初から作成し直します。

## 前提条件

作業を開始するために、Visual Studio Team Services でプロジェクトをセットアップします。

1. まだセットアップしていない場合は、[Microsoft アカウント](http://www.microsoft.com/account)を使用して Team Services アカウントを作成します。
2. Microsoft アカウントを使用して Team Services 上で新しいプロジェクトを作成します。
3. 新規または既存の Service Fabric アプリのソースをこのプロジェクトにプッシュします。

Team Services プロジェクトの操作の詳細については、「[Visual Studio への接続](https://www.visualstudio.com/get-started/setup/connect-to-visual-studio-online)」を参照してください。

## セットアップの手順

### オートメーションのための認証をセットアップする

ビルド コンピューターをセットアップするには、オートメーションが Azure への認証で使用する "サービス プリンシパル" を事前に作成しておく必要があります。Azure Key Vault ではサービス プリンシパル認証がサポートされていないので、証明書を作成し、それを Key Vault にアップロードすることも必要です。これらの手順はどのコンピューターからでも実行できます。開発用のコンピューターで行うことをお勧めします。

### Azure PowerShell をインストールしてサイン インする

1.	Azure PowerShell をインストールします。
    - PowerShellGet をインストールします。このためには、PowerShellGet が付属している [Windows Management Framework 5.0](http://www.microsoft.com/download/details.aspx?id=48729) をインストールします。

    >[AZURE.NOTE]最新の更新プログラムを適用した Windows 10 を実行している場合は、この手順を省略できます。

1.	AzureRM モジュールをインストールし更新します。
    1.  Azure PowerShell の以前のバージョンがインストールされている場合は、それを削除します。[スタート] ボタンを右クリックし、[プログラムの追加と削除] を選択します。"Azure PowerShell" を見つけて、それをアンインストールします。
    1.  Azure PowerShell コマンド プロンプトを起動します。
    1.	`Install-Module AzureRM` コマンドを使用して "AzureRM" モジュールをインストールします。
    1.	`Update-AzureRM` コマンドを使用して AzureRM モジュールを更新します。
1.	Azure データ コレクションを無効 (または有効) にします。

    Azure コマンドレットは、ユーザーが選択を行うまで、データ コレクションを有効または無効にするよう求めるメッセージをユーザーに表示します。これらのメッセージは、ユーザー入力を待機している間、オートメーションをブロックします。前もって選択しておくことでこれらのメッセージが表示されるのを抑制するために、次のコマンドのいずれかを実行します。

    - Enable-AzureRmDataCollection
    - Disable-AzureRmDataCollection

1.	Azure PowerShell にサインインします。
    1. 管理者の PowerShell プロンプトを起動し、コマンド `Login-AzureRMAccount` を実行します。
    1. 表示されるダイアログ ボックスで、Azure の資格情報を入力します。
    1. コマンド `Get-AzureRmSubscription` を実行します。
    1. 使用するサブスクリプションを検索し、コマンド `Select-AzureRmSubscription -SubscriptionId <id for your subscription>` を実行します。

### サービス プリンシパルの作成

1.	[ServiceFabricCIManualScripts.zip](http://go.microsoft.com/fwlink/?LinkId=703773) を、このコンピューター上のフォルダーに展開します。
1.	管理者 PowerShell コマンド プロンプトで ServiceFabricCIManualScripts.zip を展開するディレクトリに移動します。
1.	次のコマンドを使用してサービス プリンシパルのパスワードを選択します。このパスワードはビルド変数として使用するため、覚えておいてください。

    ```
    $password = Read-Host -AsSecureString
    ```
1.	次のパラメーターを使用して PowerShell スクリプト Create-ServicePrincipal.ps1 を実行します。

    |パラメーター|値|
    |---|---|
    |ServicePrincipalDisplayName|任意の名前。|
    |ServicePrincipalHomePage|任意の URI。実際に存在する必要はありません。|
    |ServicePrincipalIdentifierUri|任意の一意の URI。実際に存在する必要はありません。|
    |ServicePrincipalSecurePassword|$password|

    スクリプトが完了すると、次の 3 つの値が出力されます。これらの値はビルド変数として使用するので、メモしておいてください。

    - ServicePrincipalId
    - ServicePrincipalTenantId
    - ServicePrincipalSubscriptionId

### 証明書を作成し、新しい Azure Key Vault にアップロードする

1.	管理者 PowerShell プロンプトで ServiceFabricCIManualScripts.zip を展開するディレクトリに移動します。
1.	次のパラメーターを使用して PowerShell スクリプト CreateAndUpload-Certificate.ps1 を実行します。

    |パラメーター|値|
    |---|---|
    |ServiceFabricKeyVaultLocation|任意の値。クラスターを作成する場所と一致する必要があります。|
    |ServiceFabricCertificateSecretName|任意の値。|
    |ServiceFabricSecureCertificatePassword|任意の値。ビルド コンピューターに証明書をインポートするときに使用されます。|
    |ServiceFabricKeyVaultResourceGroupName|任意の値。ただし、クラスターに使用するリソース グループ名は使用しないでください。|
    |ServiceFabricKeyVaultName|任意の値。|
    |ServiceFabricPfxFileOutputPath|任意の値。このファイルを使用して、ビルド コンピューターに証明書をインポートします。|

    スクリプトが完了すると、次の 3 つの値が出力されます。これらの値はビルド変数として使用するので、メモしておいてください。

    - ServiceFabricCertificateThumbprint
    - ServiceFabricKeyVaultId
    - ServiceFabricCertificateSecretId


## ビルド コンピューターをセットアップする

次の手順で作成するビルド定義は、並列ビルドをサポートしていないことに注意してください (別々のコンピューター上でも)。これは、各ビルドが同じリソース グループ/クラスターに対して実行されるためです。 複数のビルド エージェントを実行する場合は、次の手順/スクリプトを変更して前述制限を解消する必要があります。

### Visual Studio 2015 のインストール

1.	コンピューターのプロビジョニングが完了している場合 (または、独自に行う場合)、そのコンピューターに [Visual Studio 2015](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx) をインストールします。
2.	まだコンピューターが準備できていない場合は、プレインストールされた Visual Studio 2015 を使用して Azure 仮想マシン (VM) をすばやくプロビジョニングすることができます。これを行うには、次の手順を実行します。
    1.	[Microsoft Azure 管理ポータル](http://portal.azure.com)にログインします。
    1.	画面の左上隅にある **[新規]** コマンドを選択します。
    1.	**[Marketplace]** を選択します。
    1.	**Visual Studio 2015** を検索します。
    1.	**[Compute]**、**[仮想マシン]**、**[ギャラリーから]** の順に選択します。
    1.	イメージ **[Windows Server 2012 R2 での Visual Studio Enterprise 2015 と Azure SDK 2.7 ]** を選択します。

        >[AZURE.NOTE]Azure SDK は必須のコンポーネントではありませんが、Visual Studio 2015 のみがインストールされているイメージは用意されていません。

    1.	ダイアログ ボックスの指示に従って VM を作成します (ディスクおよび CPU が最高のパフォーマンスを発揮するように D シリーズの VM を選択することをお勧めします)。

### Service Fabric SDK をインストールする

[Service Fabric SDK](https://azure.microsoft.com/campaigns/service-fabric/) をインストールします。

### Service Fabric SDK の NuGet リポジトリを登録する

1.	ビルド コンピューター上にディレクトリ `%SYSTEMDRIVE%\Windows\ServiceProfiles\LocalService\AppData\Roaming\NuGet` を作成します (まだ存在しない場合)。
2.	NuGet.config という名前のファイルがこのディレクトリに既に存在している場合は、そのディレクトリを開き、次の要素を `<packageSources>` の子として追加します。

    ```
    <add key="Service Fabric SDK" value="<path to service fabric SDK>\packages" />`
    ```

3.	NuGet.config がまだ存在していない場合は、次の内容を使用して作成します。`<path to service fabric SDK>` を、ビルド コンピューター上の Service Fabric SDK へのパスに置き換えます。
>[AZURE.NOTE]既定では、`<path to service fabric SDK>` は `%ProgramFiles%\Microsoft SDKs\Service Fabric` です。

    NuGet.config:

    ```
    <?xml version="1.0" encoding="utf-8"?>
    <configuration>
      <packageSources>
        <!-- Add this repository to the list of available repositories -->
        <add key="Service Fabric SDK" value="<path to service fabric SDK>\packages" />
      </packageSources>
    </configuration>
    ```

### Azure PowerShell をインストールするには

Azure PowerShell をインストールするには、前のセクション「**Azure PowerShell にインストールしてサインインする**」の手順に従ってください。サブセクション「**Azure PowerShell へのログイン**」は省略します。

### ローカル サービス アカウントに Azure PowerShell モジュールを登録します。

1. Win + R キーを押して、「**regedit**」と入力し、Enter キーを押します。
2. ノード `HKEY_Users\.Default\Environment` で右クリックし、**[新規]、[展開可能な文字列値]** の順に選択します。
3. 名前として `PSModulePath` を入力し、値として `%PROGRAMFILES%\WindowsPowerShell\Modules` を入力します。

>[AZURE.NOTE]この操作は、ビルド エージェントを起動する*前に*行います。そうしないと、新しい環境変数が取得されません。

### Automation の証明書をインポートする

1.	ビルド コンピューターに証明書をインポートします。これを行うには、次の手順を実行します。
    1. スクリプト CreateAndUpload Certificate.ps1 によって作成された PFX ファイルをビルド コンピューターにコピーします。
    1. 先ほど GenerateCertificate.ps1 に渡したパスワードを使用して、管理者 PowerShell アカウントを開き、次のコマンドを発行します。

        ```
        $password = Read-Host -AsSecureString
        Import-PfxCertificate -FilePath <path/to/cert.pfx> -CertStoreLocation Cert:\LocalMachine\My -Password $password -Exportable
        ```  

1.	証明書マネージャーを実行します。
    1. Windows のコントロール パネルを開きます。[スタート] ボタンを右クリックし、**[コントロール パネル]** を選択します。
    1. **証明書**を検索します。
    1. **[管理ツール]**、**[コンピューター証明書の管理]** の順に選択します。


1.	オートメーション証明書を使用するアクセス許可をローカル サービス アカウントに付与します。
    1.	**[証明書 - ローカル コンピューター]** で、**[個人]** を展開し、**[証明書]** を選択します。
    1.	一覧の中から目的の証明書を探します。
    1.	証明書を右クリックして、**[すべてのタスク]**、**[秘密キーの管理]** の順に選択します。
    1.	**[追加]** ボタンを選択し、「**ローカル サービス**」と入力し、**[名前の確認]** を選択します。
    1.	**[OK]** ボタンをクリックし、証明書マネージャーを閉じます。

![](media/service-fabric-set-up-continuous-integration/windows-certificate-manager.png)

### ビルド エージェントを登録する

1.	agent.zip をダウンロードします。これを行うには、次の手順を実行します。
    1.	* ***https://[your-VSTS-account-name].visualstudio.com** などのチーム プロジェクトにログオンします。
1.	画面の右上隅にある '歯車' アイコンを選択します。
    1.	コントロール パネルで、**[エージェント プール]** タブを選択します。
    1.	**[ダウンロード エージェント]** を選択し、agent.zip ファイルをダウンロードします。
    1.	先ほど作成したビルド コンピューターに agent.zip をコピーします。
    1.	agent.zip をビルド コンピューター`C:\agent` (または短いパスの任意の場所) に解凍します。

        >[AZURE.NOTE]ASP.NET 5 Web Services の構築を計画している場合は、デプロイ中に **PathTooLongExceptions** エラーが発生するのを防ぐために、フォルダーに対しては可能な限り短い名前を選択することをお勧めします。

1.	管理者 PowerShell コマンド プロンプトで、`C:\agent\ConfigureAgent.ps1` を実行します。スクリプトは次のパラメーターに対して入力を求めるメッセージを表示します。

    |パラメーター|値|
    |---|---|
    |エージェント名|既定値 `Agent-[machine name]` を受け入れます。
    |TFS URL|チーム プロジェクトに `https://[your-VSTS-account-name].visualstudio.com` のような URL を入力します。
    |エージェント プール|エージェント プールの名前を入力します(エージェント プールを作成していない場合は、既定値を使用します)。|
    |作業フォルダー|既定値をそのまま使用します。これは、ビルド エージェントによってアプリケーションが実際にビルドされるフォルダーです。注: ASP.NET 5 Web Services の構築を計画している場合は、デプロイ中に PathTooLongExceptions エラーが発生するのを防ぐために、フォルダーに対しては可能な限り短い名前を選択することをお勧めします。|
    |Windows サービスとしてインストールしますか?|既定値は N です。値を Y に変更します。|
    |サービスを実行するユーザー アカウント|既定値 `NT AUTHORITY\LocalService)` をそのまま使用します。|
    |既存のエージェントの構成を解除しますか?|既定値 **N** をそのまま使用します。|

1. 資格情報を入力するように求められます。チーム プロジェクトに対する権限を持つ Microsoft アカウントの資格情報を入力します。
1. ビルド エージェントが登録されていることを確認します。これを行うには、次の手順を実行します。

    1. Web ブラウザー (ページ `https://[your-VSTS-account-name].visualstudio.com/_admin/_AgentPool`) に戻り、ページを更新します。
    1. 前に ConfigureAgent.ps1 を実行したときに選択したエージェント プールを選びます。
    1. ビルド エージェントが一覧の中に表示され、緑色で強調表示されていることを確認します。赤色で強調表示されている場合は、ビルド エージェントで Team Services への接続に関する問題が発生しています。

![](media/service-fabric-set-up-continuous-integration/vso-configured-agent.png)


## ビルド定義をセットアップする

### アプリケーションのソース管理のために、継続的インテグレーション スクリプトを追加します。

1.	ソース管理内の任意のフォルダーに [ServiceFabricCIAutomationScripts.zip](http://go.microsoft.com/fwlink/?LinkId=703775) を抽出します。
1.	抽出されたファイルをチェックインします。

### ビルド定義を作成する

1.	空のビルド定義を作成します。これを行うには、次の手順を実行します。
    1.	Visual Studio Team Services でプロジェクトを開きます。
    1.	**[ビルド]** タブを選択します。
    1.	緑色の **+** 記号を選択して、新しいビルド定義を作成します。
    1.	**[空]** を選択し、**[次へ]** ボタンをクリックします。
    1.  適切なリポジトリとブランチが選択されていることを確認します。
    1.  ビルド エージェントを登録したエージェント キューを選択し、**[継続的インテグレーション]** チェック ボックスをオンにします。
1.	**[変数]** タブで、次の値を持つ変数を作成します。

    |変数|値|シークレット|キュー時の使用|
    |---|---|---|---|
    |BuildConfiguration|リリース||○|
    |BuildPlatform|x64|||
    |ServicePrincipalPassword|CreateServicePrincipal.ps1 に渡したパスワードです。|○||
    |ServicePrincipalId|CreateServicePrincipal.ps1 の出力から|||
    |ServicePrincipalTenantId|CreateServicePrincipal.ps1 の出力から|||
    |ServicePrincipalSubscriptionId|CreateServicePrincipal.ps1 の出力から|||
    |ServiceFabricCertificateThumbprint|GenerateCertificate.ps1 の出力から|||
    |ServiceFabricKeyVaultId|GenerateCertificate.ps1 の出力から|||
    |ServiceFabricCertificateSecretId|GenerateCertificate.ps1 の出力から|||
    |ServiceFabricClusterResourceGroupName|任意の名前。|||
    |ServiceFabricClusterName|任意の名前。|||
    |ServiceFabricClusterLocation|Key Vault の場所と一致する任意の名前。|||
    |ServiceFabricClusterAdminPassword|任意の名前。|○||
    |ServiceFabricClusterResourceGroupTemplateFilePath|`<path/to/extracted/automation/scripts/ArmTemplate-Full-3xVM-Secure.json>`|||
    |ServiceFabricPublishProfilePath|`<path/to/your/publish/profiles/MyPublishProfile.xml>` 注: 発行プロファイル内の接続のエンドポイントは無視されます。一時クラスター用の接続エンドポイントが使用されます。|||
    |ServiceFabricDeploymentScriptPath|`<path/to/Deploy-FabricApplication.ps1>`|||
    |ServiceFabricApplicationProjectPath|`<path/to/your/fabric/application/project/folder>` これは .sfproj ファイルを含むフォルダーとする必要があります。||||

1.	**[トリガー]** タブで、**[継続的インテグレーション]** と **[一括変更]** オプションを選択します。
1.	**[全般]** タブで、ビルド エージェントを登録したキューを選択します。
1.	ビルド定義を保存して、名前を付けます (この名前は後で必要に応じて変更できます)。

### "ビルド" ステップを追加する

1.	**[ビルド]** タブで、**[ビルド ステップの追加]** コマンドを選択します。
2.	**[ビルド]**、**[MSBuild]** の順に選択します。
3.	ビルド ステップの名前のそばにある鉛筆アイコンを選択し、その名前を「**ビルド**」に変更します。
4.	**[ソリューション]** フィールドの横にある **[...]** ボタンを選択し、.sln ファイルを選択します。
5.	**[プラットフォーム]** に対して「`$(BuildPlatform)`」を入力します。
6.	「**構成**」に対して、「`$(BuildConfiguration)`」を入力します。
7.	**[NuGet パッケージの復元]** チェック ボックスをオンにします (まだ選択されていない場合)。
8.	ビルド定義を保存します。

### "パッケージ" ステップを追加します。

1.	**[ビルド]** タブで、**[ビルド ステップの追加]** コマンドを選択します。
2.	**[ビルド]**、**[MSBuild]** の順に選択します。
3.	ビルド ステップの名前の横にある鉛筆アイコンを選択し、その名前を「**パッケージ**」に変更します。
4.	**[ソリューション]** フィールドの横にある **[...]** ボタンを選択し、アプリケーション プロジェクトの .sfproj ファイルを選択します。
5.	**[プラットフォーム]** に対して「`$(BuildPlatform)`」を入力します。
6.	「**構成**」に対して、「`$(BuildConfiguration)`」を入力します。
7.	**[MSBuild 引数]** に対して「`/t:Package`」を入力します。
8.	**[NuGet パッケージの復元]** チェック ボックスをオフにします (オフにされていない場合)。
9.	ビルド定義を保存します。

### "クラスター リソース グループの削除" ステップを追加する

クリーンアップの後で以前のビルドがクリーンアップされていない場合 (クリーンアップされる前にビルドが取り消された場合など)、既存のリソース グループが新しいものと競合する可能性があります。競合を避けるためには、残されているリソース グループ (および関連付けられているリソース) をいずれもクリーンアップしてから新しいリソース グループを作成します。

1.	**[ビルド]** タブで、**[ビルド ステップの追加]** コマンドを選択します。
2.	**[ユーティリティ]**、**[PowerShell]** の順に選択します。
3.	ビルド ステップの名前の横にある鉛筆アイコンを選択し、その名前を「**クラスター リソース グループの削除**」に変更します。
4.	**[スクリプト ファイル名]** の横にある **[...]** コマンドを選択します。自動化スクリプトを展開した場所に移動し、**Remove-ClusterResourceGroup.ps1** を選択します。
5.	**[引数]** に対して、「`-ServicePrincipalPassword "$(ServicePrincipalPassword)"`」を入力します。
6.	ビルド定義を保存します。

### "プロビジョニングして、セキュリティで保護されたクラスターにデプロイする" ステップを追加します。

1.	**[ビルド]** タブで、**[ビルド ステップの追加]** コマンドを選択します。
2.	**[ユーティリティ]**、**[PowerShell]** の順に選択します。
3.	ビルド ステップの名前の横にある鉛筆アイコンを選択し、その名前を「**プロビジョニングして、セキュリティで保護されたクラスターにデプロイする**」に変更します。
4.	**[スクリプト ファイル名]** の横にある **[...]** ボタンを選択します。自動化スクリプトを展開した場所に移動し、**ProvisionAndDeploy-SecureCluster.ps1** を選択します。
5.	[引数] に対して、「`-ServicePrincipalPassword "$(ServicePrincipalPassword)" -ServiceFabricClusterAdminPassword "$(ServiceFabricClusterAdminPassword)"`」を入力します。
6.	ビルド定義を保存します。

### "クラスター リソース グループの削除" ステップを追加する

これで一時クラスターでの作業が終了したので、それをクリーンアップする必要があります。クリーンアップを行わないと、一時クラスターに対して引き続き料金がかかります。この手順ではリソース グループを削除します。これによって、クラスターと、グループ内の他のすべてのリソースが削除されます。

1.	**[ビルド]** タブで、**[ビルド ステップの追加]** コマンドを選択します。
1.	**[ユーティリティ]**、**[PowerShell]** の順に選択します。
1.	ビルド ステップの名前の横にある鉛筆アイコンを選択し、その名前を「**クラスター リソース グループの削除**」に変更します。
1.	**[スクリプト ファイル名]** の横にある **[...]** ボタンを選択します。自動化スクリプトを展開した場所に移動し、**RemoveClusterResourceGroup.ps1** を選択します。
1.	[引数] に対して、「`-ServicePrincipalPassword "$(ServicePrincipalPassword)`」を入力します。
1.	**[コントロール オプション]** の **[常に実行する]** チェック ボックスをオンにします。
1.	ビルド定義を保存します。

### 試してみる

**[キュー ビルド]** コマンドを選択してビルドを開始します。ビルドは、チェックイン時にもトリガーされます。


## 代替ソリューション

上記手順は、ビルドごとに新しいクラスターを作成し、ビルドの最後にそれを削除するというものです。各ビルドでアプリケーションのアップグレード (既存のクラスターに対して) を行う場合は、次の手順を実行します。

1.	Microsoft Azure 管理ポータルまたは Azure PowerShell を使用して手動でテスト クラスターを作成します。リファレンスとして "ProvisionAndDeploy-SecureCluster.ps1" スクリプトを参照することができます。
1.	アプリケーションのアップグレードをサポートするように発行プロファイルを構成します。[こちらの手順](service-fabric-visualstudio-configure-upgrade.md)を参照してください。

1.	**プロビジョニングして、セキュリティで保護されたクラスターにデプロイする** ステップを、Deploy-FabricApplication.ps1 を直接呼び出すステップに置換します (そして、それを発行プロファイルに渡します)。
1.	ビルド定義から **クラスター リソース グループの削除** ビルド ステップを両方とも削除します。

## 次のステップ

Service Fabric アプリケーションとの継続的インテグレーションの詳細については、「[ビルドに関するドキュメントのホームページ](https://msdn.microsoft.com/Library/vs/alm/Build/overview)」、「[ビルド エージェントのデプロイ](https://msdn.microsoft.com/Library/vs/alm/Build/agents/windows)」、「[ビルド定義の作成と構成](https://msdn.microsoft.com/Library/vs/alm/Build/vs/define-build)」を参照してください。

<!----HONumber=AcomDC_1217_2015-->