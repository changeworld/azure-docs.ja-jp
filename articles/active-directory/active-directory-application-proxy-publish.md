<properties
	pageTitle="Azure AD アプリケーション プロキシを使用してアプリを発行する | Microsoft Azure"
	description="Azure AD アプリケーション プロキシを使用してオンプレミスのアプリケーションをクラウドに発行します。"
	services="active-directory"
	documentationCenter=""
	authors="kgremban"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="06/01/2016"
	ms.author="kgremban"/>


# Azure AD アプリケーション プロキシを使用してアプリケーションを発行する


Microsoft Azure Active Directory (AD) アプリケーション プロキシを有効にすると、プライベート ネットワークの外部のリモート ユーザーからアクセスできるように、オンプレミスのアプリケーションを発行することができます。

この記事では、ローカル ネットワークで実行中のアプリケーションを発行し、ネットワークの外部からのセキュリティで保護されたリモート アクセスを有効にする手順について説明します。アプリケーション プロキシの設定やコネクタのインストールを行っていない場合は、先に進む前に「[Azure ポータルでアプリケーション プロキシを有効にする](active-directory-application-proxy-enable.md)」の手順に従ってください。

Azure AD アプリケーション プロキシを初めて使用する場合は、アプリケーションを発行する前に、プライベート ネットワークから Web サイトを発行してコネクタをテストすることをお勧めします。

> [AZURE.NOTE] アプリケーション プロキシは、Azure Active Directory の Premium または Basic エディションにアップグレードしている場合にのみ利用できる機能です。詳細については、「[Azure Active Directory のエディション](active-directory-editions.md)」をご覧ください。

## ウィザードを使用してアプリを発行する

1. [Azure クラシック ポータル](https://manage.windowsazure.com/)に管理者としてサインインします。
2. Active Directory に移動し、アプリケーション プロキシを有効にしたディレクトリを選択します。

	![Active Directory - icon](./media/active-directory-application-proxy-publish/ad_icon.png)

3. **[アプリケーション]** タブをクリックし、画面下部にある **[追加]** をクリックします

	![アプリケーションの追加](./media/active-directory-application-proxy-publish/aad_appproxy_selectdirectory.png)

4. **[ネットワーク外部からアクセスできるアプリケーションを発行します]** を選択します。

	![Publish an application that will be accessible from outside your network](./media/active-directory-application-proxy-publish/aad_appproxy_addapp.png)

5. アプリケーションについて以下の情報を入力します。

	- **名前**: アプリケーションのわかりやすい名前。ディレクトリ内で一意の名前にする必要があります。
	- **内部 URL**: プライベート ネットワークの内部からアプリケーションにアクセスするために、アプリケーション プロキシ コネクタで使用されるアドレス。バックエンド サーバー上の特定のパスを指定して発行できます。この場合、サーバーのそれ以外のパスは発行されません。この方法では、同じサーバー上の複数のサイトを発行し、それぞれのサイトに独自の名前とアクセス規則を付与することができます。
	- **事前認証方法**: アプリケーション プロキシでユーザーにアプリケーションへのアクセス権を付与する前に、そのユーザーを確認する方法。ドロップダウン メニューから、いずれかのオプションを選択します。

		- Azure Active Directory: Azure AD へのサインインのために、アプリケーション プロキシによってユーザーがリダイレクトされ、ディレクトリとアプリケーションに対するユーザーのアクセス許可が認証されます。
		- パススルー: ユーザーは、アプリケーションにアクセスするために認証を行う必要はありません。

	![Application properties](./media/active-directory-application-proxy-publish/aad_appproxy_appproperties.png)

6. ウィザードを終了するには、画面下部にあるチェック マークをクリックします。これで、アプリケーションが Azure AD で定義されました。


## アプリケーションにユーザーとグループを割り当てる

発行したアプリケーションにユーザーがアクセスできるようにするには、それらのユーザーを個別またはグループで割り当てる必要があります。事前認証を必要とするアプリの場合は、これによって、アプリを使用するためのアクセス許可がユーザーに付与されます。事前認証を必要としないアプリの場合、ユーザーにアクセス許可は必要ありませんが、ユーザーをアプリに割り当てて、そのアプリがアプリケーションの一覧に表示されるようにする必要があります。

1. アプリの追加ウィザードを終了すると、アプリケーションの [クイック スタート] ページが表示されます。アプリにアクセスできるユーザーを管理するには、**[ユーザーとグループ]** を選択します。

	![Application Proxy quick start assign users - screenshot](./media/active-directory-application-proxy-publish/aad_appproxy_usersgroups.png)

2. ディレクトリ内の特定のグループを検索するか、すべてのユーザーを表示します。チェック マークをクリックして結果を表示します。

  	![Search for groups or users - screenshot](./media/active-directory-application-proxy-publish/aad_appproxy_search.png)

2. このアプリに割り当てる各ユーザーまたはグループを選択し、**[割り当て]** をクリックします。この操作を確認するように求められます。

> [AZURE.NOTE] 統合 Windows 認証アプリでは、オンプレミスの Active Directory から同期したユーザーとグループのみを割り当てることができます。Microsoft アカウントでサインインしているユーザーやゲストを、Azure Active Directory アプリケーション プロキシを使用して発行したアプリに割り当てることはできません。ユーザーがサインイン時には、発行するアプリと同じドメインに含まれる資格情報を使用するようにしてください。


## 詳細な構成

[構成] ページで、発行したアプリの変更や詳細オプションの設定が可能です。このページでは、名前の変更やロゴのアップロードによってアプリをカスタマイズできます。また、事前認証方法や多要素認証のようなアクセス ルールを管理することもできます。

![詳細な構成](./media/active-directory-application-proxy-publish/aad_appproxy_configure.png)


Azure Active Directory アプリケーション プロキシを使用してアプリケーションを発行すると、それらのアプリケーションが Azure AD の [アプリケーション] リストに表示され、そこで管理できるようになります。

アプリケーションの発行後にアプリケーション プロキシ サービスを無効にした場合、アプリケーションは削除されませんが、プライベート ネットワークの外部からそのアプリケーションにアクセスできなくなります。

アプリケーションを表示し、アクセス可能かどうかを確認するには、そのアプリケーションの名前をダブルクリックします。アプリケーション プロキシ サービスが無効になっていて、アプリケーションが使用できない場合は、画面上部に警告メッセージが表示されます。

アプリケーションを削除するには、リスト内のアプリケーションを選択し、**[削除]** をクリックします。

## 次のステップ

- [独自のドメイン名でアプリケーションを発行する](active-directory-application-proxy-custom-domains.md)
- [シングル サインオンを有効にする](active-directory-application-proxy-sso-using-kcd.md)
- [条件付きアクセスを有効にする](active-directory-application-proxy-conditional-access.md)
- [要求に対応するアプリケーションを利用する](active-directory-application-proxy-claims-aware-apps.md)

最新のニュースと更新情報については、[アプリケーション プロキシに関するブログ](http://blogs.technet.com/b/applicationproxyblog/)をご覧ください。

<!---HONumber=AcomDC_0608_2016-->