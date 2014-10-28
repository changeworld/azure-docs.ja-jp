<properties linkid="manage-services-how-to-use-appdynamics" urlDisplayName="Monitor with AppDynamics" pageTitle="How to use AppDynamics with Azure" metaKeywords="" description="Learn how to use AppDynamics for Azure." metaCanonical="" services="cloud-services" documentationCenter="" title="How To Use AppDynamics for Azure" authors="ryanwi" solutions="" manager="timlt" editor="" />

<tags ms.service="cloud-services" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="ryanwi"></tags>

# Azure における AppDynamics の使用方法

このトピックでは、Azure 用の AppDynamics の基本的な使用方法について説明しています。

## 目次

-   [AppDynamics とは](#what)
-   [前提条件](#prereq)
-   [AppDynamics アカウントを登録する](#register)
-   [AppDynamics から .NET エージェントをダウンロードする](#download)
-   [.NET エージェントを Azure のロールに追加してスタートアップを変更する](#addagent)
-   [AppDynamics でインストルメント化されたアプリケーションを Azure に発行する](#publish)
-   [アプリケーションを監視する](#monitor)

## <span id="what"></span></a>AppDynamics とは

AppDynamics は、アプリケーション パフォーマンス監視ソリューションで、次のような用途があります。

-   運用環境で、動作が遅くストールしたユーザー リクエストやエラーのような問題を特定する

-   そのような問題の根本原因を解明して特定する

AppDynamics にはコンポーネントが 2 つあります。

-   アプリケーション サーバー エージェント: アプリケーション サーバー .NET エージェントがサーバーからデータを収集します。監視するあらゆるロール インスタンス上で別々のエージェントを実行します。AppDynamics ポータルからこのエージェントをダウンロードできます。

-   AppDynamics コントローラー: エージェントは、Azure 上の AppDynamics コントローラー ホステッド サービスに情報を送信します。Web ブラウザー ベースのコンソールを使用して、コントローラーにログインし、アプリケーションの監視、分析、および問題解決を実行します。

    ![AppDynamics の図](./media/cloud-services-how-to-appdynamics/addiagram.png)

## <span id="prereq"></span></a>前提条件

-   Visual Studio 2010 またはそれ以降
-   監視対象の Visual Studio ソリューション
-   Azure SDK
-   Azure アカウント

## <span id="register"></span></a>AppDynamics アカウントを登録する

Azure アカウント用の AppDynamics を登録するには、次に示している手順を実行します。

1.  Azure Marketplace ([https://datamarket.azure.com/browse/Applications](https://datamarket.azure.com/browse/Applications)) で AppDynamics の **[無料試用]** または **[サインアップ]** をクリックします。

    **[サインアップ]** をクリックした場合、Azure 用の AppDynamics Pro の無料版を受け取ります。機能は制限されておらず、30 日後に機能が制限された Azure 用の AppDynamics Lite の無料版にダウングレードされます。この場合、クレジット カード番号を入力する必要はありません。いつでも Azure 用の AppDynamics Pro にアップグレードできます。

    **[無料試用]** をクリックした場合、Azure 用の AppDynamics Pro の無料版を受け取ります。機能は制限されていません。この場合、クレジット カード番号を入力する必要があります。サブスクリプションを取り消さない限り、30 日後、Azure 用の AppDynamics Pro の継続使用に関してクレジット アカウントに課金されます。

    監視するロール インスタンスごとに 1 つエージェント ライセンスが必要です。たとえば、2 つの Web ロール インスタンスと 2 つの woker ロール インスタンスを実行するサイトはエージェント ライセンスが 4 つ必要です。

2.  登録ページで、ユーザーの情報、パスワード、メール アドレス、会社名、および監視するアプリケーションの名前を指定します。このアプリケーションは Azure を使用して発行します。

3.  **[今すぐ登録]** をクリックします。

    AppDynamics 資格情報およびアカウントに割り当てられた AppDynamics コントローラーの URL (ホストとポート) が、サインアップ ページで指定したメール アドレスに送信されます。この情報は保存してください。

    別の製品で既に AppDynamics 資格情報を持っている場合は、それを使用してサインインすることができます。

    AppDynamics アカウントのホーム ページも提供されます。

    AppDynamics アカウントの自分のホーム ページが表示されます。

    AppDynamics アカウントのホーム ページには以下が含まれます。

    -   コントローラー URL: AppDynamics コントローラー ホステッド サービスのアカウントにログインする URL

    -   AppDynamics 資格情報: アカウント名とアクセス キー

    -   AppDynamics ダウンロード サイトへのリンク: AppDynamics .NET エージェントをダウンロードするためのリンク

    -   Pro 試用版の残り日数

    -   AppDynamics の入門ビデオおよびマニュアルへのリンク

    AppDynamics アカウントのホーム ページは、Web ブラウザーで URL を入力し、AppDynamics 資格情報を使用してサインインすると、いつでもアクセスできます。

## <span id="download"></span></a>AppDynamics から .NET エージェントをダウンロードする

1.  AppDynamics ダウンロード サイトに移動します。URL は、ウェルカム メールおよび AppDynamics アカウントのホーム ページに記載されています。

2.  AppDynamics のアカウント名とアクセス キーを使用してログインします。

3.  AppDynamicsdotNetAgentSetup64.msi という名前のファイルをダウンロードします。実行はしないでください。

## <span id="addagent"></span></a>.NET エージェントを Azure のロールに追加してスタートアップを変更する

この手順では、Visual Studio ソリューションのロールに AppDynamics による監視を設定します。Azure 用の AppDynamics を使用するにあたって、従来のような Windows のウィザード式インストール手順は必要ありません。

1.  Visual Studio で新しい Azure プロジェクトを作成するか、既存の Azure プロジェクトを開きます。

2.  新しいプロジェクトを作成した場合は、ソリューションに Web ロールまたはワーカー ロールのプロジェクトを追加します。

3.  監視する Web およびワーカー ロール プロジェクトごとに、ダウンロードした .NET エージェント .msi ファイルを追加します。

    各*ロール プロジェクト*には 1 つの .NET エージェント .msi がアタッチされているだけですが、プロジェクトの各*ロール インスタンス*には別々の .NET エージェント ライセンスが必要なことに注意してください。

4.  監視する Web およびワーカー ロール プロジェクトごとに、startup.cmd という名前のテキスト ファイルを追加し、以下の行を貼り付けます。

        if defined COR_PROFILER GOTO END 
        SETLOCAL EnableExtensions 
        REM Run the agent installer 
        AppDynamicsdotNetAgentSetup64.msi AD_Agent_Environment=Azure AD_Agent_ControllerHost=%1 AD_Agent_ControllerPort=%2 AD_Agent_AccountName=%3 AD_Agent_AccessKey=%4 AD_Agent_ControllerApplication=%5 /quiet /log d:\adInstall.log  
        SHUTDOWN /r /c "Rebooting the instance after the installation of AppDynamics Monitoring Agent" /t 0 
        GOTO END   
        :END

5.  監視する Web および worker ロールごとに、AppDynamics エージェント .msi ファイルおよび startup.cmd ファイルの **[出力ディレクトリにコピー]** プロパティを **[常にコピーする]** に設定します。

    ![常にコピーする](./media/cloud-services-how-to-appdynamics/adcopyalways.png)

6.  Azure プロジェクトの ServiceDefinition.csdef ファイルで、各 WorkerRole および WebRole 要素に、startup.cmd を起動する Startup Task 要素をパラメーターと共に追加します。

    次の行を追加します。

        <Startup>
        <Task commandLine="startup.cmd [your_controller_host] [your_controller_port] [your_account_name] [your_access_key] [your_application_name]" executionContext="elevated" taskType="simple"/>
        </Startup>

    各値の説明:

    -   *your\_controller\_host* および *your\_controller\_port* は、アカウントに割り当てられたコントローラーのホストとポートで、*your\_account\_name* および *your\_access\_key* は、AppDynamics によって割り当てられた資格情報です。この情報は、AppDynamics に登録したときに受け取ったメールおよび AppDynamic のホーム ページに記載されています。「[AppDynamics アカウントを登録する](#register)」を参照してください。

    -   *your\_application\_name* はアプリケーションに付ける名前です。この名前は、AppDynamics コントローラーの操作画面でアプリケーションを識別するために使用されます。

    ServiceDefinition.csdef ファイルは次のようになります。

    ![サービス定義](./media/cloud-services-how-to-appdynamics/adscreen.png)

## <a name="publish"></a>AppDynamics でインストルメント化されたアプリケーションを Azure に発行する

AppDynamics でインストルメント化されたロール プロジェクトごとに、以下の手順を実行します。

1.  Visual Studio で、ロール プロジェクトを選択します。

2.  [発行] をクリックして、Azure に発行します。

## <a name="monitor"></a>アプリケーションを監視する

1.  ウェルカム メールおよび AppDynamics アカウントのホーム ページに記載された URL を使用して AppDynamics にログインします。

2.  監視するトラフィックが生じるように、アプリケーションにリクエストを送信して、数分間待ちます。

3.  AppDynamics コントローラーで、アプリケーションを選択します。

4.  アプリケーションを監視します。

## <a name="learn"></a>詳細情報

マニュアルとビデオのリンクについては、AppDynamics アカウントのホーム ページを参照してください。

この文書の最新版は、wiki 版 ([http://docs.appdynamics.com/display/ADAZ/How+To+Use+AppDynamics+for+Windows+Azure](http://docs.appdynamics.com/display/ADAZ/How+To+Use+AppDynamics+for+Windows+Azure)) で参照できます。


