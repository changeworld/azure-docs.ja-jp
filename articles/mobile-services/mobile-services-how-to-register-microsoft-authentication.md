<properties
	pageTitle="Microsoft 認証用の登録 | Microsoft Azure"
	description="Azure Mobile Services アプリケーションで Microsoft 認証用に登録する方法について説明します。"
	authors="ggailey777"
	services="mobile-services"
	documentationCenter="Mobile"
	manager="dwrede"
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="NA" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="02/25/2016" 
	ms.author="glenga"/>

# 認証で Microsoft アカウントを使用するためのアプリケーションの登録

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;


[AZURE.INCLUDE [mobile-services-selector-register-identity-provider](../../includes/mobile-services-selector-register-identity-provider.md)]

## 概要

このトピックでは、Azure Mobile Services の ID プロバイダーとして Microsoft アカウントを使用できるようにモバイル アプリを登録する方法について説明します。Live SDK を使用するサービス主導型認証とクライアント主導型認証の両方に同じ手順が適用されます。

##Windows デベロッパー センターで Windows ストア アプリを登録する

Windows ストア アプリを最初に Windows デベロッパー センターで登録する必要があります。登録により、Windows アプリでシングル サインオンの動作を使用できるようになります。

>[AZURE.NOTE]Windows Phone 8、Windows Phone 8.1 Silverlight、および Windows 以外のアプリは、このセクションをスキップできます。

1. アプリケーションをまだ登録していない場合は、[Windows デベロッパー センター](https://dev.windows.com/dashboard/Application/New)に移動して Microsoft アカウントでログインし、アプリの名前を入力して **[アプリ名の予約]** をクリックします。

3. Visual Studio で Windows アプリ プロジェクトを開き、ソリューション エクスプローラーで Windows Store アプリ プロジェクトを右クリックし、**[ストア]**、**[アプリケーションをストアと関連付ける]** の順にクリックします。

  	![](./media/mobile-services-how-to-register-microsoft-authentication/mobile-services-store-association.png)

5. ウィザードで、**[サインイン]** をクリックしてから Microsoft アカウントでサインインし、予約したアプリケーションを選択して **[次へ]**、**[関連付け]** の順にクリックします。

6. (省略可能) ユニバーサル Windows 8.1 アプリケーションの場合、Windows Phone ストア プロジェクトの手順 4 および 5 を繰り返します。

6. 新しいアプリケーションの Windows デベロッパー センター ページに戻り、**[サービス]**、**[プッシュ通知]** の順にクリックします。

7. **[プッシュ通知]** ページで、**[Windows プッシュ通知サービス (WNS) と Microsoft Azure Mobile Services]** の下にある **[ライブ サービス サイト]** をクリックします。
 
	これにより、アプリの Microsoft アカウント アプリ設定ページが表示されます。

8. **パッケージ SID** 値をメモしておきます。この SID をAzure ポータルで保存して、Windows アプリのシングル サインオンとプッシュ通知を有効にすることができます。

次に、Windows アプリの Microsoft アカウント認証を構成します (次のセクションの手順 4 から始めます)。

## Microsoft アカウントの登録の構成と Mobile Services への接続

前のセクションで Windows アプリを既に登録した場合は、手順 2 にスキップできます。

1. Windows ストア アプリ以外の場合、Microsoft アカウント デベロッパー センターの [[マイ アプリケーション]](http://go.microsoft.com/fwlink/p/?LinkId=262039) ページに移動し、Microsoft アカウントでログオンして (必要な場合)、**[アプリケーションの作成]** をクリックし、**[アプリケーション名]** を入力して、**[同意する]** をクリックします。

   	これにより Microsoft アカウントでアプリ名が確保され、アプリの Microsoft アカウント ページが表示されます。

2. アプリの Microsoft アカウント ページで、**[API 設定]** をクリックして **[モバイル アプリまたはデスクトップ クライアント アプリ]** を有効にし、モバイル サービス URL を **[ターゲット ドメイン]** として設定して **[リダイレクト URL]** に次の URL 形式のいずれかを指定し、**[保存]** をクリックします。

	+ **.NET バックエンド**: `https://<mobile_service>.azure-mobile.net/signin-microsoft`
	+ **JavaScript バックエンド**: `https://<mobile_service>.azure-mobile.net/login/microsoftaccount`

	 >[AZURE.NOTE]Mobile Services バックエンドの種類として、正しいリダイレクト URL パスの形式を使用してください。これが正しくない場合、認証は失敗します。**[ルート ドメイン]** は自動的に設定されます。&nbsp;

    ![Microsoft アカウント API の設定](./media/mobile-services-how-to-register-microsoft-authentication/mobile-services-win8-app-push-auth-2.png)


4. **[アプリ設定]** をクリックして、**[クライアント ID]**、**[クライアント シークレット]**、**[パッケージ SID]** の値を書き留めます。

   	![Microsoft アカウント アプリの設定](./media/mobile-services-how-to-register-microsoft-authentication/mobile-services-win8-app-push-auth.png)


    > [AZURE.NOTE] クライアント シークレットは、重要なセキュリティ資格情報です。クライアント シークレットは、他のユーザーと共有したり、アプリケーションで配信したりしないでください。Windows ストア アプリの登録でのみ、パッケージ SID フィールドが表示されます。

4. [Microsoft Azure クラシック ポータル]で、モバイル サービスの **[ID]** タブをクリックし、ID プロバイダーから入手したクライアント ID、クライアント シークレット、パッケージ SID を入力して、**[保存]** をクリックします。

	>[AZURE.NOTE]Windows Phone 8、Windows Phone Store 8.1 Silverlight、または Windows 以外のアプリの場合は、パッケージ SID を指定する必要はありません。

これで、Microsoft アカウントと連携するようにモバイル サービスとアプリケーションが構成されました。

<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->

[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039

[Microsoft Azure クラシック ポータル]: https://manage.windowsazure.com/

<!---HONumber=AcomDC_0302_2016-->