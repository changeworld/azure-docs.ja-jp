<properties linkid="websites-dotnet-deploy-webjobs" urlDisplayName="Deploy Azure WebJobs to Azure Websites" pageTitle="Deploy Azure WebJobs to Azure Websites" metaKeywords="Azure Websites, WebJobs, background tasks" description="Learn how to deploy Azure WebJobs to Azure Websites using Visual Studio." metaCanonical="" services="web-sites" documentationCenter="" title="Deploy Azure WebJobs to Azure Websites" authors="tdykstra"  solutions="" manager="wpickett" editor="mollybos"  />

# Azure Web ジョブの Azure Web サイトへのデプロイ方法

このトピックでは、Visual Studio を使用して、コンソール アプリケーション プロジェクトを [Azure Web ジョブ][Azure Web ジョブ]として Azure Web サイトにデプロイする方法を説明します。Web ジョブをデプロイする代替の方法は、Azure 管理ポータルを使用することです。この方法の詳細については、「[Microsoft Azure Web Sites で Web ジョブ機能を使用する方法][Microsoft Azure Web Sites で Web ジョブ機能を使用する方法]」を参照してください。

## 目次

-   [Overview][Overview]
-   [前提条件][前提条件]
-   [既存のコンソール アプリケーション プロジェクトで Web ジョブのデプロイメントを有効にする][既存のコンソール アプリケーション プロジェクトで Web ジョブのデプロイメントを有効にする]
-   [新しい Web ジョブ対応のプロジェクトを作成する][新しい Web ジョブ対応のプロジェクトを作成する]
-   [Azure Web ジョブの追加ダイアログ]
-   [webjob-publish-settings.json ファイル][webjob-publish-settings.json ファイル]
-   [webjobs-list.json ファイル][webjobs-list.json ファイル]
-   [Web ジョブ プロジェクトをデプロイする][Web ジョブ プロジェクトをデプロイする]
-   [次のステップ][次のステップ]

## <span id="overview"></span></a>概要

Visual Studio では Web ジョブ対応のコンソール アプリケーション プロジェクトをデプロイする場合に、2 つのタスクを実行します。

-   ランタイム ファイルを Azure Web サイトの適切なフォルダーにコピーします (継続的な Web ジョブの場合は *App\_Data/jobs/continuous*、スケジュールされたオンデマンドの Web ジョブの場合は *App\_Data/jobs/triggered*)。
-   特定の時間に実行するようにスケジュールされる Web ジョブに [Azure Scheduler ジョブ][Azure Scheduler ジョブ]を設定します (これは継続的な Web ジョブでは必要ありません)。

Web ジョブ対応のプロジェクトでは、次の項目が追加されています。

-   [Microsoft.Web.WebJobs.Publish][Microsoft.Web.WebJobs.Publish] NuGet パッケージ。
-   デプロイメントとスケジューラの設定を含む [webjob-publish-settings.json][webjob-publish-settings.json ファイル] ファイル。

![Diagram showing what is added to a Console App to enable deployment as a WebJob][Diagram showing what is added to a Console App to enable deployment as a WebJob]

これらの項目を既存のコンソール アプリケーション プロジェクトに追加することもできれば、テンプレートを使用して新しい Web ジョブ対応のコンソール アプリケーション プロジェクトを作成することもできます。

プロジェクト自体を Web ジョブとしてデプロイできます。また、Web プロジェクトをデプロイしたときに常に自動的にデプロイされるように、プロジェクトを Web プロジェクトにリンクすることもできます。プロジェクトをリンクする場合、Visual Studio で、Web プロジェクト内の [webjobs-list.json][webjobs-list.json ファイル] ファイルに Web ジョブ対応のプロジェクトの名前が含められます。

![Diagram showing WebJob project linking to web project][Diagram showing WebJob project linking to web project]

## 前提条件

Web ジョブのデプロイメント機能は、Azure SDK バージョン 2.4 以降をインストールしている場合に、Visual Studio 2013 で使用できます。

-   [Azure SDK for Visual Studio 2013][Azure SDK for Visual Studio 2013]

Web ジョブのデプロイメント機能は、[更新プログラム 3][更新プログラム 3] にも含まれています。

## <span id="convert"></span></a>既存のコンソール アプリケーション プロジェクトで Web ジョブのデプロイメントを有効にする

2 つのオプションがあります。

-   [Web プロジェクトを使用した自動デプロイメントを有効にする][Web プロジェクトを使用した自動デプロイメントを有効にする]。

    Web プロジェクトをデプロイしたときに自動的に Web ジョブとしてデプロイされるように、既存のコンソール アプリケーション プロジェクトを構成します。Web ジョブを、関連する Web アプリケーションが実行する Web サイトと同じサイトで実行する場合に、このオプションを使用します。

-   [Web プロジェクトなしでデプロイメントを有効にする][Web プロジェクトなしでデプロイメントを有効にする]。

    Web プロジェクトへリンクせず、既存のコンソール アプリケーション プロジェクトがそれ自身で Web ジョブとしてデプロイされるように構成します。Web サイトで Web アプリケーションを実行せずに、Web ジョブ自身によって Web サイト内で実行されようにする場合に、このオプションを使用します。このオプションは、Web アプリケーション リソースとは関係なく Web ジョブ リソースを拡張する必要がある場合などに使用できます。

### <span id="convertlink"></span></a>Web プロジェクトを使用した Web ジョブの自動デプロイメントを有効にする

1.  **ソリューション エクスプローラー**で Web プロジェクトを右クリックし、**[追加]**、**[既存のプロジェクトを Azure Web ジョブとして]** の順にクリックします。

    ![Existing Project as Azure WebJob][Existing Project as Azure WebJob]

    [Azure Web ジョブの追加][Azure Web ジョブの追加ダイアログ] ダイアログボックスが表示されます。

2.  **[プロジェクト名]** ボックスの一覧で、コンソール アプリケーション プロジェクトを選択し、Web ジョブとして追加します。

    ![Selecting project in Add Azure WebJob dialog][Selecting project in Add Azure WebJob dialog]

3.  [Azure Web ジョブの追加][Azure Web ジョブの追加ダイアログ] ダイアログで必要な設定を完了し、**[OK]** をクリックします。

### <span id="convertnolink"></span></a>Web プロジェクトなしで Web ジョブ デプロイメントを有効にする

1.  **ソリューション エクスプローラー**でコンソール アプリケーション プロジェクトを右クリックし、**[Azure Web ジョブとして発行]** をクリックします。

    ![Publish as Azure WebJob][Publish as Azure WebJob]

    [Azure Web ジョブの追加][Azure Web ジョブの追加ダイアログ] ダイアログボックスが表示され、**[プロジェクト名]** ボックスに選択されたプロジェクトが示されます。

2.  [Azure Web ジョブの追加][Azure Web ジョブの追加ダイアログ] ダイアログボックスで必要な設定を完了し、**[OK]** をクリックします。

    **Web の発行**ウィザードが表示されます。すぐに発行しない場合は、ウィザードを閉じます。[プロジェクトをデプロイする][Web ジョブ プロジェクトをデプロイする]ときのために、入力した設定値は保存されます。

## <span id="create"></span></a>新しい Web ジョブ対応のプロジェクトを作成する

新しい Web ジョブ対応のプロジェクトを作成するために、[前のセクション][既存のコンソール アプリケーション プロジェクトで Web ジョブのデプロイメントを有効にする]で説明したように、コンソール アプリケーション プロジェクト テンプレートを使用して、Web ジョブのデプロイメントを有効にすることができます。または、次のように Web ジョブの新しいプロジェクト テンプレートを使用できます。

-   [Web プロジェクトにリンクされた Web ジョブ用に Web ジョブの新しいプロジェクト テンプレートを使用する][Web プロジェクトにリンクされた Web ジョブ用に Web ジョブの新しいプロジェクト テンプレートを使用する]

    同じソリューション内の Web プロジェクトがデプロイされたときに Web ジョブとして自動的にデプロイされるように構成して、プロジェクトを作成します。Web ジョブを、関連する Web アプリケーションが実行する Web サイトと同じサイトで実行する場合に、このオプションを使用します。

-   [独立した Web ジョブ用に Web ジョブの新しいプロジェクト テンプレートを使用する][独立した Web ジョブ用に Web ジョブの新しいプロジェクト テンプレートを使用する]

    プロジェクトを作成し、Web プロジェクトへはリンクせずに、それ自身で Web ジョブとしてデプロイされるように構成します。Web サイトで Web アプリケーションを実行せずに、Web ジョブ自身によって Web サイト内で実行されようにする場合に、このオプションを使用します。このオプションは、Web アプリケーション リソースとは関係なく Web ジョブ リソースを拡張する必要がある場合などに使用できます。

SDK 2.4 リリースでは、Web ジョブの新しいプロジェクト テンプレートは、コンソール アプリケーション プロジェクトを作成して Web ジョブのデプロイメントを有効にする方法と比較して、それほど簡単ではありません。将来的には、Web ジョブの新しいプロジェクト テンプレートは、自動的に適切な Web ジョブ SDK NuGet パッケージをインストールするようになり、[Web ジョブ SDK][Web ジョブ SDK] 開発において今より使いやすくなります。それまでは、[Web ジョブ SDK のチュートリアル][Web ジョブ SDK]に示すように、手動でパッケージをインストールすることで Web ジョブ SDK を使用するようプロジェクトを構成できます。

### <span id="createlink"></span></a>Web プロジェクトにリンクされた Web ジョブ用に Web ジョブの新しいプロジェクト テンプレートを使用する

-   **ソリューション エクスプローラー**で Web プロジェクトを右クリックし、**[追加]**、**[新しい Azure Web ジョブ プロジェクト]** の順にクリックします。

    ![New Azure WebJob Project menu entry][New Azure WebJob Project menu entry]

    [Azure Web ジョブの追加][Azure Web ジョブの追加ダイアログ] ダイアログボックスが表示されます。

-   [Azure Web ジョブの追加][Azure Web ジョブの追加ダイアログ] ダイアログボックスで必要な設定を完了し、**[OK]** をクリックします。

### <span id="createnolink"></span></a>独立した Web ジョブ用に Web ジョブの新しいプロジェクト テンプレートを使用する

1.  **[ファイル]**、**[新しいプロジェクト]** の順にクリックし、**[新しいプロジェクト]** ダイアログ ボックスで **[クラウド]**、**[Microsoft Azure Web ジョブ]** の順にクリックします。

    ![New Project dialog showing WebJob template][New Project dialog showing WebJob template]

2.  前に示された指示に従い、[コンソール アプリケーション プロジェクトを独立した Web ジョブ プロジェクトにします][Web プロジェクトなしでデプロイメントを有効にする]。

## <span id="configure"></span></a>[Azure Web ジョブの追加] ダイアログ

**[Azure Web ジョブの追加]** ダイアログを使用して、Web ジョブの Web ジョブ名とスケジュール設定を入力できます。

![Add Azure WebJob dialog][Add Azure WebJob dialog]

このダイアログのフィールドは、Azure の管理ポータルの **[新しいジョブ]** ダイアログのフィールドに対応しています。詳細については、「[Microsoft Azure Web Sites で Web ジョブ機能を使用する方法][Microsoft Azure Web Sites で Web ジョブ機能を使用する方法]」を参照してください。

スケジュールされた Web ジョブの場合 (継続的な Web ジョブではなく)、Visual Studio は [Azure Scheduler][Azure Scheduler] ジョブ コレクションが存在しない場合はコレクションを作成し、コレクション内にジョブを作成します。

-   スケジューラ ジョブ コレクションには *WebJobs-{regionname}* という名前が付けられます。ここで、*{regionname}* は Web サイトがホストされているリージョンを指しています。例: WebJobs-WestUS。
-   スケジューラ ジョブには *{websitename}-{webjobname}* という名前が付けられます。次に例を示します。MyWebSite-MyWebJob。

> [WACOM.NOTE]
>
> -   MSBuild を使用したコマンドライン デプロイメントでは、スケジュールされた Web ジョブのスケジュールが設定されません。この機能は、Visual Studio を使用してデプロイする場合にのみ動作します。
> -   **定期的なジョブ**を構成して、繰り返しの頻度を分単位で設定した場合、Azure Scheduler サービスは無料ではなくなります。その他の頻度 (時間、日など) では無料です。
> -   Web ジョブをデプロイし、後から実行モードを継続的から継続的以外、またはその逆に変更した場合、Visual Studio は再デプロイ時に Azure 内に新しい Web ジョブを作成します。その他のスケジュール設定を変更し、実行モードを同じままにするか、または [スケジュール済み] と [オンデマンド] の間で切り替えた場合は、Visual Studio は新しいジョブを作成するのではなく、既存のジョブを更新します。

## <span id="publishsettings"></span></a>webjob-publish-settings.json

Web ジョブのデプロイメント用にコンソール アプリケーションを構成すると、Visual Studio は [Microsoft.Web.WebJobs.Publish][Microsoft.Web.WebJobs.Publish] NuGet パッケージ
をインストールし、Web プロジェクトの *Properties* フォルダーにある *webjob-publish-settings.json* ファイルにスケジュール情報を格納します。次にこのファイルの例を示します。

        {
          "$schema": "http://schemastore.org/schemas/json/webjob-publish-settings.json",
          "webJobName": "WebJob1",
          "startTime": "2014-06-23T00:00:00-08:00",
          "endTime": "2014-06-27T00:00:00-08:00",
          "jobRecurrenceFrequency": "Minute",
          "interval": 5,
          "runMode": "Scheduled"
        }

このファイルは直接編集でき、Visual Studio で IntelliSense を使用できます。ファイル スキーマは [][]<http://schemastore.org></a> に格納され、そこで表示できます。

> [WACOM.NOTE]
>
> -   **定期的なジョブ**を構成して、繰り返しの頻度を分単位で設定した場合、Azure Scheduler サービスは無料ではなくなります。その他の頻度 (時間、日など) では無料です。

## <span id="webjobslist"></span></a>webjobs-list.json

Web ジョブ対応のプロジェクトを Web プロジェクトにリンクすると、Visual Studio は Web ジョブ プロジェクトの名前を Web プロジェクトの *Properties* フォルダー内の *webjobs-list.json* ファイルに格納します。次の例に示すように、一覧には複数の Web ジョブ プロジェクトが含まれる場合があります。

        {
          "$schema": "http://schemastore.org/schemas/json/webjobs-list.json",
          "WebJobs": [
            {
              "filePath": "../ConsoleApplication1/ConsoleApplication1.csproj"
            },
            {
              "filePath": "../WebJob1/WebJob1.csproj"
            }
          ]
        }

このファイルは直接編集でき、Visual Studio で IntelliSense を使用できます。ファイル スキーマは [][1]<http://schemastore.org></a> に格納され、そこで表示できます。

## <span id="deploy"></span></a>Web ジョブ プロジェクトをデプロイする

Web プロジェクトにリンクされた Web ジョブ プロジェクトは、Web プロジェクトと共に自動的にデプロイされます。Web プロジェクトのデプロイメントの詳細については、「[Azure の Web サイトをデプロイする方法][Azure の Web サイトをデプロイする方法]」を参照してください。

Web ジョブ プロジェクトがそれ自身でデプロイされるように指定するには、**ソリューション エクスプローラー**でプロジェクトを右クリックし、**[Azure Web ジョブとして発行]** をクリックします。

![Publish as Azure WebJob][Publish as Azure WebJob]

独立した Web ジョブの場合は、Web プロジェクトで使用されたウィザードと同じ **Web の発行**ウィザードが表示されますが、変更可能な設定値の数は少なくなります。

## <span id="nextsteps"></span></a> 次のステップ

Azure Web ジョブと Web ジョブ SDK の詳細については、「[Azure Web Jobs Recommended Resources (Azure Web ジョブの推奨リソース)][Azure Web Jobs Recommended Resources (Azure Web ジョブの推奨リソース)]」を参照してください。

  [Azure Web ジョブ]: http://go.microsoft.com/fwlink/?LinkId=390226
  [Microsoft Azure Web Sites で Web ジョブ機能を使用する方法]: /ja-jp/documentation/articles/web-sites-create-web-jobs/
  [Overview]: #overview
  [前提条件]: #prerequisites
  [既存のコンソール アプリケーション プロジェクトで Web ジョブのデプロイメントを有効にする]: #convert
  [新しい Web ジョブ対応のプロジェクトを作成する]: #create
  [Azure Web ジョブの追加ダイアログ]: #configure
  [webjob-publish-settings.json ファイル]: #publishsettings
  [webjobs-list.json ファイル]: #webjobslist
  [Web ジョブ プロジェクトをデプロイする]: #deploy
  [次のステップ]: #next-steps
  [Azure Scheduler ジョブ]: #scheduler
  [Microsoft.Web.WebJobs.Publish]: http://www.nuget.org/packages/Microsoft.Web.WebJobs.Publish/
  [Diagram showing what is added to a Console App to enable deployment as a WebJob]: ./media/websites-dotnet-deploy-webjobs/convert.png
  [Diagram showing WebJob project linking to web project]: ./media/websites-dotnet-deploy-webjobs/link.png
  [Azure SDK for Visual Studio 2013]: http://go.microsoft.com/fwlink/p/?linkid=323510&clcid=0x409
  [更新プログラム 3]: http://go.microsoft.com/fwlink/?linkid=397827&clcid=0x409
  [Web プロジェクトを使用した自動デプロイメントを有効にする]: #convertlink
  [Web プロジェクトなしでデプロイメントを有効にする]: #convertnolink
  [Existing Project as Azure WebJob]: ./media/websites-dotnet-deploy-webjobs/eawj.png
  [Selecting project in Add Azure WebJob dialog]: ./media/websites-dotnet-deploy-webjobs/aaw1.png
  [Publish as Azure WebJob]: ./media/websites-dotnet-deploy-webjobs/paw.png
  [Web プロジェクトにリンクされた Web ジョブ用に Web ジョブの新しいプロジェクト テンプレートを使用する]: #createlink
  [独立した Web ジョブ用に Web ジョブの新しいプロジェクト テンプレートを使用する]: #createnolink
  [Web ジョブ SDK]: http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/getting-started-with-windows-azure-webjobs
  [New Azure WebJob Project menu entry]: ./media/websites-dotnet-deploy-webjobs/nawj.png
  [New Project dialog showing WebJob template]: ./media/websites-dotnet-deploy-webjobs/np.png
  [Add Azure WebJob dialog]: ./media/websites-dotnet-deploy-webjobs/aaw2.png
  [Azure Scheduler]: /ja-jp/services/scheduler/
  []: http://schemastore.org/schemas/json/webjob-publish-settings.json
  [1]: http://schemastore.org/schemas/json/webjobs-list.json
  [Azure の Web サイトをデプロイする方法]: /ja-jp/documentation/articles/websites-dotnet-deploy/
  [Azure Web Jobs Recommended Resources (Azure Web ジョブの推奨リソース)]: http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/azure-webjobs-recommended-resources
