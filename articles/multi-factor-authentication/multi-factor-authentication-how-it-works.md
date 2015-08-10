<properties 
	pageTitle="Azure Multi-Factor Authentication - しくみ" 
	description="Azure Multi-Factor Authentication を使えば、シンプルなサインイン プロセスを好むユーザーのニーズに応えながら、データやアプリケーションへのアクセスを効果的に保護することが可能です。2 番目の形式の認証を要求することで追加のセキュリティを提供し、次の一連の容易な確認オプションによって強力な認証を提供します。" 
	services="multi-factor-authentication" 
	documentationCenter="" 
	authors="billmath" 
	manager="terrylan" 
	editor="bryanla"/>

<tags 
	ms.service="multi-factor-authentication" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/02/2015" 
	ms.author="billmath"/>

#Azure Multi-Factor Authentication のしくみ

多要素認証のセキュリティは、レイヤー アプローチによってもたらされます。攻撃者にとって、多要素認証を侵害することはかなり困難です。攻撃者にユーザーのパスワードを知られても、信頼できるデバイスを手に入れなければ役に立ちません。ユーザーがデバイスを紛失した場合、そのデバイスを見つけた人は、ユーザーのパスワードを知らなければデバイスを使用することができません。

![追加のセキュリティ確認](./media/multi-factor-authentication-how-it-works/howitworks.png)



Azure Multi-Factor Authentication を使えば、シンプルなサインイン プロセスを好むユーザーのニーズに応えながら、データやアプリケーションへのアクセスを効果的に保護することが可能です。2 番目の形式の認証を要求することで追加のセキュリティを提供し、次の一連の容易な確認オプションによって強力な認証を提供します。

- 音声通話 
- テキスト メッセージ
- モバイル アプリの通知 (ユーザーは希望の方法を選択できます)
- モバイル アプリの検証コード
- サード パーティの OATH トークン

しくみの詳細については、次のビデオをご覧ください。

[AZURE.VIDEO multi-factor-authentication-deep-dive-securing-access-on-premises]

##Multi-Factor Authentication で使用可能な方法
ユーザーがサインインしようとすると、追加の確認がユーザーに対して行われます。この 2 番目の確認のために使用できる方法の一覧を次に示します。

確認方法 | 説明 
------------- | ------------- |
電話 | ユーザーのスマートフォンが呼び出され、# 記号を押してサインインを確認するよう求めるメッセージが再生されます。これによって確認プロセスが完了します。このオプションは構成可能であり、指定するコードを変更することができます。
テキスト メッセージ | 6 桁のコードを含むテキスト メッセージがユーザーのスマートフォンに送信されます。確認プロセスを完了するには、このコードを入力します。
モバイル アプリの通知 | ユーザーのスマートフォンに、モバイル アプリで [確認] を選択して確認を完了するよう求める確認要求が送信されます。これは、プライマリの確認方法としてアプリの通知を選択した場合に発生します。サインインしようとしていないときにこの通知を受信した場合、ユーザーはそれを不正アクセスとして報告できます。
モバイル アプリの検証コード | ユーザーのスマート フォンで実行されているモバイル アプリに確認コードが送信されます。これは、プライマリの確認方法として確認コードを選択した場合に発生します。


##Azure Multi-Factor Authentication の使用可能なバージョン
Azure Multi-Factor Authentication は、次の 3 種類のバージョンが使用可能です。下の表では各バージョンについて詳細に説明しています。

バージョン | 説明 
------------- | ------------- |
Office 365 の多要素認証 | このバージョンは、Office 365 アプリケーション専用に動作し、Office 365 ポータルから管理されます。これにより、管理者は多要素認証を使用して Office 365 リソースを保護できるようになります。このバージョンには、Office 365 サブスクリプションが付属しています。
Azure 管理者用の多要素認証 | すべての Azure 管理者には、Office 365 向けと同じ多要素認証機能が追加費用なしでご利用いただけます。Azure サブスクリプションのすべての管理者アカウントは、この主要な多要素認証機能を有効にすることによって、追加の保護を実現できるようになりました。したがって、Azure ポータルにアクセスして、VM と Web サイトの作成、ストレージの管理、モバイル サービスの使用など、Azure のさまざまなサービスを利用する必要のある管理者は、各自の管理者アカウントに多要素認証を追加することができます。
Azure Multi-Factor Authentication | Azure Multi-Factor Authentication は、豊富な機能を備えています。Azure 管理ポータルを介した追加の構成オプション、高度なレポート、および一連のオンプレミスおよびクラウド アプリケーションのサポートを提供します。Azure Multi-Factor Authentication は、Azure Active Directory Premium の一部として提供されます。

##バージョンごとの機能の比較
下の表は、さまざまなバージョンの Azure Multi-Factor Authentication で使用できる機能の一覧を提供しています。


機能 | Office 365 の多要素認証 (Office 365 SKU に付属)|Azure 管理者用の多要素認証 (Azure サブスクリプションに付属) | Azure Multi-Factor Authentication (Azure AD Premium および Enterprise Mobility Suite に付属) 
------------- | :-------------: |:-------------: |:-------------: |
管理者は MFA によってアカウントを保護することができます。| \* | \* (Azure 管理者アカウントのみ使用可能)|\*
モバイル アプリを 2 番目の要素にする|\* | \* | \*
音声通話を 2 番目の要素にする|\* | \* | \*
SMS を 2 番目の要素にする|\* | \* | \*
MFA をサポートしていないクライアントのアプリ パスワード|\* | \* | \*
認証方法の管理制御| | | \*
PIN モード| | | \*
不正アクセスのアラート| | | \*
MFA レポート| | | \*
ワンタイム バイパス| | | \*
音声通話のカスタムあいさつ文| | | \*
音声通話の発信元 ID のカスタマイズ| | | \*
イベントの確認| | | \*
信頼できる IP| | | \*
記憶されたデバイスでの MFA の中断 (パブリック プレビュー)| | | \*
MFA の SDK| | | \*
MFA サーバーによる、オンプレミス アプリケーション用の MFA| | | \*


##Azure Multi-Factor Authentication の入手方法

Azure Multi-Factor Authentication は、Azure Active Directory Premium および Enterprise Mobility Suite の一部として提供されます。これらが既にある場合は、Azure Multi-Factor Authentication も入手済みです。

現在、Office 365 ユーザーまたは Azure サブスクライバーであり、Azure Multi-Factor Authentication が提供する追加機能の利用を希望する場合は、引き続きお読みください。

上記のいずれも入手していない場合、Azure Multi-Factor Authentication を使用するには、まず Azure サブスクリプションまたは [Azure 試用サブスクリプション](http://azure.microsoft.com/pricing/free-trial/)が必要です。

Azure Multi-Factor Authentication を使用する際は、次の 2 種類の課金オプションが使用可能です。

- **ユーザーごと**。通常は、定期的に認証が必要な固定数の従業員用に Multi-Factor Authentication を有効にすることを望む企業向けです。
- **認証ごと**。通常は、認証を必要とする頻度が低い外部ユーザーの大規模グループ用に Multi-Factor Authentication を有効にすることを望む企業向けです。

料金の詳細については、[「Azure MFA の料金」](http://azure.microsoft.com/pricing/details/multi-factor-authentication/)を参照してください。

お客様の組織にとって最適なモデルを選択してください。作業を開始するには、[「使用の開始」](multi-factor-authentication-get-started.md)を参照してください。



 

<!---HONumber=July15_HO5-->