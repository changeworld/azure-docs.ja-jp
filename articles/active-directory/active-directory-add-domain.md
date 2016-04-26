<properties
	pageTitle="Azure Active Directory によるサインインを簡単にするためにカスタム ドメイン名を追加する | Microsoft Azure"
	description="Azure Active Directory に会社のドメイン名を追加する方法とドメイン名を検証する方法"
	services="active-directory"
	documentationCenter=""
	authors="jeffsta"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="04/19/2016"
	ms.author="curtand;jeffsta"/>

# Azure Active Directory によるサインインを簡単にするためにカスタム ドメイン名を追加する

独自のカスタム ドメイン名を使用すると、サインインをはじめとする Azure Active Directory (Azure AD) のユーザー エクスペリエンスを高め、簡略化することができます。たとえば、勤務先の組織が "contoso.com" というドメイン名を所有している場合、ユーザーは慣れ親しんだユーザー名 (joe@contoso.com など) でサインインすることができます。 Azure AD ディレクトリのグローバル管理者であるユーザー アカウントにアクセスするための権限が必要です。

Azure Active Directory のテナント ディレクトリを最初に取得するとき、ユーザーは ‘alice@contoso.onmicrosoft.com’ のようなユーザー名でサインインします。 この例では、contoso.onmicrosoft.com が組み込まれている初期ドメイン名です。カスタム ドメイン名を確認するまでこれを利用できます。次の手順の 1 つで、組織が所有する ‘contoso.com’ などのカスタム ドメイン名を追加することがあります。 ‘alice@contoso.com’ など、ユーザーになじみのある名前を割り当てることができます。

Azure AD でドメイン名が使用されるしくみの背景については、「[Domain Management Concepts in Azure AD (Azure AD のドメイン管理の概念)](active-directory-add-domain-concepts.md)」をお読みください。ほとんどの管理者は、Azure クラシック ポータルを使用し、Azure AD でドメイン名管理タスクを実行します。ただし、必要であれば、[PowerShell](https://msdn.microsoft.com/library/azure/e1ef403f-3347-4409-8f46-d72dafa116e0#BKMK_ManageDomains) または [Graph API プレビュー](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/domains-operations)を利用し、管理タスクを実行できます。

## カスタム ドメイン名をディレクトリに追加する

カスタム ドメイン名をディレクトリに追加するには:

1. Azure AD ディレクトリのグローバル管理者となっているユーザー アカウントで [Azure クラシック ポータル](https://manage.windowsazure.com/)にサインインします。

2. 左のナビゲーション バーで **[Active Directory]** を選択します。

3. ディレクトリを開きます。

4. **[ドメイン]** タブを選択します。

5. コマンド バーで、**[追加]** を選択します。

6. ‘contoso.com’ など、カスタム ドメインの名前を入力します。必ず、.com、.net、その他の上位拡張子を含めます。

7. オンプレミス Active Directory で[フェデレーション サインイン](https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Configuring-AD-FS-for-user-sign-in-with-Azure-AD-Connect)にこのドメインを構成するには、チェック ボックスを選択します。

8. **[追加]** を選択します。

カスタム ドメイン名を含むユーザー名を割り当てる前に、組織がドメイン名を所有していることを Azure AD は検証する必要があります。この検証を実行するには、ドメインのドメイン名レジストラーで DNS エントリを更新する必要があります。

## ドメイン名を検証するために Azure AD で使用される DNS エントリを取得する

ドメインを追加したとき、フェデレーションのドメインを構成するオプションを選択した場合、Azure AD Connect ツールをダウンロードするためのガイダンスが表示されます。Azure AD Connect ツールを実行し、[ドメイン名レジストラーで追加する必要がある DNS エントリを取得します](active-directory-aadconnect-get-started-custom.md#verify-the-azure-ad-domain-selected-for-federation)。

Windows Server AD のフェデレーション サインインのオプションを選択しなかった場合、**ドメインの追加**ウィザードの 2 つ目のページに DNS エントリが表示されます。

## ドメインの DNS ゾーン ファイルに DNS エントリを追加する

Azure AD で必要になる DNS エントリを追加するには:

1.  ドメインのドメイン名レジストラーにサインインします。ドメインの DNS ゾーン ファイルを更新する権限が与えられていない場合、組織内でそのアクセス権を持つ人物またはチームと DNS エントリを共有し、更新を依頼します。

2.  Azure AD から提供された DNS エントリを追加し、ドメインの DNS ゾーン ファイルを更新します。これらの DNS エントリにより、Azure AD でドメインの所有権を検証することが可能になります。メール ルーティングや Web ホスティングなどの動作は変更されません。

[DNS エントリを一般的な DNS レジストラーで追加するための手順](https://support.office.com/article/Create-DNS-records-for-Office-365-when-you-manage-your-DNS-records-b0f3fdca-8a80-4e8e-9ef3-61e8a2a9ab23/)

## Azure AD でドメインを検証する

DNS エントリを追加すると、Azure AD でドメインを検証できます。

カスタム ドメイン名をフェデレーションするオプションを選択した場合、検証は Azure AD Connect ツールにより自動的に行われます。前提条件を完了したら、ツールを実行します。完了していない場合、Azure クラシック ポータルでドメインを検証します。**ドメインの追加**ウィザードがまだ開いている場合、ウィザードの 3 ページで検証ボタンをクリックできます。DNS レコードの反映には数分かかることがあります。

**ドメインの追加**ウィザードが開いていない場合、[Azure クラシック ポータル](https://manage.windowsazure.com/)でドメインを検証できます。

1.  Azure AD ディレクトリのグローバル管理者であるユーザー アカウントでサインインします。

2.  左のナビゲーション バーで **[Active Directory]** を選択します。

3.  ディレクトリを開きます。

4.  **[ドメイン]** タブを選択します。

5.  ドメインの一覧で、検証するドメインを選択します。

6.  コマンド バーで **[確認]** を追加します。

7.  ダイアログ ボックスで **[確認]** を選択します。

これでカスタム ドメイン名を含むユーザー名を割り当てることができます。

## カスタム ドメイン名を追加する

‘contoso.com’ や ‘contosobank.com’ など、組織が複数のカスタム ドメイン名を使用する場合、各ドメイン名を Azure AD ディレクトリに追加できます。最大で 900 ドメインを追加できます。上記の同じ手順を利用し、後続ドメイン名を追加します。

次のステップ

-   [ユーザー名にカスタム ドメイン名を割り当てる](active-directory-add-domain-add-users.md)

-   [カスタム ドメイン名を管理する](active-directory-add-manage-domain-names.md)

-   [ユーザーがサインインしたときに会社のブランドを表示する](active-directory-add-company-branding.md)

-   [Azure AD のドメイン管理の概念](active-directory-add-domain-concepts.md)

<!---HONumber=AcomDC_0420_2016-->