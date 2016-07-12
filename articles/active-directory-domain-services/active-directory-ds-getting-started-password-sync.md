<properties
	pageTitle="Azure AD ドメイン サービス: パスワード同期を有効にする | Microsoft Azure"
	description="Azure Active Directory ドメイン サービスの概要"
	services="active-directory-ds"
	documentationCenter=""
	authors="mahesh-unnikrishnan"
	manager="stevenpo"
	editor="curtand"/>

<tags
	ms.service="active-directory-ds"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="07/06/2016"
	ms.author="maheshu"/>

# Azure AD ドメイン サービス *(プレビュー)* - Azure AD ドメイン サービスとのパスワード同期を有効にする

## タスク 5: AAD ドメイン サービスとのパスワード同期を有効にする (クラウド専用 Azure AD ディレクトリの場合)
Azure AD テナントに対する Azure AD ドメイン サービスを有効にしたら、Azure AD ドメイン サービスとの間で資格情報の同期を有効にします。そうすることで管理下のドメインに対し、ユーザーはその会社の資格情報を使ってサインインできるようになります。

実行する手順は、組織の Azure AD ディレクトリがクラウド専用であるか、Azure AD Connect を使用してオンプレミスのディレクトリと同期するように設定されているかによって異なります。

<br>

> [AZURE.SELECTOR]
- [クラウド専用 Azure AD ディレクトリ](active-directory-ds-getting-started-password-sync.md)
- [同期された Azure AD ディレクトリ](active-directory-ds-getting-started-password-sync-synced-tenant.md)

<br>

### NTLM と Kerberos の資格情報ハッシュを生成する (クラウド専用 Azure AD ディレクトリ)
組織の Azure AD ディレクトリがクラウド専用である場合、Azure AD ドメイン サービスを使用するユーザーは各自のパスワードを変更する必要があります。このパスワード変更プロセスにより、Azure AD ドメイン サービスの Kerberos/NTLM 認証に必要な資格情報のハッシュが Azure AD 内に生成されます。Azure AD ドメイン サービスを使用する必要があるテナントの全ユーザーのパスワードの有効期限を無効にするか、これらのユーザーにパスワードを変更するように指示できます。

パスワードを変更するためにエンド ユーザーに提供する必要がある手順を次に示します。

1. 組織の Azure AD アクセス パネル ページに移動します。これは、通常は [http://myapps.microsoft.com](http://myapps.microsoft.com)です。

2. そのページで **[プロファイル]** タブを選択します。

3. そのページで **[パスワードの変更]** タイルをクリックして、パスワードの変更を開始します。

    ![Azure AD Domain Services 用の仮想ネットワークを作成します。](./media/active-directory-domain-services-getting-started/user-change-password.png)

4. **[パスワードの変更]** ページが表示されます。ユーザーは、既存の (古い) パスワードを入力し、操作を進めてパスワードを変更します。

    ![Azure AD Domain Services 用の仮想ネットワークを作成します。](./media/active-directory-domain-services-getting-started/user-change-password2.png)

ユーザーが自分のパスワードを変更すると、Azure AD ドメイン サービスですぐに新しいパスワードを使用できるようになります。また、数分経つと、新しく変更したパスワードを使用して、管理対象ドメインに参加しているコンピューターにサインインできるようになります。


<br>

## 関連コンテンツ

- [AAD ドメイン サービスとのパスワード同期を有効にする (同期された Azure AD ディレクトリの場合)](active-directory-ds-getting-started-password-sync-synced-tenant.md)

- [Azure AD ドメイン サービスで管理されているドメインの管理](active-directory-ds-admin-guide-administer-domain.md)

- [Azure AD ドメイン サービスで管理されているドメインに Windows 仮想マシンを参加させる](active-directory-ds-admin-guide-join-windows-vm.md)

- [Azure AD ドメイン サービスで管理されているドメインに Red Hat Enterprise Linux 仮想マシンを参加させる](active-directory-ds-admin-guide-join-rhel-linux-vm.md)

<!---HONumber=AcomDC_0706_2016-->