<properties
	pageTitle="Azure AD Connect - Windows リモート管理のヒント | Microsoft Azure"
	description="Azure AD Connect Windows リモート管理を AD FS と共に使用するためのヒント"
	services="active-directory"
	documentationCenter=""
	authors="billmath"
	manager="stevenpo"
	editor="curtand"/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/13/2015"
	ms.author="billmath"/>

# Azure AD Connect - Windows リモート管理のヒント


Azure AD Connect を使用して Active Directory フェデレーション サービスまたは Web アプリケーション プロキシをデプロイする場合、接続および構成が正常に完了するように、以下のヒントに示す要件を確認します。

- ターゲット サーバーがドメインに参加している場合は、Windows リモート管理が有効であることを確認します。
	* 管理者特権の PSH コマンド ウィンドウで、コマンド "Enable-PSRemoting –force" を使用します。

- ターゲット サーバーが、ドメインに参加していない WAP コンピューターである場合は、いくつかの追加の要件があります。
	- ターゲット コンピューター (WAP コンピューター) での要件

- サービス スナップインから winrm (Windows Remote Management / WS-Management) サービスが実行されていることを確認します。

- 管理者特権の PSH コマンド ウィンドウで、コマンド "Enable-PSRemoting –force" を使用します。
	- ウィザードを実行しているコンピューターでの要件 (ターゲット コンピューターがドメインに参加していないか、信頼されていないドメインにある場合)

- 管理者特権の PSH コマンド ウィンドウで、コマンド "Set-item WSMan:\\localhost\\Client\\TrustedHosts –Value <DMZServerFQDN> -Force –Concatenate" を使用します。
	- サーバー マネージャーでの要件
		- DMZ WAP ホストをコンピューターのプールに追加します ([サーバー マネージャー]、[管理]、[サーバーの追加] の順にクリックし、[DNS] タブを使用)。
		- サーバー マネージャーの [すべてのサーバー] タブで、 WAP サーバーを右クリックし、[管理に使用する資格情報] を選択し、WAP コンピューターのローカルの資格情報 (ドメインの資格情報ではない) を入力します。
		- リモートの PSH 接続を検証するには、サーバー マネージャーの [すべてのサーバー] タブで WAP サーバーを右クリックし、[Windows PowerShell] を選択します。リモート PSH セッションが開き、リモート PowerShell セッションを確立できます。

**その他のリソース**


* [Azure AD Connect アカウントとアクセス許可の詳細](active-directory-aadconnect-account-summary.md)
* [Azure AD Connect のカスタム インストール](active-directory-aadconnect-get-started-custom.md)
* [MSDN の Azure AD Connect](active-directory-aadconnect.md)

<!---HONumber=Oct15_HO3-->