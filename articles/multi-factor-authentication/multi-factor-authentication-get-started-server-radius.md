<properties 
	pageTitle="RADIUS 認証と Azure Multi-Factor Authentication Server" 
	description="RADIUS 認証と Azure Multi-Factor Authentication Server をデプロイする際に役立つ Azure Multi-Factor Authentication のページです。" 
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



# RADIUS 認証と Azure Multi-Factor Authentication Server

RADIUS 認証セクションでは、Azure Multi-Factor Authentication Server の RADIUS 認証を有効にして構成することができます。RADIUS は、認証要求を承認してそれらの要求を処理する標準のプロトコルです。Azure Multi-Factor Authentication Server は RADIUS サーバーとして機能し、RADIUS クライアント (VPN 機器など) と認証ターゲット (Active Directory (AD)、LDAP ディレクトリ、別の RADIUS サーバーなど) の間に挿入され、Azure Multi-Factor Authentication を追加します。Azure Multi-Factor Authentication が機能するには、クライアント サーバーと認証ターゲットの両方と通信するように Azure Multi-Factor Authentication Server を構成する必要があります。Azure Multi-Factor Authentication Server は RADIUS クライアントから要求を受け取り、認証ターゲットに対して資格情報を検証し、Azure Multi-Factor Authentication を追加して、RADIUS クライアントに応答を返します。全体の認証は、プライマリ認証と Azure Multi-Factor Authentication の両方が成功した場合にのみ、成功します。

![RADIUS 認証](./media/multi-factor-authentication-get-started-server-rdg/radius.png)

## RADIUS 認証の構成

RADIUS 認証を構成するには、Azure Multi-Factor Authentication Server を Windows サーバーにインストールします。Active Directory 環境を使用している場合は、サーバーをネットワーク内のドメインに参加させる必要があります。次の手順に従って Azure Multi-Factor Authentication Server を構成します。

1. Azure Multi-Factor Authentication Server 内で、左側のメニューの [RADIUS 認証] アイコンをクリックします。
2. [RADIUS 認証を有効にする] チェック ボックスをオンにします。
3. [クライアント] タブで、構成されるクライアントからの RADIUS 要求をリッスンするために Azure Multi-Factor Authentication RADIUS サービスを非標準のポートにバインドする必要がある場合は、認証ポートとアカウンティング ポートを変更します。
4. [追加…] ボタンをクリックします。
5. [RADIUS クライアントの追加] ダイアログ ボックスで、Azure Multi-Factor Authentication Server、アプリケーション名 (省略可能)、および共有シークレットを認証する機器/サーバーの IP アドレスを入力します。共有シークレットは、Azure Multi-Factor Authentication Server と機器/サーバーの両方で同じである必要があります。アプリケーション名は Azure Multi-factor Authentication レポートに表示され、SMS またはモバイル アプリの認証メッセージにも表示される場合があります。
6. すべてのユーザーが Server にインポート済みである、またはインポート予定であり、多要素認証の対象となる場合は、[Multi-Factor Authentication のユーザー照合が必要] ボックスをオンにします。多数のユーザーがまだサーバーにインポートされていない、および/または多要素認証から除外される場合、ボックスのチェック マークを外したままにします。この機能の追加情報については、ヘルプ ファイルを参照してください。
7. ユーザーが Azure Multi-Factor Authentication モバイル アプリ認証を使用し、帯域外の電話、SMS、またはプッシュ通知に対するフォールバック認証として OATH パスコードを使用する場合は、[代替の OATH トークンを有効にする] ボックスをオンにします。
8. [OK] ボタンをクリックします。
9. RADIUS クライアントを追加する場合は、手順 4. ～ 8. を繰り返します。
10. [ターゲット] タブをクリックします。
11. Azure Multi-Factor Authentication Server を Active Directory 環境のドメインに参加しているサーバーにインストールする場合は、[Windows ドメイン] を選択します。
12. ユーザーを LDAP ディレクトリに対して認証する必要がある場合は、[LDAP バインド] を選択します。LDAP バインドを使用する場合は、[ディレクトリの統合] アイコンをクリックし、[設定] タブで LDAP 構成を編集して、Server を指定のディレクトリにバインドする必要があります。LDAP を構成する手順については、LDAP プロキシの構成ガイドに記載されています。 
13. 別の RADIUS サーバーに対してユーザーを認証する必要がある場合は、[RADIUS サーバー] を選択します。
14. [追加...] ボタンをクリックして、Server が RADIUS 要求を送信するサーバーを構成します。
15. [RADIUS サーバーの追加] ダイアログ ボックスで、RADIUS サーバーの IP アドレスと共有シークレットを入力します。共有シークレットは、Azure Multi-Factor Authentication Server と RADIUS サーバーの両方で同じである必要があります。RADIUS サーバーで異なるポートが使用されている場合は、認証ポートとアカウンティング ポートを変更します。
16. [OK] ボタンをクリックします。 
17. Azure Multi-Factor Authentication Server から受け取ったアクセス要求を処理できるように、Azure Multi-Factor Authentication Server を RADIUS クライアントとしてその他の RADIUS サーバーに追加する必要があります。Azure Multi-Factor Authentication Server で構成されている同じ共有シークレットを使用する必要があります。
18. この手順を繰り返して RADIUS サーバーを追加し、[上へ移動] と [下へ移動] ボタンを使用して Server が呼び出すサーバーの順序を構成できます。これで Azure Multi-Factor Authentication Server の構成は完了です。Server が構成されたポートを使用して、構成されたクライアントからの RADIUS アクセス要求をリッスンするようになります。   


## RADIUS クライアントの構成

RADIUS クライアントを構成するには、ガイドラインに従います。

- RADIUS 経由で Azure Multi-Factor Authentication Server の IP アドレスに対して認証するように機器/サーバーを構成します。これにより、RADIUS サーバーとして機能します。 
- 前に構成した同じ共有シークレットを使用します。 
- RADIUS のタイムアウトを 30 ～ 60 秒に設定し、ユーザーの資格情報を検証する、多要素認証を実行する、応答を受信する、および RADIUS アクセス要求に対して応答する時間を確保します。

<!---HONumber=AcomDC_1125_2015-->