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
   ms.date="01/27/2016"
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

1.	Azure PowerShell をインストールします。
2. PowerShellGet をインストールします。このためには、PowerShellGet が付属している [Windows Management Framework 5.0](http://www.microsoft.com/download/details.aspx?id=48729) をインストールします。

    >[AZURE.NOTE] 最新の更新プログラムを適用した Windows 10 を実行している場合は、この手順を省略できます。

3.	AzureRM モジュールをインストールし更新します。Azure PowerShell の以前のバージョンがインストールされている場合は、それを削除します。

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

1.	[ServiceFabricContinuousIntegrationScripts.zip](https://gallery.technet.microsoft.com/Set-up-continuous-f8b251f6) をダウンロードして、このコンピューター上のフォルダーに展開します。

2.	管理者 PowerShell コマンド プロンプトで、展開されたアーカイブ内のディレクトリ `Powershell\Manual` に移動します。

3.	次のコマンドを使用してサービス プリンシパルのパスワードを選択します。このパスワードはビルド変数として使用するため、覚えておいてください。

    ```
    $password = Read-Host -AsSecureString
    ```
4.	次のパラメーターを使用して PowerShell スクリプト Create-ServicePrincipal.ps1 を実行します。

|パラメーター|値|
|---|---|
|DisplayName|任意の名前。|
|HomePage|任意の URI。実際に存在する必要はありません。|
|IdentifierUri|任意の一意の URI。実際に存在する必要はありません。|
|SecurePassword|$password|

スクリプトが完了すると、次の 3 つの値が出力されます。これらの値はビルド変数として使用するので、メモしておいてください。

 - `ServicePrincipalId`
 - `ServicePrincipalTenantId`
 - `ServicePrincipalSubscriptionId`

### 証明書を作成し、新しい Azure Key Vault のインスタンスにアップロードする

>[AZURE.NOTE] このサンプル スクリプトは、自己署名証明書を生成します。これは安全なプラクティスではなく、実験の場合にだけ許容できます。代わりに、組織のガイドラインに従って、正規の証明書を取得してください。

1.	管理者 PowerShell プロンプトで、`Manual.zip` を展開したディレクトリに移動します。

2.	次のパラメーターを指定して PowerShell スクリプト `CreateAndUpload-Certificate.ps1` を実行します。

| パラメーター | 値 |
| --- | --- |
| KeyVaultLocation | 任意の値。このパラメーターは、クラスターを作成する場所と一致する必要があります。 |
| CertificateSecretName | 任意の値。 |
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

5. **[Compute]**、**[仮想マシン]**、**[ギャラリーから]** の順にクリックします。

6. **[Windows Server 2012 R2 での Visual Studio Enterprise 2015 Update 1 と Azure SDK 2.8]** イメージを選択します。

    >[AZURE.NOTE] Azure SDK は必須のコンポーネントではありませんが、Visual Studio 2015 のみがインストールされているイメージは、現在は用意されていません。

7.	ダイアログ ボックスの指示に従って VM を作成します

### Service Fabric SDK をインストールします。

コンピューターに [Service Fabric SDK](https://azure.microsoft.com/campaigns/service-fabric/) をインストールします。

### Azure PowerShell をインストールするには

Azure PowerShell をインストールするには、前のセクション「Azure PowerShell をインストールしてサインインする」の手順に従ってください。「Azure PowerShell にサインインします」の手順は省略します。

### ローカル サービス アカウントに Azure PowerShell モジュールを登録します。

>[AZURE.NOTE] ビルド エージェントを開始する*前*にこれを行います。そうしないと、新しい環境変数が反映されません。

1. Windows ロゴ キーと R キーを押し、「**regedit**」と入力して Enter キーを押します。

2. ノード `HKEY_Users\.Default\Environment` を右クリックし、**[新規]**、**[展開可能な文字列値]** の順に選択します。

3. 名前として「`PSModulePath`」と入力し、値として「`%PROGRAMFILES%\WindowsPowerShell\Modules`」と入力します。`%PROGRAMFILES%` は、`PROGRAMFILES` 環境変数の値に置き換えます。

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

3.	オートメーション証明書を使用するアクセス許可をローカル サービス アカウントに付与します。

    a.**[証明書 - ローカル コンピューター]** で、**[個人]** を展開し、**[証明書]** を選択します。

    b.一覧の中から目的の証明書を探します。

    c.証明書を右クリックして、**[すべてのタスク]**、**[秘密キーの管理]** の順に選択します。

    d.**[追加]** ボタンを選択し、「**ローカル サービス**」と入力し、**[名前の確認]** を選択します。

    e.**[OK]** を選択し、証明書マネージャーを閉じます。

 ![ローカル サービス アカウントのアクセス許可を付与するための手順のスクリーンショット](media/service-fabric-set-up-continuous-integration/windows-certificate-manager.png)

### ビルド エージェントを登録する

1.	agent.zip をダウンロードします。これを行うには、次の手順を実行します。

    a.* **https://[your-VSTS-account-name].visualstudio.com** などのチーム プロジェクトにサインインします。

    b.画面の右上隅にある歯車アイコンを選択します。

    c.**[エージェント プール]** タブを選択します。

    d.**[ダウンロード エージェント]** を選択し、agent.zip ファイルをダウンロードします。

    e.先ほど作成したビルド コンピューターに agent.zip をコピーします。

    f.agent.zip をビルド コンピューターの `C:\agent` (または短いパスの任意の場所) に解凍します。

    >[AZURE.NOTE] ASP.NET 5 Web サービスを使用する場合は、デプロイ中に **PathTooLongExceptions** エラーが発生するのを防ぐために、フォルダーに対しては可能な限り短い名前を選択することをお勧めします。

2.	管理者のコマンド プロンプトで、`C:\agent\ConfigureAgent.cmd` を実行します。スクリプトは次のパラメーターに対して入力を求めるメッセージを表示します。

|パラメーター|値|
|---|---|
|エージェント名|既定値 `Agent-[machine name]` をそのまま使用します。|
|TFS URL|チーム プロジェクトに `https://[your-VSTS-account-name].visualstudio.com` のような URL を入力します。|
|エージェント プール|エージェント プールの名前を入力します(エージェント プールを作成していない場合は、既定値を使用します)。|
|作業フォルダー|既定値をそのまま使用します。これは、ビルド エージェントによってアプリケーションが実際にビルドされるフォルダーです。ASP.NET 5 Web サービスを使用する場合は、デプロイ中に PathTooLongExceptions エラーが発生するのを防ぐために、フォルダーに対しては可能な限り短い名前を選択することをお勧めします。|
|Windows サービスとしてインストールしますか?|既定値は N です。値を **Y** に変更します。|
|サービスを実行するユーザー アカウント|既定値 `NT AUTHORITY\LocalService` をそのまま使用します。|
|既存のエージェントの構成を解除しますか?|既定値 **N** をそのまま使用します。|

3.  資格情報の指定を求められたら、チーム プロジェクトに対する権限を持つ Microsoft アカウントの資格情報を入力します。

4.  ビルド エージェントが登録されていることを確認します。これを行うには、次の手順を実行します。

    a.Web ブラウザー (`https://[your-VSTS-account-name].visualstudio.com/_admin/_AgentPool`) に戻り、ページを更新します。

    b.前に ConfigureAgent.ps1 を実行したときに選択したエージェント プールを選びます。

    c.ビルド エージェントが一覧の中に表示され、緑色で強調表示されていることを確認します。赤色で強調表示されている場合は、ビルド エージェントで Team Services への接続に関する問題が発生しています。

 ![ビルド エージェントの状態を示すスクリーンショット](media/service-fabric-set-up-continuous-integration/vso-configured-agent.png)


## ビルド定義を作成する

>[AZURE.NOTE] 次の手順で作成するビルド定義は、別々のコンピューター上であっても、複数の同時実行ビルドをサポートしません。これは、各ビルドが同じリソース グループまたはクラスターに対して実行されるためです。複数のビルド エージェントを実行する場合は、次の手順/スクリプトを変更して前述制限を解消する必要があります。

### アプリケーションのソース管理のために、継続的インテグレーション スクリプトを追加する

1.	[ServiceFabricContinuousIntegrationScripts.zip](https://gallery.technet.microsoft.com/Set-up-continuous-f8b251f6) をコンピューター上の任意のフォルダーに展開します。`Powershell\Automation` の内容をソース管理内の任意のフォルダーにコピーします。

2.	抽出されたファイルをチェックインします。

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
|BuildPlatform|x64|||
|ServicePrincipalPassword|CreateServicePrincipal.ps1 に渡したパスワードです。|○||
|ServicePrincipalId|CreateServicePrincipal.ps1 の出力から。|||
|ServicePrincipalTenantId|CreateServicePrincipal.ps1 の出力から。|||
|ServicePrincipalSubscriptionId|CreateServicePrincipal.ps1 の出力から。|||
|ServiceFabricCertificateThumbprint|GenerateCertificate.ps1 の出力から。|||
|ServiceFabricKeyVaultId|GenerateCertificate.ps1 の出力から。|||
|ServiceFabricCertificateSecretId|GenerateCertificate.ps1 の出力から。|||
|ServiceFabricClusterName|任意の名前。|||
|ServiceFabricClusterResourceGroupName|任意の名前。|||
|ServiceFabricClusterLocation|Key Vault の場所と一致する任意の名前。|||
|ServiceFabricClusterAdminPassword|任意の名前。|○||
|ServiceFabricClusterResourceGroupTemplateFilePath|`<path/to/extracted/automation/scripts/ArmTemplate-Full-3xVM-Secure.json>`|||
|ServiceFabricPublishProfilePath|`<path/to/your/publish/profiles/MyPublishProfile.xml>`発行プロファイル内の接続のエンドポイントは無視されます。一時クラスター用の接続エンドポイントが使用されます。|||
|ServiceFabricDeploymentScriptPath|`<path/to/Deploy-FabricApplication.ps1>`|||
|ServiceFabricApplicationProjectPath|`<path/to/your/fabric/application/project/folder>` これは .sfproj ファイルを含むフォルダーとする必要があります。||||

3.  ビルド定義を保存して、名前を付けます (この名前は後で必要に応じて変更できます)。

### "ビルド" ステップを追加する

1.	**[ビルド]** タブで、**[ビルド ステップの追加]** コマンドを選択します。

2.	**[ビルド]**、**[MSBuild]** の順に選択します。

3.	ビルド ステップの名前のそばにある鉛筆アイコンを選択し、その名前を「**ビルド**」に変更します。

4.	**[ソリューション]** フィールドの横にある **[...]** ボタンを選択し、.sln ファイルを選択します。

5.	**[プラットフォーム]** に対して「`$(BuildPlatform)`」を入力します。

6.	「**構成**」に対して、「`$(BuildConfiguration)`」を入力します。

7.	**[NuGet パッケージの復元]** チェック ボックスをオンにします (まだ選択されていない場合)。

8.	ビルド定義を保存します。

### "パッケージ" ステップを追加する

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

### "プロビジョニングして、セキュリティで保護されたクラスターにデプロイする" ステップを追加する

1.	**[ビルド]** タブで、**[ビルド ステップの追加]** コマンドを選択します。

2.	**[ユーティリティ]**、**[PowerShell]** の順に選択します。

3.	ビルド ステップの名前の横にある鉛筆アイコンを選択し、その名前を「**プロビジョニングして、セキュリティで保護されたクラスターにデプロイする**」に変更します。

4.	**[スクリプト ファイル名]** の横にある **[...]** ボタンを選択します。自動化スクリプトを展開した場所に移動し、**ProvisionAndDeploy-SecureCluster.ps1** を選択します。

5.	**[引数]** に、「`-ServicePrincipalPassword "$(ServicePrincipalPassword)" -ServiceFabricClusterAdminPassword "$(ServiceFabricClusterAdminPassword)"`」と入力します。

6.	ビルド定義を保存します。

### "クラスター リソース グループの削除" ステップを追加する

これで一時クラスターでの作業が終了したので、それをクリーンアップする必要があります。クリーンアップを行わないと、一時クラスターに対して引き続き料金がかかります。この手順ではリソース グループを削除します。これによって、クラスターと、グループ内の他のすべてのリソースが削除されます。

>[AZURE.NOTE] このステップと、前の "クラスター リソース グループの削除" ステップには違いが 1 つあります。このステップでは、**[常に実行する]** チェック ボックスをオンにしておく必要があります。

1.	**[ビルド]** タブで、**[ビルド ステップの追加]** コマンドを選択します。

2.	**[ユーティリティ]**、**[PowerShell]** の順に選択します。

3.	ビルド ステップの名前の横にある鉛筆アイコンを選択し、その名前を「**クラスター リソース グループの削除**」に変更します。

4.	**[スクリプト ファイル名]** の横にある **[...]** ボタンを選択します。自動化スクリプトを展開した場所に移動し、**RemoveClusterResourceGroup.ps1** を選択します。

5.	**[引数]** に対して、「`-ServicePrincipalPassword "$(ServicePrincipalPassword)`」と入力します。

6.	**[コントロール オプション]** の **[常に実行する]** チェック ボックスをオンにします。

7.	ビルド定義を保存します。

### 試してみる

**[キュー ビルド]** を選択してビルドを開始します。ビルドは、プッシュ時またはチェックイン時にもトリガーされます。


## 代替ソリューション

上記手順は、ビルドごとに新しいクラスターを作成し、ビルドの最後にそれを削除するというものです。各ビルドでアプリケーションのアップグレード (既存のクラスターに対して) を行う場合は、次の手順を実行します。

1.	Azure ポータルまたは Azure PowerShell を使用して手動でテスト クラスターを作成します。`ProvisionAndDeploy-SecureCluster.ps1` スクリプトを参照することができます。

2.	アプリケーションのアップグレードをサポートするように発行プロファイルを構成します。[こちらの手順](service-fabric-visualstudio-configure-upgrade.md)を参照してください。

3.	**プロビジョニングして、セキュリティで保護されたクラスターにデプロイする** ステップを、Deploy-FabricApplication.ps1 を直接呼び出すステップに置換します (そして、それを発行プロファイルに渡します)。

4.	ビルド定義から **クラスター リソース グループの削除** ビルド ステップを両方とも削除します。

## 次のステップ

Service Fabric アプリケーションの継続的インテグレーションに関する詳細については、次の記事を参照してください。

 - [ビルドに関するドキュメントのホームページ](https://msdn.microsoft.com/Library/vs/alm/Build/overview)
 - [ビルド エージェントのデプロイ](https://msdn.microsoft.com/Library/vs/alm/Build/agents/windows)
 - [ビルド定義の作成と構成](https://msdn.microsoft.com/Library/vs/alm/Build/vs/define-build)

<!---HONumber=AcomDC_0330_2016------>