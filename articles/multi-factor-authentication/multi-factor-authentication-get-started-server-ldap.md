<properties 
	pageTitle="LDAP 認証と Azure Multi-Factor Authentication Server"
	description="LDAP 認証と Azure Multi-Factor Authentication Server をデプロイする際に役立つ Azure Multi-Factor Authentication のページです。"
	services="multi-factor-authentication"
	documentationCenter=""
	authors="kgremban"
	manager="femila"
	editor="curtand"/>

<tags
	ms.service="multi-factor-authentication"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="08/04/2016"
	ms.author="kgremban"/>

# LDAP 認証と Azure Multi-Factor Authentication Server


既定では、Azure Multi-Factor Authentication Server は、Active Directory からユーザーをインポートするか同期するように構成されます。ただし、ADAM ディレクトリや特定の Active Directory ドメイン コントローラーなどの別の LDAP ディレクトリにバインドするように構成できます。LDAP 経由でディレクトリに接続するように構成した場合は、認証を実行する LDAP プロキシとして機能するように Azure Multi-Factor Authentication Server を構成できます。LDAP バインドは、RADIUS ターゲットとして使用する、IIS 認証使用時にユーザーを事前認証する、または Azure Multi-Factor Authentication ユーザー ポータルでのプライマリ認証のために使用することもできます。

Azure Multi-Factor Authentication を LDAP プロキシとして使用する場合、多要素認証を追加するために、Azure Multi-Factor Authentication Server が LDAP クライアント (VPN アプライアンス、アプリケーションなど) と LDAP ディレクトリ サーバーの間に挿入されます。Azure Multi-Factor Authentication を機能させるには、クライアント サーバーと LDAP ディレクトリの両方と通信するように Azure Multi-Factor Authentication Server を構成する必要があります。この構成の中で、Azure Multi-Factor Authentication Server は、クライアント サーバーとアプリケーションから LDAP 要求を受け取り、プライマリ資格情報を検証するためにターゲット LDAP ディレクトリ サーバーに要求を転送します。LDAP ディレクトリからの応答でプライマリ資格情報が有効であることが示された場合、Azure Multi-Factor Authentication は第 2 要素認証を実行し、応答を LDAP クライアントに送信します。認証全体は、LDAP サーバーへの認証と多要素認証の両方が成功した場合にのみ成功します。





## LDAP 認証の構成


LDAP 認証を構成するには、Azure Multi-Factor Authentication Server を Windows サーバーにインストールします。次の手順に従います。

1. Azure Multi-Factor Authentication Server 内で、左側のメニューの [LDAP 認証] アイコンをクリックします。
2. [LDAP 認証を有効にする] チェックボックスをオンにします。![LDAP 認証](./media/multi-factor-authentication-get-started-server-ldap/ldap2.png)
3. Azure Multi-Factor Authentication LDAP サービスを、構成対象のクライアントからの LDAP 要求をリッスンする標準ポート以外のポートにバインドする必要がある場合は、[クライアント] タブで TCP ポートと SSL ポートを変更します。
4. クライアントから Azure Multi-Factor Authentication Server に対して LDAPS を使用する場合は、Server が実行されているサーバーに SSL 証明書をインストールする必要があります。[SSL 証明書] ボックスの横にある [参照…] ボタンをクリックし、セキュリティで保護された接続で使用するインストール済みの証明書を選択します。
5. [追加…] ボタンをクリックします。
6. [LDAP クライアントの追加] ダイアログ ボックスで、Server に対して認証するアプライアンス、サーバー、またはアプリケーションの IP アドレスを入力し、アプリケーション名 (省略可能) を入力します。アプリケーション名は Azure Multi-factor Authentication レポートに表示され、SMS またはモバイル アプリの認証メッセージにも表示される場合があります。
7. すべてのユーザーが Server にインポート済みであるかインポート予定であり、多要素認証の対象となる場合は、[Azure Multi-Factor Authentication のユーザー照合が必要] ボックスをオンにします。多数のユーザーがまだ Server にインポートされていない、または多要素認証から除外される場合、ボックスはオフのままにします。この機能の追加情報については、ヘルプ ファイルを参照してください。
8. 手順 5. ～ 7. を繰り返して、別の LDAP クライアントを追加できます。
9. Azure Multi-Factor Authentication が LDAP 認証を受けるように構成するときは、これらの認証を LDAP ディレクトリに委任する必要があります。したがって、[ターゲット] タブには LDAP ターゲットを使用する 1 つのオプションだけがグレー表示されます。LDAP ディレクトリ接続を構成するには、[ディレクトリ統合] アイコンをクリックします。
10. [設定] タブで、[特定の LDAP 構成の使用] ラジオ ボタンを選択します。
11. [編集…] ボタンをクリックします。
12. [LDAP 構成の編集] ダイアログ ボックスで、LDAP ディレクトリに接続するために必要な情報を各フィールドに入力します。フィールドの説明を、次の表に示します。注: この情報は、Azure Multi-Factor Authentication Server のヘルプ ファイルにも含まれています。![ディレクトリ統合](./media/multi-factor-authentication-get-started-server-ldap/ldap.png)
13. [テスト] ボタンをクリックして、LDAP 接続をテストします。
14. LDAP 接続テストが成功した場合、[OK] ボタンをクリックします。
15. [フィルター] タブをクリックします。Server は、Active Directory からコンテナー、セキュリティ グループ、およびユーザーを読み込むように事前構成されています。別の LDAP ディレクトリにバインドする場合は、表示されるフィルターの編集が必要になることがあります。フィルターの詳細については、[ヘルプ] リンクをクリックしてください。
16. [属性] タブをクリックします。Server は、Active Directory から属性をマッピングするように事前構成されています。
17. 別の LDAP ディレクトリにバインドする、または事前構成されている属性のマッピングを変更する場合は、[編集...] ボタンをクリックします。
18. [属性の編集] ダイアログ ボックスで、ディレクトリの LDAP 属性マッピングを変更します。属性名はキーボードから入力するか、各フィールドの横にある […] ボタンをクリックして選択できます。
19. 属性の詳細については、[ヘルプ] リンクをクリックしてください。
20. [OK] ボタンをクリックします。
21. [会社の設定] アイコンをクリックし、[ユーザー名の解決] タブを選択します。ドメインに参加しているサーバーから Active Directory に接続する場合は、[Windows セキュリティ識別子 (SID) をユーザー名の照合に使用する] ラジオ ボタンをオンのままにすることができます。それ以外の場合は、[LDAP 一意識別子の属性をユーザー名の照合に使用する] ラジオ ボタンをオンにします。これを選択した場合、Azure Multi-Factor Authentication Server は、各ユーザー名を LDAP ディレクトリ内の一意識別子に解決しようとします。[ディレクトリ統合] の [属性] タブに定義されたユーザー名の各属性に対して LDAP 検索が実行されます。ユーザーが認証されると、ユーザー名が LDAP ディレクトリ内の一意識別子に解決され、その一意識別子が Azure Multi-Factor Authentication データ ファイル内のユーザーと照合するために使用されます。これにより、大文字と小文字を区別せず、ユーザー名の形式 (長短) を問わない比較が可能になります。Azure Multi-Factor Authentication Server の構成はこれで完了です。これで、Server は、構成されたポートで構成されたクライアントからの LDAP アクセス要求をリッスンし、認証用に LDAP ディレクトリにこれらの要求を委任するように設定されました。


## LDAP クライアントの構成

LDAP クライアントを構成するには、次のガイドラインに従います。

- アプライアンス、サーバー、またはアプリケーションのLDAP を経由した Azure Multi-Factor Authentication Server での認証は、あたかも LDAP ディレクトリで行われるかのように構成します。サーバー名または IP アドレスを Azure Multi-Factor Authentication Server のサーバー名または IP アドレスにすること以外は、LDAP ディレクトリに直接接続するために通常使用するものと同じ設定を使用する必要があります。
- LDAP タイムアウトは、LDAP ディレクトリでユーザーの資格情報を検証し、第 2 要素認証を実行し、応答を受け取って LDAP アクセス要求に応答する時間があるように、30 ～ 60 秒に構成します。
- LDAPS を使用する場合、LDAP クエリを行うアプライアンスまたはサーバーは、Azure Multi-Factor Authentication Server にインストールされている SSL 証明書を信頼する必要があります。

<!---HONumber=AcomDC_0921_2016-->