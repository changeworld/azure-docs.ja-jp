<properties 
	pageTitle="Windows 認証と Azure Multi-Factor Authentication Server" 
	description="これは、Windows 認証と Azure Multi-Factor Authentication Server をデプロイする際に役立つ Azure Multi-Factor Authentication のページです。" 
	services="multi-factor-authentication" 
	documentationCenter="" 
	authors="billmath" 
	manager="stevenpo" 
	editor="curtand"/>

<tags 
	ms.service="multi-factor-authentication" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/19/2015" 
	ms.author="billmath"/>

# Windows 認証と Azure Multi-Factor Authentication Server

Windows 認証セクションでは、管理者が 1 つ以上のアプリケーションで Windows 認証を有効にして構成できます。Windows 認証を設定する前に考慮する必要がある点の一覧を次に示します。

-  ターミナル サービスで Azure Multi-Factor Authentication を有効にするには再起動が必要です。
-  [Multi-Factor Authentication のユーザー照合が必要] ボックスがオンになっており、ユーザーがユーザー一覧にいない場合、再起動後にマシンにログインできません。
-  信頼できる IP は、アプリケーションが認証付きのクライアント IP を提供できるかどうかに依存します。現在はターミナル サービスのみがサポートされます。  







>[AZURE.NOTE]この機能は Windows Server 2012 R2 上のターミナル サービスのセキュリティを保護しません。
 



## Windows 認証を使用してアプリケーションを保護するには、次の手順に従います。

1. Azure Multi-Factor Authentication Server で、[Windows 認証] アイコン ![Windows 認証](./media/multi-factor-authentication-get-started-server-windows/windowsauth.png) をクリックします。
2. [Windows 認証を有効にする] チェック ボックスをオンにします。既定では、このボックスはオフになっています。
3. [アプリケーション] タブでは、管理者が Windows 認証を使用する 1 つ以上のアプリケーションを構成できます。
4. サーバーまたはアプリケーションを選択し、サーバーまたはアプリケーションが有効になっているかどうかを指定します。[OK] をクリックします。
5. [追加…] ボタンをクリックします。
6. [信頼される IP] タブでは、特定の IP から送信された Windows セッションの Azure Multi-Factor Authentication をスキップすることができます。たとえば、従業員がオフィスと自宅の両方でアプリケーションを使用する場合、オフィスにいる間は Azure Multi-Factor Authentication の電話を鳴らさないように設定できます。それには、社内のサブネットを信頼される IP エントリとして指定します。
7. [追加…] ボタンをクリックします。
8. 単一の IP アドレスをスキップする場合は、[単一 IP を追加する] を選択します。
9. IP 範囲全体をスキップする場合は、[IP 範囲を指定して追加する] を選択します (例: 10.63.193.1-10.63.193.100)。
10. サブネット表記を使用して IP の範囲を指定する場合は、[サブネット] を選択します。サブネットの開始 IP を入力し、ドロップダウン リストから適切なネットマスクを選択します。 
11. [OK] ボタンをクリックします。

<!---HONumber=AcomDC_1125_2015-->