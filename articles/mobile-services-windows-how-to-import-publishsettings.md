<properties linkid="develop-mobile-how-to-guides-import-publish-settings" urlDisplayName="Import your subscription publish settings file in Visual Studio 2013" pageTitle="Import your publish settings file in Visual Studio 2013 | Mobile Services" metaKeywords="Azure import publishsettings, mobile services" description="Learn how to import a subscription publish settings file for your Azure Mobile Services application in Visual Studio 2013." title="Import your subscription publish settings file in Visual Studio 2013" documentationCenter="Mobile" services="" solutions="" manager="" editor="" videoId="" scriptId="" authors="glenga" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="glenga" />

# サブスクリプション発行設定ファイルを Visual Studio 2013 にインポートする

モバイル サービスを作成する前に、Azure サブスクリプションから Visual Studio に発行設定ファイルをインポートする必要があります。これにより、Visual Studio は Azure に自動的に接続できます。

1.  Visual Studio 2013 で、ソリューション エクスプローラーを開き、プロジェクトを右クリックして、**[追加]**、**[接続済みサービス]** の順にクリックします。

    ![add connected service][add connected service]

2.  [サービス マネージャー] ダイアログで **[サービスの作成]** をクリックし、[モバイル サービスの作成] ダイアログの **[サブスクリプション]** で **[インポート]** をクリックします。

    ![create a new mobile service from VS 2013][create a new mobile service from VS 2013]

3.  [Azure サブスクリプションのインポート] で、**[サブスクリプション ファイルのダウンロード]** をクリックし、必要であれば Azure アカウントにログインします。ブラウザーがファイルの保存を要求した場合は、**[保存]** をクリックします。

    ![download subscription file in VS][download subscription file in VS]

    <div class="dev-callout"><strong>注</strong> <p>ログイン ウィンドウはブラウザーに表示されます。このウィンドウは、Visual Studio のウィンドウの後ろに表示されている場合があります。ダウンロードした .publishsettings ファイルの保存場所を書き留めておいてください。プロジェクトが Azure サブスクリプションに既に接続している場合は、この手順をスキップできます。</p></div>

4.  **[参照]** をクリックし、.publishsettings ファイルを保存した場所に移動して、そのファイルを選択します。次に、**[開く]** をクリックして **[インポート]** をクリックします。

    ![import subscription in VS][import subscription in VS]

    Visual Studio によって、Azure サブスクリプションへの接続に必要なデータがインポートされます。サブスクリプションに 1 つ以上の既存のモバイル サービスが含まれている場合、サービス名が表示されます。

    <div class="dev-callout"><strong>セキュリティに関する注意</strong> <p>発行設定をインポートした後、ダウンロードした .publishsettings ファイルには他のユーザーがアカウントにアクセスするために使用できる情報が含まれているので、削除することを検討してください。接続されている他のアプリケーション プロジェクトで使用するためにこのファイルを保持する場合は、セキュリティで保護してください。</p></div>

<!-- Anchors. --> 
<!-- Images. --> 
<!-- URLs. -->

  [add connected service]: ./media/mobile-services-create-new-service-vs2013/mobile-add-connected-service.png
  [create a new mobile service from VS 2013]: ./media/mobile-services-create-new-service-vs2013/mobile-create-service-from-vs2013.png
  [download subscription file in VS]: ./media/mobile-services-create-new-service-vs2013/mobile-import-azure-subscription.png
  [import subscription in VS]: ./media/mobile-services-create-new-service-vs2013/mobile-import-azure-subscription-2.png
