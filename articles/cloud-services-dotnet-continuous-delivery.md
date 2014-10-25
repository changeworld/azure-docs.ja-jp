<properties linkid="dev-net-common-tasks-continuous-delivery" urlDisplayName="Continuous Delivery" pageTitle="Continuous delivery for cloud services with TFS in Azure" metaKeywords="Azure continuous delivery, continuous delivery sample code, continuous delivery PowerShell" description="Learn how to set up continuous delivery for Azure cloud apps. Code samples for MSBuild command-line statements and PowerShell scripts." metaCanonical="" services="" documentationCenter="" title="Continuous Delivery for Cloud Services in Azure" authors="ghogen" solutions="" manager="" editor="" />

<tags ms.service="cloud-services" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="ghogen"></tags>

# Azure でのクラウド サービスの継続的な配信

この記事のプロセスでは、Azure クラウド アプリケーションの継続的な配信を設定する方法について説明します。このプロセスを使用すると、自動的にパッケージを作成し、コードをチェックインするたびにそのパッケージを Azure に展開できます。この記事で説明するパッケージのビルド プロセスは、Visual Studio のPackage コマンドに対応し、発行手順は、Visual Studio の Publish コマンドに対応します。この記事では、MSBuild コマンド ライン ステートメントとWindows PowerShell スクリプトを使用してビルド サーバーを作成するためのいくつかの方法を紹介し、オプションで Visual Studio Team Foundation Server を構成する方法 (MSBuild コマンドと PowerShell スクリプトを使用するためのチーム ビルド定義) について説明します。このプロセスは、ビルド環境や Azure ターゲット環境に合わせてカスタマイズできます。

また、Visual Studio Online (Azure でホストされる TFS のバージョン) を使用すると、これらの操作をより簡単に実行できます。詳細については、「[Continuous Delivery to Azure by Using Visual Studio Online (Visual Studio Online を使用した Azure への継続的な配信)][Continuous Delivery to Azure by Using Visual Studio Online (Visual Studio Online を使用した Azure への継続的な配信)]」を参照してください。

作業を開始する前に、Visual Studio からアプリケーションを発行する必要があります。これにより、発行プロセスの自動化を行う際にすべてのリソースが利用可能で、初期化されることを確認できます。

このタスクの手順は次のとおりです。

-   [手順 1.ビルド サーバーを構成する][手順 1.ビルド サーバーを構成する]
-   [手順 2.MSBuild コマンドを使用してパッケージをビルドする][手順 2.MSBuild コマンドを使用してパッケージをビルドする]
-   [手順 3.TFS チーム ビルドを使用してパッケージをビルドする (省略可能)][手順 3.TFS チーム ビルドを使用してパッケージをビルドする (省略可能)]
-   [手順 4.PowerShell スクリプトを使用してパッケージを発行する][手順 4.PowerShell スクリプトを使用してパッケージを発行する]
-   [手順 5.TFS チーム ビルドを使用してパッケージを発行する (省略可能)][手順 5.TFS チーム ビルドを使用してパッケージを発行する (省略可能)]

## <a name="step1"> </a><span class="short-header">ビルド サーバーを構成する</span>手順 1: ビルド サーバーを構成する

MSBuild を使用して Azure パッケージを作成するには、ビルド サーバーに必要なソフトウェアおよびツールを事前にインストールしておく必要があります。

ビルド サーバーには、Visual Studio をインストールする必要はありません。
Team Foundation ビルド サービスを使用してビルド サーバーを管理する場合は、
[Team Foundation ビルド サービス][Team Foundation ビルド サービス]
のドキュメントの説明に従ってください。

1.  ビルド サーバーに [.NET Framework 4][.NET Framework 4] または [.NET Framework 4.5][.NET Framework 4.5] をインストールします。これには MSBuild が含まれています。
2.  [Azure Authoring Tools][Azure Authoring Tools] をインストールします (ビルド サーバーのプロセッサに応じて、WindowsAzureAuthoringTools-x86.msi または WindowsAzureAuthoringTools-x64.msi を選択してください)。
3.  [Azure Libraries][Azure Libraries] をインストールします (WindowsAzureLibsForNet-x86.msi または WindowsAzureLibsForNet-x64.msi のいずれか該当する方を探して使用)。
4.  インストール済みの Visual Studio の Microsoft.WebApplication.targets ファイルをビルド サーバーにコピーします。
    このファイルは、Visual Studio がインストールされているコンピューターの C:\\\\Program Files
    (x86)\\\\MSBuild\\\\Microsoft\\\\VisualStudio\\\\v11.0\\\\WebApplications (Visual Studio 2013 の場合は v12.0) ディレクトリに含まれています。
    このファイルを、ビルド サーバー上の同じディレクトリにコピーします。
5.  [Azure Tools for Visual Studio][Azure Libraries] をインストールします。Visual Studio 2012 のプロジェクトのビルドには WindowsAzureTools.VS110.exe、Visual Studio 2013 のプロジェクトのビルドには WindowsAzureTools.VS120.exe を探して使用します。

## <a name="step2"> </a><span class="short-header">MSBuild を使用してパッケージをビルドする</span>手順 2: MSBuild コマンドを使用してパッケージをビルドする

このセクションでは、Azure パッケージをビルドする MSBuild コマンドを作成する方法について説明します。ビルド サーバー上でこの手順を実行して、すべてが適切に構成され、MSBuild コマンドが予定どおりに動作することを確認します。このコマンド ラインは、ビルド サーバー上の既存のビルド スクリプトに追加することができます。また、次のセクションで説明するように、TFS ビルド定義内でコマンド ラインを使用してもかまいません。コマンド ライン パラメーターおよび MSBuild の詳細については、[MSBuild コマンド ライン リファレンスのページ][MSBuild コマンド ライン リファレンスのページ]を参照してください。

1.  ビルド サーバーに Visual Studio が インストールされている場合は、**[スタート]** メニューの **[すべてのプログラム]** をクリックし、**[Visual Studio Tools]** の **[Visual Studio コマンド プロンプト]**をクリックします。

    Visual Studio がビルド サーバーにインストールされていない場合は、コマンド プロンプトを開き、MSBuild.exe へのパスを確認します。MSBuild は、.NET Framework と共に %WINDIR%\\Microsoft.NET\\Framework\\*Version* というパスにインストールされます。たとえば、.NET Framework 4 をインストールしたときに MSBuild.exe を PATH 環境変数に追加するには、コマンド プロンプトで次のコマンドを入力します。

        set PATH=%PATH%;"C:\Windows\Microsoft.NET\Framework.0"

2.  コマンド プロンプトで、ビルドする Azure プロジェクト ファイルが含まれているフォルダーに移動します。

3.  次の例に示すように、/target:Publish オプションを指定して msbuild を実行します。

        MSBuild /target:Publish

    このオプションは、/t:Publish のように短縮できます。Azure SDK をインストールしている場合は、MSBuild の /t:Publish オプションと Visual Studio の Publish コマンドとを混同しないように注意してください。 /t:Publish オプションは、Azure パッケージをビルドするのみです。 Visual Studio の Publish コマンドとは異なり、パッケージを展開しません。

    必要に応じて、プロジェクト名を MSBuild のパラメーターとして指定できます。 このパラメーターを指定しない場合は、現在のディレクトリが使用されます。MSBuild のコマンド ライン オプションの詳細については、[MSBuild コマンド ライン リファレンスのページ][MSBuild コマンド ライン リファレンスのページ]を参照してください。

4.  出力を見つけます。既定では、プロジェクトのルート フォルダーに対してディレクトリが作成されます (たとえば、*ProjectDir*\\bin\\*Configuration*\\app.publish\\)。Azure プロジェクトをビルドすると、パッケージ ファイルとそれに対応する構成ファイルの 2 つのファイルが生成されます。

    -   Project.cspkg
    -   ServiceConfiguration.*TargetProfile*.cscfg

    既定で、各 Azure プロジェクトでは 1 つのサービス構成ファイル (.cscfg ファイル) でローカル (デバッグ) ビルドとクラウド (ステージングまたは運用) ビルドに対応しますが、必要に応じてサービス構成ファイルを追加したり削除したりできます。Visual Studio 内でパッケージをビルドするときは、パッケージに含めるサービス構成ファイルを指定するよう求められます。

5.  サービス構成ファイルを指定します。MSBuild を使用してパッケージをビルドする場合、既定でローカル サービス構成ファイルが含められます。別のサービス構成ファイルを含めるには、次の例に示すように、MSBuild コマンドの TargetProfile プロパティを設定します。

        MSBuild /t:Publish /p:TargetProfile=Cloud

6.  出力先を指定します。/p:PublishDir=*Directory*\\ オプションを使用してパスを設定します。次の例に示すように、末尾にバックスラッシュ区切り記号を指定します。

        MSBuild /target:Publish /p:PublishDir=\myserver\drops\

    プロジェクトをビルドして Azure パッケージに結合するために使用する適切な MSBuild コマンド ラインの作成とテストが完了したら、このコマンド ラインをビルド スクリプトに追加します。ビルド サーバーでカスタム スクリプトを使用する場合、このプロセスは、ビルド カスタム プロセスの特定の内容に依存します。ビルド環境として TFS を使用している場合は、次のステップに従って Azure パッケージ ビルドをビルド プロセスに追加できます。

## <a name="step3"> </a><span class="short-header">TFS を使用してパッケージをビルドする</span>手順 3: TFS チーム ビルドを使用してパッケージをビルドする (省略可能)

ビルド コントローラーとして Team Foundation Server (TFS) を設定し、TFS ビルド コンピューターとしてビルド サーバーを設定している場合、Azure パッケージの自動化されたビルドを設定できます。Team Foundation Server をビルド システムとして設定および使用する方法については、「[Team Foundation ビルド システムについて][Team Foundation ビルド システムについて]」を参照してください。特に、次の手順では、「[ビルド コンピューターの構成][ビルド コンピューターの構成]」の説明に従ってビルド サーバーが構成されていることを前提としています。

Azure パッケージをビルドするために TFS を構成するには、次の手順を実行します。

1.  開発コンピューターの Visual Studio で、[表示] メニューの **[チーム エクスプローラー]** をクリックするか、Ctrl + \\ キーに続けて Ctrl + M キーを押します。[チーム エクスプローラー] ウィンドウで、**[ビルド]** ノードを展開します。**[すべてのビルド定義]** を右クリックし、**[ビルド定義の新規作成]** をクリックします。

    ![][]

2.  **[プロセス]** タブをクリックします。[プロセス] タブで、既定のテンプレートを選択します。[ビルドする項目] で、プロジェクトを選択し、グリッドの **[詳細設定]** セクションを展開します。

3.  **[MSBuild 引数]** を選択し、上の手順 2. で説明したように適切な MSBuild コマンド ライン引数を設定します。たとえば、パッケージをビルドしてパッケージ ファイルを \\\\myserver\\drops\\ にコピーするには、「**/t:Publish /p:PublishDir=\\\\myserver\\drops\\**」と入力します。

    ![][1]

    **注:** ファイルをパブリック共有にコピーしておくと、開発コンピューターからパッケージを手動で展開する場合に便利です。

4.  **[トリガー]** タブをクリックし、パッケージのビルドを実行する条件を指定します。ソース管理でチェックインが実行されるたびにパッケージをビルドするには、**[継続的インテグレーション]** を指定します。

5.  **[ビルドの既定値]** タブを選択します。[ビルド コントローラー] で、ビルド サーバーの名前を確認します。さらに、**[ビルド出力を次の格納フォルダーにコピーする]** オプションを選択し、目的の格納場所を指定します。

6.  変更をプロジェクトにチェックインしてビルド手順が成功したかどうかをテストするか、または新しいビルドをキューに配置します。新しいビルドをキューに配置するには、チーム エクスプローラーで、**[すべてのビルド定義]** を右クリックし、**[新しいビルドをキューに配置]** を選択します。

## <a name="step4"> </a><span class="short-header">PowerShell を使用して発行する</span>手順 4: PowerShell スクリプトを使用してパッケージを発行する

このセクションでは、オプション パラメーターを使用して、クラウド アプリケーション パッケージ出力を Azure に発行する Windows PowerShell スクリプトを作成する方法について説明します。このスクリプトは、カスタム ビルド自動化のビルド手順の後に呼び出すことができます。このスクリプトは、Visual Studio TFS チーム ビルドのプロセス テンプレート ワークフロー アクティビティから呼び出すこともできます。

1.  [Azure PowerShell コマンドレット][Azure PowerShell コマンドレット] (v0.6.1 以降) をインストールします。
    コマンドレット設定フェーズで、スナップインとしてインストールすることを選択します。従来 CodePlex で提供されていたバージョンは 2.x.x でしたが、これはそれに代わる公式にサポートされたバージョンです。

2.  [スタート] メニューを使用して Azure PowerShell を起動します。この方法で起動すると、Azure PowerShell コマンドレットが読み込まれます。

3.  PowerShell プロンプトで、PowerShell コマンドレットが読み込まれているかどうかを確認します。そのためには、部分的なコマンド Get-Azure を入力し、Tab キーを押して、ステートメントが補完されるかどうかを確認します。

    さまざまな Azure PowerShell コマンドが表示されるのが確認できます。

4.  Azure サブスクリプションに接続できることを確認します。
    そのためには、サブスクリプション情報を publishsettings ファイルからインポートします。

    Import-AzurePublishSettingsFile c:\\scripts\\WindowsAzure\\default.publishsettings

    次に、次のコマンドを入力します。

    Get-AzureSubscription

    サブスクリプションに関する情報が表示されます。すべての情報が正しいことを確認します。

5.  [この記事の末尾][この記事の末尾]に示されているスクリプト テンプレートをスクリプト フォルダーに
    c:\\scripts\\WindowsAzure\\**PublishCloudService.ps1** として保存します。

6.  スクリプトのパラメーター セクションを見直します。既定値を追加または変更します。これらの値は、明示的なパラメーター値を渡すことでオーバーライドできます。

7.  発行スクリプトの対象となる有効なクラウド サービスおよびストレージ アカウントがサブスクリプションに作成されていることを確認します。ストレージ アカウント (BLOB ストレージ) はアップロード用に使用され、展開を作成するときに展開パッケージと config ファイルを一時的に格納します。

    -   新しいクラウド サービスを作成するには、このスクリプトを呼び出すか、Azure の管理ポータルを使用することができます。クラウド サービス名は、完全修飾ドメイン名のプレフィックスとして使用されるため、一意である必要があります。

            New-AzureService -ServiceName "mytestcloudservice" -Location "North Central US" -Label "mytestcloudservice"

    -   新しいストレージ アカウントを作成するには、このスクリプトを呼び出すか、Azure の管理ポータルを使用します。ストレージ アカウント名は、完全修飾ドメイン名のプレフィックスとして使用されるため、一意である必要があります。クラウド サービスと同じ名前を使用してもかまいません。

            New-AzureStorageAccount -ServiceName "mytestcloudservice" -Location "North Central US" -Label "mytestcloudservice"

8.  スクリプトは、Azure PowerShell から直接呼び出す以外に、ホスト ビルド自動化に関連付けてパッケージがビルドされるたびに実行されるように設定できます。

    **警告:** このスクリプトは、既定で既存の配置を常に削除するか置き換えます (既存の展開が検出された場合)。これは、ユーザーへのプロンプトが不可能な自動化から継続的な配信を実現するために必要な操作です。

    **サンプル シナリオ 1:** サービスのステージング環境への継続的な配置

        PowerShell c:\scripts\windowsazure\PublishCloudService.ps1 -environment Staging -serviceName mycloudservice -storageAccountName mystoragesaccount -packageLocation c:\drops\app.publish\ContactManager.Azure.cspkg -cloudConfigLocation c:\drops\app.publish\ServiceConfiguration.Cloud.cscfg -subscriptionDataFile c:\scripts\default.publishsettings

    通常は、この後にテスト実行検証と VIP スワップが行われます。
    VIP スワップは、Azure の管理ポータルまたは Move-Deployment コマンドレットを使用して実行できます。

    **サンプル シナリオ 2:** 専用テスト サービスの運用環境への継続的な配置

        PowerShell c:\scripts\windowsazure\PublishCloudService.ps1 -environment Production -enableDeploymentUpgrade 1 -serviceName mycloudservice -storageAccountName mystorageaccount -packageLocation c:\drops\app.publish\ContactManager.Azure.cspkg -cloudConfigLocation c:\drops\app.publish\ServiceConfiguration.Cloud.cscfg -subscriptionDataFile c:\scripts\default.publishsettings

    **リモート デスクトップ:**

    Azure プロジェクトでリモート デスクトップが有効になっている場合は、1 回限りの追加手順を実行して、適切なクラウド サービス証明書がこのスクリプトの対象となっているすべてのクラウド サービスにアップロードされることを確認する必要があります。

    自分のロールに該当する証明書のサムプリント値を見つけます。サムプリント値は、クラウド構成ファイル (ServiceConfiguration.Cloud.cscfg) の Certificates セクションに記載されています。また、サムプリント値は、Visual Studio の [リモート デスクトップ構成] ダイアログで [オプションの表示] を選択し、選択した証明書を表示する場合にも表示されます。

        <Certificates>
              <Certificate name="Microsoft.WindowsAzure.Plugins.RemoteAccess.PasswordEncryption" thumbprint="C33B6C432C25581601B84C80F86EC2809DC224E8" thumbprintAlgorithm="sha1" />
        </Certificates>

    1 回限りの設定手順として、次のコマンドレット スクリプトを使用してリモート デスクトップ証明書をアップロードします。

        Add-AzureCertificate -serviceName <CLOUDSERVICENAME> -certToDeploy (get-item cert:\CurrentUser\MYAdd-AzureCertificate -serviceName <CLOUDSERVICENAME> -certToDeploy (get-item cert:\CurrentUser\MY\<THUMBPRINT>)lt;THUMBPRINT>)

    次に例を示します。

        Add-AzureCertificate -serviceName 'mytestcloudservice' -certToDeploy (get-item cert:\CurrentUser\MY\C33B6C432C25581601B84C80F86EC2809DC224E8

    または、Azure の管理ポータルを使用して、証明書ファイル PFX を秘密キーと共にエクスポートし、証明書を各ターゲット クラウド サービスにアップロードすることもできます。詳細については、<http://msdn.microsoft.com/ja-jp/library/windowsazure/gg443832.aspx> を参照してください。

    **配置のアップグレードと、配置の削除後に新しい配置を作成する操作**

    スクリプトでは、パラメーターが渡されないとき、または値 1 が明示的に渡されたときに、既定で配置のアップグレードが実行されます ($enableDeploymentUpgrade = 1)。単一のインスタンスに対しては、完全な配置に比べて処理に要する時間が少なくなるという利点があります。また、高い可用性が求められるインスタンスに対しても、いくつかのインスタンスを実行中に (更新ドメインの) 他のインスタンスをアップグレードできることに加え、VIP が削除されないという利点があります。

    配置のアップグレード操作は、スクリプトで ($enableDeploymentUpgrade = 0)、またはパラメーターとして –enableDeploymentUpgrade 0 を渡すことによって無効にできます。これにより、スクリプトの動作が変更され、既存の配置が削除された後、新しい配置が作成されます。

    **警告:** このスクリプトは、既定で既存の配置を常に削除するか置き換えます (既存の展開が検出された場合)。これは、ユーザー/オペレーターへのプロンプトが不可能な自動化から継続的な配信を実現するために必要な操作です。

## <a name="step5"> </a><span class="short-header">TFS を使用して発行する </span>手順 5: TFS チーム ビルドを使用してパッケージを発行する (省略可能)

この手順では、Azure へのパッケージ ビルドの発行を処理する、ステップ 4. で作成したスクリプトに TFS チーム ビルドを関連付けます。これには、ビルド定義によって使用されるプロセス テンプレートを変更して、ワークフローの最後に発行アクティビティが実行されるようにする操作が含まれます。発行アクティビティでは、ビルドからのパラメーターを渡して PowerShell コマンドを実行します。MSBuild ターゲットと発行スクリプトの出力は、パイプを使用して標準ビルド出力に送られます。

1.  継続的な展開のためのビルド定義を編集します。

2.  **[プロセス]** タブを選択します。

3.  タブの [プロセス テンプレートの選択] 領域にある [新規作成] ボタンをクリックして、新しいプロセス テンプレートを作成します。[新しいビルド プロセス テンプレート] ダイアログが開きます。このダイアログの [既存の XAML ファイルをコピー] が選択されていることを確認します。既定値を変更する場合は、コピーする ProcessTemplate を参照します。新しいテンプレートに名前を付けます (たとえば、DefaultTemplateAzure)。

4.  選択したプロセス テンプレートを編集するために開きます。ワークフロー デザイナーで直接開くことも、XML エディターを使って XAML を操作することもできます。

5.  次の新しい引数を、ワークフロー デザイナーの引数タブに別の行項目として追加します。すべての引数について、方向として [入力]、型として [文字列] を設定する必要があります。これらは、ビルド定義のパラメーターを、発行スクリプトを呼び出すワークフローに送るために使用されます。

        SubscriptionName
        StorageAccountName
        CloudConfigLocation
        PackageLocation
        Environment
        SubscriptionDataFileLocation
        PublishScriptLocation
        ServiceName

    ![][2]

    対応する XAML は次のようになります。

        <Activity  _ />
          <x:Members>
            <x:Property Name="BuildSettings" Type="InArgument(mtbwa:BuildSettings)" />
            <x:Property Name="TestSpecs" Type="InArgument(mtbwa:TestSpecList)" />
            <x:Property Name="BuildNumberFormat" Type="InArgument(x:String)" />
            <x:Property Name="CleanWorkspace" Type="InArgument(mtbwa:CleanWorkspaceOption)" />
            <x:Property Name="RunCodeAnalysis" Type="InArgument(mtbwa:CodeAnalysisOption)" />
            <x:Property Name="SourceAndSymbolServerSettings" Type="InArgument(mtbwa:SourceAndSymbolServerSettings)" />
            <x:Property Name="AgentSettings" Type="InArgument(mtbwa:AgentSettings)" />
            <x:Property Name="AssociateChangesetsAndWorkItems" Type="InArgument(x:Boolean)" />
            <x:Property Name="CreateWorkItem" Type="InArgument(x:Boolean)" />
            <x:Property Name="DropBuild" Type="InArgument(x:Boolean)" />
            <x:Property Name="MSBuildArguments" Type="InArgument(x:String)" />
            <x:Property Name="MSBuildPlatform" Type="InArgument(mtbwa:ToolPlatform)" />
            <x:Property Name="PerformTestImpactAnalysis" Type="InArgument(x:Boolean)" />
            <x:Property Name="CreateLabel" Type="InArgument(x:Boolean)" />
            <x:Property Name="DisableTests" Type="InArgument(x:Boolean)" />
            <x:Property Name="GetVersion" Type="InArgument(x:String)" />
            <x:Property Name="PrivateDropLocation" Type="InArgument(x:String)" />
            <x:Property Name="Verbosity" Type="InArgument(mtbw:BuildVerbosity)" />
            <x:Property Name="Metadata" Type="mtbw:ProcessParameterMetadataCollection" />
            <x:Property Name="SupportedReasons" Type="mtbc:BuildReason" />
            <x:Property Name="SubscriptionName" Type="InArgument(x:String)" />
            <x:Property Name="StorageAccountName" Type="InArgument(x:String)" />
            <x:Property Name="CloudConfigLocation" Type="InArgument(x:String)" />
            <x:Property Name="PackageLocation" Type="InArgument(x:String)" />
            <x:Property Name="Environment" Type="InArgument(x:String)" />
            <x:Property Name="SubscriptionDataFileLocation" Type="InArgument(x:String)" />
            <x:Property Name="PublishScriptLocation" Type="InArgument(x:String)" />
            <x:Property Name="ServiceName" Type="InArgument(x:String)" />
          </x:Members>

          <this:Process.MSBuildArguments>

6.  [エージェントで実行] の最後に新しいシーケンスを追加します。

    1.  最初に、有効なスクリプト ファイルの有無をチェックする If ステートメント アクティビティを追加します。条件を次の値に設定します。

            Not String.IsNullOrEmpty(PublishScriptLocation)

    2.  If ステートメントの Then ケースに、新しい Sequence アクティビティを追加します。表示名を "Start publish" に設定します。

    3.  "Start publish" シーケンスが選択されている状態で、次の新しい変数を、ワークフロー デザイナーの変数タブに別の行項目として追加します。すべての変数について、変数の型として [文字列]、スコープとして [Start publish] を設定する必要があります。これらは、ビルド定義のパラメーターを、発行スクリプトを呼び出すワークフローに送るために使用されます。

        -   SubscriptionDataFilePath、[文字列] 型

        -   PublishScriptFilePath、[文字列] 型

            ![][3]

    4.  ConvertWorkspaceItem アクティビティを新しいシーケンスの先頭に追加します(Direction=ServerToLocal、DisplayName='Convert publish script filename'、Input=' PublishScriptLocation'、 Result='PublishScriptFilePath'、Workspace='Workspace')。このアクティビティは、TFS サーバーの場所 (該当する場合) からの発行スクリプトへのパスを標準ローカル ディスク パスに変換します。

    5.  別の ConvertWorkspaceItem アクティビティを新しいシーケンスの末尾に追加します (Direction=ServerToLocal、DisplayName='Convert subscription filename'、Input=' SubscriptionDataFileLocation'、Result= 'SubscriptionDataFilePath'、Workspace='Workspace')。

    6.  InvokeProcess アクティビティを新しいシーケンスの末尾に追加します。このアクティビティは、ビルド定義から渡された引数を使用して PowerShell.exe を呼び出します。

        1.  Arguments = String.Format(" -File ""{0}"" -serviceName {1}
            -storageAccountName {2} -packageLocation ""{3}""
            -cloudConfigLocation ""{4}"" -subscriptionDataFile ""{5}""
            -selectedSubscription {6} -environment ""{7}""",
            PublishScriptFilePath, ServiceName, StorageAccountName,
            PackageLocation, CloudConfigLocation,
            SubscriptionDataFilePath, SubscriptionName, Environment)

        2.  DisplayName ='Execute publish script'

        3.  FileName = "PowerShell"

        4.  OutputEncoding=
            'System.Text.Encoding.GetEncoding(System.Globalization.CultureInfo.InstalledUICulture.TextInfo.OEMCodePage)'

    7.  InvokeProcess の [標準出力の処理] セクションのテキスト ボックスで、値を "data" に設定します。これは、標準出力データを格納する変数です。

    8.  標準出力セクションのすぐ下に WriteBuildMessage アクティビティを追加します。重要度として "Microsoft.TeamFoundation.Build.Client.BuildMessageImportance.High"、メッセージとして "data" を設定します。これにより、スクリプトの標準出力がビルド出力に書き込まれます。

    9.  InvokeProcess の [標準エラーの処理] セクションのテキスト ボックスで、値を "data" に設定します。これは、標準エラーのデータを格納する変数です。

    10. 標準出力セクションのすぐ下に WriteBuildError アクティビティを追加します。メッセージとして "data" を設定します。これにより、スクリプトの標準エラーがビルド エラー出力に書き込まれます。

    ワークフローの発行アクティビティの最終的な結果は、デザイナーで次のように表示されます。

    ![][4]

    ワークフローの発行アクティビティの最終的な結果は、XAML で次のように表示されます。

            </TryCatch>
              <If Condition="[Not String.IsNullOrEmpty(PublishScriptLocation)]" sap:VirtualizedContainerService.HintSize="1539,552">
                <If.Then>
                  <Sequence DisplayName="Start publish" sap:VirtualizedContainerService.HintSize="297,446">
                    <Sequence.Variables>
                      <Variable x:TypeArguments="x:String" Name="PublishScriptFilePath" />
                      <Variable x:TypeArguments="x:String" Name="SubscriptionDataFilePath" />
                    </Sequence.Variables>
                    <sap:WorkflowViewStateService.ViewState>
                      <scg:Dictionary x:TypeArguments="x:String, x:Object">
                        <x:Boolean x:Key="IsExpanded">True</x:Boolean>
                      </scg:Dictionary>
                    </sap:WorkflowViewStateService.ViewState>
                    <mtbwa:ConvertWorkspaceItem DisplayName="Convert publish script filename" sap:VirtualizedContainerService.HintSize="234,22" Input="[PublishScriptLocation]" Result="[PublishScriptFilePath]" Workspace="[Workspace]" />
                    <mtbwa:ConvertWorkspaceItem DisplayName="Convert subscription data file filename" sap:VirtualizedContainerService.HintSize="234,22" Input="[SubscriptionDataFileLocation]" Result="[SubscriptionDataFilePath]" Workspace="[Workspace]" />
                    <mtbwa:InvokeProcess Arguments="[String.Format(" -File ""{0}"" -serviceName {1} -storageAccountName {2} -packageLocation ""{3}"" -cloudConfigLocation ""{4}"" -subscriptionDataFile ""{5}"" -selectedSubscription {6} -environment ""{7}""", PublishScriptFilePath, ServiceName, StorageAccountName, PackageLocation, CloudConfigLocation, SubscriptionDataFilePath, SubscriptionName, Environment)]" DisplayName="Execute publish script" FileName="PowerShell" sap:VirtualizedContainerService.HintSize="234,198">
                      <mtbwa:InvokeProcess.ErrorDataReceived>
                        <ActivityAction x:TypeArguments="x:String">
                          <ActivityAction.Argument>
                            <DelegateInArgument x:TypeArguments="x:String" Name="data" />
                          </ActivityAction.Argument>
                          <mtbwa:WriteBuildError sap:VirtualizedContainerService.HintSize="200,22" Message="[data]" />
                        </ActivityAction>
                      </mtbwa:InvokeProcess.ErrorDataReceived>
                      <mtbwa:InvokeProcess.OutputDataReceived>
                        <ActivityAction x:TypeArguments="x:String">
                          <ActivityAction.Argument>
                            <DelegateInArgument x:TypeArguments="x:String" Name="data" />
                          </ActivityAction.Argument>
                          <mtbwa:WriteBuildMessage sap:VirtualizedContainerService.HintSize="200,22" Importance="[Microsoft.TeamFoundation.Build.Client.BuildMessageImportance.High]" Message="[data]" mva:VisualBasic.Settings="Assembly references and imported namespaces serialized as XML namespaces" />
                        </ActivityAction>
                      </mtbwa:InvokeProcess.OutputDataReceived>
                    </mtbwa:InvokeProcess>
                  </Sequence>
                </If.Then>
              </If>
            </mtbwa:AgentScope>
            <mtbwa:InvokeForReason DisplayName="Check In Gated Changes for CheckInShelveset Builds" sap:VirtualizedContainerService.HintSize="1370,146" Reason="CheckInShelveset">
              <mtbwa:CheckInGatedChanges DisplayName="Check In Gated Changes" sap:VirtualizedContainerService.HintSize="200,22" />
            </mtbwa:InvokeForReason>
          </Sequence>
        </Activity>

7.  DefaultTemplateAzure ワークフローを保存し、このファイルをチェックインします。

8.  ビルド定義内で DefaultTemplateAzure プロセス テンプレートを選択します。プロセス テンプレートの一覧にこのファイルが表示されていない場合は、[最新の情報に更新] をクリックするか、[新規] ボタンをクリックします。

9.  [その他] セクションのパラメーター プロパティ値を次のように設定します。

    1.  CloudConfigLocation =
        'c:\\drops\\app.publish\\ServiceConfiguration.Cloud.cscfg'
        *この値の派生元:
        ($PublishDir)ServiceConfiguration.Cloud.cscfg*

    2.  PackageLocation =
        'c:\\drops\\app.publish\\ContactManager.Azure.cspkg'
        *この値の派生元:($PublishDir)($ProjectName).cspkg*

    3.  PublishScriptLocation =
        'c:\\scripts\\WindowsAzure\\PublishCloudService.ps1'

    4.  ServiceName = 'mycloudservicename'
        *適切なクラウド サービス名を使用します*

    5.  Environment = 'Staging'

    6.  StorageAccountName = 'mystorageaccountname'
        *適切なストレージ アカウント名を使用します*

    7.  SubscriptionDataFileLocation =
        'c:\\scripts\\WindowsAzure\\Subscription.xml'

    8.  SubscriptionName = 'default'

    ![][5]

10. 変更内容をビルド定義に保存します。

11. パッケージのビルドと発行の両方を実行するために、ビルドをキューに配置します。トリガーを [継続的展開] に設定している場合、チェックインのたびにこの動作が実行されます。

### <a name="script"> </a>PublishCloudService.ps1 スクリプト テンプレート

    Param(  $serviceName = "",
            $storageAccountName = "",
            $packageLocation = "",
            $cloudConfigLocation = "",
            $environment = "Staging",
            $deploymentLabel = "ContinuousDeploy to $servicename",
            $timeStampFormat = "g",
            $alwaysDeleteExistingDeployments = 1,
            $enableDeploymentUpgrade = 1,
            $selectedsubscription = "default",
            $subscriptionDataFile = ""
         )
          

    function Publish()
    {
        $deployment = Get-AzureDeployment -ServiceName $serviceName -Slot $slot -ErrorVariable a -ErrorAction silentlycontinue 
        if ($a[0] -ne $null)
        {
            Write-Output "$(Get-Date -f $timeStampFormat) - No deployment is detected. Creating a new deployment. "
        }
        #check for existing deployment and then either upgrade, delete + deploy, or cancel according to $alwaysDeleteExistingDeployments and $enableDeploymentUpgrade boolean variables
        if ($deployment.Name -ne $null)
        {
            switch ($alwaysDeleteExistingDeployments)
            {
                1 
                {
                    switch ($enableDeploymentUpgrade)
                    {
                        1  #Update deployment inplace (usually faster, cheaper, won't destroy VIP)
                        {
                            Write-Output "$(Get-Date -f $timeStampFormat) - Deployment exists in $servicename.  Upgrading deployment."
                            UpgradeDeployment
                        }
                        0  #Delete then create new deployment
                        {
                            Write-Output "$(Get-Date -f $timeStampFormat) - Deployment exists in $servicename.  Deleting deployment."
                            DeleteDeployment
                            CreateNewDeployment
                            
                        }
                    } # switch ($enableDeploymentUpgrade)
                }
                0
                {
                    Write-Output "$(Get-Date -f $timeStampFormat) - ERROR: Deployment exists in $servicename.  Script execution cancelled."
                    exit
                }
            } #switch ($alwaysDeleteExistingDeployments)
    } else {
                CreateNewDeployment
        }
    }

    function CreateNewDeployment()
    {
        write-progress -id 3 -activity "Creating New Deployment" -Status "In progress"
        Write-Output "$(Get-Date -f $timeStampFormat) - Creating New Deployment: In progress"

        $opstat = New-AzureDeployment -Slot $slot -Package $packageLocation -Configuration $cloudConfigLocation -label $deploymentLabel -ServiceName $serviceName
            
        $completeDeployment = Get-AzureDeployment -ServiceName $serviceName -Slot $slot
        $completeDeploymentID = $completeDeployment.deploymentid

        write-progress -id 3 -activity "Creating New Deployment" -completed -Status "Complete"
        Write-Output "$(Get-Date -f $timeStampFormat) - Creating New Deployment: Complete, Deployment ID: $completeDeploymentID"
        
        StartInstances
    }

    function UpgradeDeployment()
    {
        write-progress -id 3 -activity "Upgrading Deployment" -Status "In progress"
        Write-Output "$(Get-Date -f $timeStampFormat) - Upgrading Deployment: In progress"

        # perform Update-Deployment
        $setdeployment = Set-AzureDeployment -Upgrade -Slot $slot -Package $packageLocation -Configuration $cloudConfigLocation -label $deploymentLabel -ServiceName $serviceName -Force
        
        $completeDeployment = Get-AzureDeployment -ServiceName $serviceName -Slot $slot
        $completeDeploymentID = $completeDeployment.deploymentid
        
        write-progress -id 3 -activity "Upgrading Deployment" -completed -Status "Complete"
        Write-Output "$(Get-Date -f $timeStampFormat) - Upgrading Deployment: Complete, Deployment ID: $completeDeploymentID"
    }

    function DeleteDeployment()
    {

        write-progress -id 2 -activity "Deleting Deployment" -Status "In progress"
        Write-Output "$(Get-Date -f $timeStampFormat) - Deleting Deployment: In progress"

        #WARNING - always deletes with force
        $removeDeployment = Remove-AzureDeployment -Slot $slot -ServiceName $serviceName -Force

        write-progress -id 2 -activity "Deleting Deployment: Complete" -completed -Status $removeDeployment
        Write-Output "$(Get-Date -f $timeStampFormat) - Deleting Deployment: Complete"
        
    }

    function StartInstances()
    {
        write-progress -id 4 -activity "Starting Instances" -status "In progress"
        Write-Output "$(Get-Date -f $timeStampFormat) - Starting Instances: In progress"

        $deployment = Get-AzureDeployment -ServiceName $serviceName -Slot $slot
        $runstatus = $deployment.Status

        if ($runstatus -ne 'Running') 
        {
            $run = Set-AzureDeployment -Slot $slot -ServiceName $serviceName -Status Running
        }
        $deployment = Get-AzureDeployment -ServiceName $serviceName -Slot $slot
        $oldStatusStr = @("") * $deployment.RoleInstanceList.Count
        
        while (-not(AllInstancesRunning($deployment.RoleInstanceList)))
        {
            $i = 1
            foreach ($roleInstance in $deployment.RoleInstanceList)
            {
                $instanceName = $roleInstance.InstanceName
                $instanceStatus = $roleInstance.InstanceStatus

                if ($oldStatusStr[$i - 1] -ne $roleInstance.InstanceStatus)
                {
                    $oldStatusStr[$i - 1] = $roleInstance.InstanceStatus
                    Write-Output "$(Get-Date -f $timeStampFormat) - Starting Instance '$instanceName': $instanceStatus"
                }

                write-progress -id (4 + $i) -activity "Starting Instance '$instanceName'" -status "$instanceStatus"
                $i = $i + 1
            }

            sleep -Seconds 1

            $deployment = Get-AzureDeployment -ServiceName $serviceName -Slot $slot
        }

        $i = 1
        foreach ($roleInstance in $deployment.RoleInstanceList)
        {
            $instanceName = $roleInstance.InstanceName
            $instanceStatus = $roleInstance.InstanceStatus

            if ($oldStatusStr[$i - 1] -ne $roleInstance.InstanceStatus)
            {
                $oldStatusStr[$i - 1] = $roleInstance.InstanceStatus
                Write-Output "$(Get-Date -f $timeStampFormat) - Starting Instance '$instanceName': $instanceStatus"
            }

            $i = $i + 1
        }
        
        $deployment = Get-AzureDeployment -ServiceName $serviceName -Slot $slot
        $opstat = $deployment.Status 
        
        write-progress -id 4 -activity "Starting Instances" -completed -status $opstat
        Write-Output "$(Get-Date -f $timeStampFormat) - Starting Instances: $opstat"
    }

    function AllInstancesRunning($roleInstanceList)
    {
        foreach ($roleInstance in $roleInstanceList)
        {
            if ($roleInstance.InstanceStatus -ne "ReadyRole")
            {
                return $false
            }
        }
        
        return $true
    }

    #configure powershell with Azure 1.7 modules
    Import-Module Azure

    #configure powershell with publishsettings for your subscription
    $pubsettings = $subscriptionDataFile
    Import-AzurePublishSettingsFile $pubsettings
    Set-AzureSubscription -CurrentStorageAccount $storageAccountName -SubscriptionName $selectedsubscription

    #set remaining environment variables for Azure cmdlets
    $subscription = Get-AzureSubscription $selectedsubscription
    $subscriptionname = $subscription.subscriptionname
    $subscriptionid = $subscription.subscriptionid
    $slot = $environment

    #main driver - publish & write progress to activity log
    Write-Output "$(Get-Date -f $timeStampFormat) - Azure Cloud Service deploy script started."
    Write-Output "$(Get-Date -f $timeStampFormat) - Preparing deployment of $deploymentLabel for $subscriptionname with Subscription ID $subscriptionid."

    Publish

    $deployment = Get-AzureDeployment -slot $slot -serviceName $servicename
    $deploymentUrl = $deployment.Url

    Write-Output "$(Get-Date -f $timeStampFormat) - Created Cloud Service with URL $deploymentUrl."
    Write-Output "$(Get-Date -f $timeStampFormat) - Azure Cloud Service deploy script finished."

## 次のステップ

継続的な配信を使用する場合にリモート デバッガーを有効にするには、[これらの手順][これらの手順]を参照してください。

  [Continuous Delivery to Azure by Using Visual Studio Online (Visual Studio Online を使用した Azure への継続的な配信)]: ../cloud-services-continuous-delivery-use-vso/
  [手順 1.ビルド サーバーを構成する]: #step1
  [手順 2.MSBuild コマンドを使用してパッケージをビルドする]: #step2
  [手順 3.TFS チーム ビルドを使用してパッケージをビルドする (省略可能)]: #step3
  [手順 4.PowerShell スクリプトを使用してパッケージを発行する]: #step4
  [手順 5.TFS チーム ビルドを使用してパッケージを発行する (省略可能)]: #step5
  [Team Foundation ビルド サービス]: http://go.microsoft.com/fwlink/p/?LinkId=239963
  [.NET Framework 4]: http://go.microsoft.com/fwlink/?LinkId=239538
  [.NET Framework 4.5]: http://go.microsoft.com/fwlink/?LinkId=245484
  [Azure Authoring Tools]: http://go.microsoft.com/fwlink/?LinkId=239600
  [Azure Libraries]: http://go.microsoft.com/fwlink/?LinkId=257862
  [MSBuild コマンド ライン リファレンスのページ]: http://msdn.microsoft.com/ja-jp/library/ms164311(v=VS.90).aspx
  [MSBuild コマンド ライン リファレンスのページ]: http://go.microsoft.com/fwlink/p/?LinkId=239966
  [Team Foundation ビルド システムについて]: http://go.microsoft.com/fwlink/?LinkId=238798
  [ビルド コンピューターの構成]: http://go.microsoft.com/fwlink/?LinkId=238799
  []: ./media/cloud-services-dotnet-continuous-delivery/tfs-01.png
  [1]: ./media/cloud-services-dotnet-continuous-delivery/tfs-02.png
  [Azure PowerShell コマンドレット]: http://go.microsoft.com/fwlink/?LinkId=256262
  [この記事の末尾]: #script
  [2]: ./media/cloud-services-dotnet-continuous-delivery/common-task-tfs-03.png
  [3]: ./media/cloud-services-dotnet-continuous-delivery/common-task-tfs-04.png
  [4]: ./media/cloud-services-dotnet-continuous-delivery/common-task-tfs-05.png
  [5]: ./media/cloud-services-dotnet-continuous-delivery/common-task-tfs-06.png
  [これらの手順]: http://go.microsoft.com/fwlink/p/?LinkID=402354
