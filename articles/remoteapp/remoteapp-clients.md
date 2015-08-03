
<properties
    pageTitle="アプリにアクセスする"
    description="Azure RemoteApp にサポートされているクライアントとアプリへのアクセス方法について説明します。"
    services="remoteapp"
	documentationCenter=""
    authors="lizap"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="06/30/2015"
    ms.author="elizapo" />



# Azure RemoteApp でのアプリへのアクセス

Azure RemoteApp の優れた点の 1 つは、どのデバイスからでも発行されたアプリにアクセスできることです。さらに、1 つのデバイスで作業を開始し、2 つ目のデバイスにシームレスに移行し、中断した箇所から作業を再開できます。開始するには、デバイスの適切なクライアントをダウンロードして、サービスにサインインする必要があります。

このトピックでは、現在サポートされているクライアントの一覧とダウンロードする方法を確認してから、各クライアントから RemoteApp へサインインする方法を説明します。

## サポートされているクライアント

デバイスがこれらのいずれかのオペレーティング システムを実行している場合は、次の手順を使用して RemoteApp にアクセスできます。

 - Windows 10 プレビュー
 - Windows 8.1
 - Windows 8
 - Windows 7 Service Pack 1
 - Windows RT
 - Windows Phone 8.1
 - iOS
 - Mac OS X
 - Android


 シン クライアントはどうでしょうか。 次の Windows Embedded のシン クライアントはサポートされています。

 - Windows Embedded Standard 7 with Service Pack 1
 - Windows Embedded POSReady7
 - Windows Embedded Thin PC
 - Windows Embedded 8.1 Industry

## クライアントのダウンロード

使用しているプラットフォームに関わらず、 RemoteApp にアクセスする必要があるクライアントは、「[リモート デスクトップ クライアントのダウンロード](https://www.remoteapp.windowsazure.com/ClientDownload/AllClients.aspx)」 ページに表示されています。

別のリンクをクリックすると、クライアントのダウンロードを直接開始するか、またはプラットフォームのアプリ ストアのクライアント ダウンロード ページ移動します。画面の手順に従って、クライアントをインストールします。

デバイスにクライアントをインストールし、起動したら、次の対応するセクションにジャンプし、そのクライアントから RemoteApp へサインインする方法を学習します。

## Android

Google Play ストアから、Microsoft リモート デスクトップ アプリをインストールすると、**[リモート デスクトップ]** にアプリの一覧が表示されます。

1. アプリを起動すると、まだアプリを使用していない場合は、空の接続センターに移動します。Azure RemoteApp を開始するには、追加ボタン **[+]** をタップし、 **[Azure RemoteApp]** をタップします。

![空の接続センター](./media/remoteapp-clients/Android1.png)

2. サービスにアクセスし、プロセスを開始するには、メールアドレスでサインインし、**[開始する]** をタップします。

![サインインのプロンプト](./media/remoteapp-clients/Android2.png)

3. 次のページで **メール アドレス** を入力し、**[続行]** をタップします。これで、Azure Active Directory を使用したサインイン プロセスが開始されます。

![Azure Active Directory の最初のページ](./media/remoteapp-clients/Android3.png)

4. 画面の指示に従って、Microsoft アカウント (LiveID) や組織 ID でサインインします。サインインすると、受信したすべての招待を一覧表示したページが表示されます。信頼する招待を選択し、 **[完了]** をタップします。

![招待のページ](./media/remoteapp-clients/Android4.png)

5. 招待を受け付けると、アクセスできるアプリの一覧がデバイスにダウンロードされ、接続センターで使用可能になります。アプリのいずれかをタップして起動し、使用を開始します。

![フィードによる接続センター](./media/remoteapp-clients/Android5.png)

6. まだ招待がない場合でも、サービスを試すことができます。サービスを試すには、プロンプトが表示されたら、**[無料試用版を試す]** をタップします。

![デモ フィードのプロンプト](./media/remoteapp-clients/Android6.png)

7. これで基本的なアプリのセットにアクセスし、RemoteApp を開始できます。

![Azure RemoteApp のデモ フィード](./media/remoteapp-clients/Android7.png)

## iOS

アプリ ストアから、Microsoft リモート デスクトップ アプリをインストールすると、**「RD Client」** にアプリの一覧が表示されます。

1. アプリを起動すると、まだアプリを使用していない場合は、空の接続センターに移動します。Azure RemoteApp を開始するには、追加ボタン **[+]** をタップし、 **[Azure RemoteApp を追加]** をタップします。

![空の接続センター](./media/remoteapp-clients/IOS1.png)

2. サービスにアクセスし、プロセスを開始するには、メールアドレスでサインインし、**[メールアドレス]** に入力し、**[続行]** をタップします。

![サインインのプロンプト](./media/remoteapp-clients/IOS2.png)

3. 画面の指示に従って、Microsoft アカウント (LiveID) や組織 ID でサインインします。サインインすると、受信したすべての招待を一覧表示したページが表示されます。信頼する招待を選択し、 **[完了]** をタップします。

![招待のページ](./media/remoteapp-clients/IOS3.png)

4. 招待を受け付けると、アクセスできるアプリの一覧がデバイスにダウンロードされ、接続センターで使用可能になります。アプリのいずれかをタップして起動し、使用を開始します。

![フィードによる接続センター](./media/remoteapp-clients/IOS4.png)

5. まだ招待がない場合でも、サービスを試すことができます。サービスを試すには、プロンプトが表示されたら、**[無料試用版を試す]** をタップします。

![デモ フィードのプロンプト](./media/remoteapp-clients/IOS5.png)

6. これで基本的なアプリのセットにアクセスし、RemoteApp を開始できます。

![Azure RemoteApp のデモ フィード](./media/remoteapp-clients/IOS6.png)

## Mac OS X

アプリ ストアから、Microsoft リモート デスクトップ アプリをインストールすると、**[Microsoft リモート デスクトップ]** にアプリの一覧が表示されます。

1. アプリを起動すると、まだアプリを使用していない場合は、空の接続センターに移動します。Azure RemoteApp を開始するには、**[Azure RemoteApp]** ボタンをクリックします。

![空の接続センター](./media/remoteapp-clients/Mac1.png)

2. サービスにアクセスし、プロセスを開始するには、メールアドレスでサインインし、**[開始する]** をタップします。

![サインインのプロンプト](./media/remoteapp-clients/Mac2.png)

3. 次のページで **メール アドレス** を入力し、**[続行]** をタップします。これで、Azure Active Directory を使用したサインイン プロセスが開始されます。

![Azure Active Directory の最初のページ](./media/remoteapp-clients/Mac3.png)

4. 画面の指示に従って、Microsoft アカウント (LiveID) や組織 ID でサインインします。サインインすると、受信したすべての招待を一覧表示したページが表示されます。信頼する招待を選択し、 ダイアログを閉じます。

![招待のページ](./media/remoteapp-clients/Mac4.png)

5. 招待を受け付けると、アクセスできるアプリの一覧がデバイスにダウンロードされ、接続センターで使用可能になります。アプリのいずれかをダブルクリックして起動し、使用を開始します。

![フィードによる接続センター](./media/remoteapp-clients/Mac5.png)

6. まだ招待がない場合でも、サービスを試すことができます。サービスを試すには、プロンプトが表示されたら、**[無料試用版を試す]** をタップします。

![デモ フィードのプロンプト](./media/remoteapp-clients/Mac6.png)

7. これで基本的なアプリのセットにアクセスし、RemoteApp を開始できます。

![Azure RemoteApp のデモ フィード](./media/remoteapp-clients/Mac7.png)

## Windows (Windows Phone 以外のサポートされているすべてのバージョン)

インストールが終了すると、クライアントは自動的に起動しますが、後でもう一度アクセスする必要がある場合は、 **[Azure RemoteApp]** の名前のアプリの一覧に表示されます。

1. クライアントを起動すると、最初に、Azure RemoteApp の開始ページが表示されます。続けるには、**[開始]** をクリックします。

![Azure RemoteApp のクライアントの開始ページ](./media/remoteapp-clients/Windows1.png)

2. 次のページでは、Azure Active Directory を使用した Azure RemoteApp のサインイン プロセスが開始されます。過去に Microsoft のサービスを使用したことのあるユーザーには、このプロセスは見慣れたものです。**メール アドレス** を入力して、**[続行]** をクリックします。

![Azure Active Directory の最初のプロンプト](./media/remoteapp-clients/Windows2.png)

3. 画面の指示に従って、Microsoft アカウント (LiveID) や組織 ID でサインインします。サインインすると、受信したすべての招待を一覧表示したページが表示されます。信頼する招待を選択し、 **[完了]** をタップします。

![Azure RemoteApp のクライアントの招待のページ](./media/remoteapp-clients/Windows3.png)

4. 招待を受け付けると、アクセスできるアプリの一覧がデバイスにダウンロードされ、接続センターで使用可能になります。アプリのいずれかをダブルクリックして起動し、使用を開始します。

![Azure RemoteApp のクライアントの接続センター](./media/remoteapp-clients/Windows4.png)

5. まだ、だれも招待を送信していなくても、デモ コレクションでサービスをテストすることができます。 デモ コレクションへはアクセスできますので、サービスをテストすることができます。

![Azure RemoteApp のデモ フィード](./media/remoteapp-clients/Windows5.png)

## Windows Phone 8.1

Windows Phone 8.1 ストアから、Microsoft リモート デスクトップ アプリをインストールすると、**[リモート デスクトップ]** にアプリの一覧が表示されます。

1. アプリを起動すると、まだアプリを使用していない場合は、空の接続センターに移動します。Azure RemoteApp を開始するには、画面の下部にある追加ボタン **[+]** をタップします。

![空の接続センター](./media/remoteapp-clients/WinPhone1.png)

2. 次に、**[Azure RemoteApp]** をタップします。

![項目ページの追加](./media/remoteapp-clients/WinPhone2.png)

3. サービスにアクセスし、プロセスを開始するには、メールアドレスでサインインし、**[接続]** をタップします。

![サインインのプロンプト](./media/remoteapp-clients/WinPhone3.png)

4. 次のページで **メール アドレス** を入力し、**[続行]** をタップします。これで、Azure Active Directory を使用したサインイン プロセスが開始されます。

![Azure Active Directory の最初のページ](./media/remoteapp-clients/WinPhone4.png)

5. 画面の指示に従って、Microsoft アカウント (LiveID) や組織 ID でサインインします。サインインすると、受信したすべての招待を一覧表示したページが表示されます。信頼する招待を選択し、 **[保存]** をタップします。

![招待のページ](./media/remoteapp-clients/WinPhone5.png)

6. 招待を受け付けると、アクセスできるアプリの一覧がデバイスにダウンロードされ、接続センターで使用可能になります。アプリのいずれかをタップして起動し、使用を開始します。

![フィードによる接続センター](./media/remoteapp-clients/WinPhone6.png)

7. まだ招待がない場合でも、サービスを試すことができます。サービスを試すには、プロンプトが表示されたら、**[はい]** をタップします。

![デモ フィードのプロンプト](./media/remoteapp-clients/WinPhone7.png)

8. これで基本的なアプリのセットにアクセスし、RemoteApp を開始できます。

![Azure RemoteApp のデモ フィード](./media/remoteapp-clients/WinPhone8.png)
 

<!---HONumber=July15_HO4-->