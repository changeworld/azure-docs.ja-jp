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
	ms.date="04/20/2016"
	ms.author="curtand;jeffsta"/>

# Azure Active Directory によるサインインを簡単にするためにカスタム ドメイン名を追加する

Azure Active Directory (Azure AD) に初めてディレクトリを追加するときに重要な作業の 1 つとして、組織で使用するカスタム ドメイン名 ("contoso.com" など) の検証があります。この作業を行うことで、ユーザーにとって馴染みのあるユーザー名を割り当てることができます ("alice@contoso.com" など)。 ユーザーは、カスタム ドメイン名が検証されるまで、ディレクトリの初期ドメイン名に基づくユーザー名 ("alice@contoso.onmicrosoft.com" など) でサインインする必要があります。

## カスタム ドメイン名をディレクトリに追加する

カスタム ドメイン名をディレクトリに追加するには:

1. Azure AD ディレクトリのグローバル管理者のユーザー アカウントで [Azure クラシック ポータル](https://manage.windowsazure.com/)にサインインします。

2. 左のナビゲーション バーで **[Active Directory]** を選択し、ディレクトリを開きます。

4. **[ドメイン]** タブを選択します。

5. コマンド バーで、**[追加]** を選択します。

6. "contoso.com" など、カスタム ドメインの名前を入力します。必ず、.com、.net、その他の上位拡張子を含めます。

7. オンプレミスの Active Directory との[フェデレーション サインイン](https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Configuring-AD-FS-for-user-sign-in-with-Azure-AD-Connect)に使用できるようにこのドメインを構成するには、該当するチェック ボックスをオンにします。

8. **[追加]** を選択します。

カスタム ドメイン名を含むユーザー名を割り当てる前に、組織がドメイン名を所有していることを Azure AD は検証する必要があります。この検証を実行するには、ドメイン名の DNS ゾーン ファイルに DNS エントリを追加する必要があります。この作業は、ドメイン名レジストラーの Web サイトで実行できます。

## ドメイン名の DNS エントリを取得する

フェデレーションを想定したドメインの構成を選択した場合、Azure AD Connect ツールをダウンロードするよう求められます。Azure AD Connect ツールを実行し、[ドメイン名レジストラーで追加する必要がある DNS エントリを取得します](active-directory-aadconnect-get-started-custom.md#verify-the-azure-ad-domain-selected-for-federation)。

フェデレーションを想定したドメインの構成を選択しなかった場合は、**ドメインの追加**ウィザードの 2 ページ目に DNS エントリが表示されます。

## DNS エントリを DNS ゾーン ファイルに追加します。

Azure AD で必要になる DNS エントリを追加するには、次の手順に従います。

1.  ドメインのドメイン名レジストラーにサインインします。DNS エントリを更新するうえで必要な権限がない場合は、そのアクセス権を持つ担当者またはチームに、DNS エントリの追加を依頼してください。

2.  Azure AD から提供された DNS エントリを追加し、ドメインの DNS ゾーン ファイルを更新します。この DNS エントリにより、Azure AD でドメインの所有権を検証することが可能になります。メール ルーティングや Web ホスティングなどの動作は変更されません。

[認定された DNS レジストラーに DNS エントリを登録するための手順](https://support.office.com/article/Create-DNS-records-for-Office-365-when-you-manage-your-DNS-records-b0f3fdca-8a80-4e8e-9ef3-61e8a2a9ab23/)

## Azure AD との間でドメイン名を検証する

DNS エントリを追加すると、Azure AD との間でドメイン名を検証できます。

カスタム ドメイン名のフェデレーションを行うように選択した場合は、Azure AD Connect ツールによって自動的に検証が実行されます。前提条件を完了したら、ツールを実行します。完了していない場合、Azure クラシック ポータルでドメインを検証します。**ドメインの追加**ウィザードがまだ開いている場合は、ウィザードの 3 ページ目で **[Verify]** (検証) をクリックしてください。DNS レコードが反映されるまでに 1 時間程度かかる場合があります。

**ドメインの追加**ウィザードが開いていない場合、[Azure クラシック ポータル](https://manage.windowsazure.com/)でドメインを検証できます。

1.  Azure AD ディレクトリのグローバル管理者であるユーザー アカウントでサインインします。

2.  左のナビゲーション バーで **[Active Directory]** を選択します。

3.  ディレクトリを開きます。

4.  **[ドメイン]** タブを選択します。

5.  ドメインの一覧で、検証するドメインを選択します。

6.  コマンド バーで **[Verify]** (検証) を選択します。

7.  ダイアログ ボックスで **[Verify]** (検証) を選択します。

これで[カスタム ドメイン名を含んだユーザー名を割り当て](active-directory-add-domain-add-users.md)ることができます。

## カスタム ドメイン名を追加する

"contoso.com" や "contosobank.com" など、組織が複数のカスタム ドメイン名を使用する場合、それらを Azure AD ディレクトリに追加できます。追加できるドメイン名は最大 900 件です。同じ手順に従ってそれぞれのドメイン名を追加してください。

## トラブルシューティング
カスタム ドメイン名を検証できない場合、いくつかの原因が考えられます。以下では、最も一般的な原因から順に列挙します。

- DNS エントリが反映される前にドメイン名を検証しようとした。しばらく待って、もう一度やり直してください。

- DNS レコードの入力漏れがある。DNS エントリを確認し、反映されるまで待ってからやり直してください。

- 既に別のディレクトリでドメイン名が検証済みである。他のディレクトリから該当するドメイン名を特定、削除したうえで、やり直してください。

- DNS レコードに誤りがある。エラーを修正してからやり直してください。

- DNS レコードを更新するために必要な権限がない。必要なアクセス権を持つ組織内の担当者またはチームに連絡し、DNS エントリを追加してもらいます。


## 次のステップ

-   [カスタム ドメイン名を含むユーザー名を割り当てる](active-directory-add-domain-add-users.md)

-   [カスタム ドメイン名を管理する](active-directory-add-manage-domain-names.md)

-   [ユーザーがサインインしたときに会社のブランドを表示する](active-directory-add-company-branding.md)

-   [PowerShell を使用して Azure AD のドメイン名を管理する](https://msdn.microsoft.com/library/azure/e1ef403f-3347-4409-8f46-d72dafa116e0#BKMK_ManageDomains)

-   [Azure AD のドメイン管理の概念を理解する](active-directory-add-domain-concepts.md)

<!---HONumber=AcomDC_0427_2016-->