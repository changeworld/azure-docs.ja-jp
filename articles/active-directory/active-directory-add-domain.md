<properties
	pageTitle="Azure Active Directory へのカスタム ドメイン名の追加 | Microsoft Azure"
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

# Azure Active Directory へのカスタム ドメイン名の追加

組織にはビジネス向けに使用する 1 つ以上のドメイン名があり、ユーザーはその会社のドメイン名を使用してその会社のネットワークにサインインします。Azure Active Directory (Azure AD) を利用していると、会社のドメイン名を Azure AD にも追加することができます。これにより、ディレクトリ内で "alice@contoso.com" のようなユーザーにとって馴染みのあるユーザー名を割り当てることができます。 この場合のプロセスは単純です。

- Azure クラシック ポータルの**ドメインの追加**ウィザードでドメイン名を追加する

- Azure AD クラシック ポータルまたは Azure AD Connect ツールで DNS エントリを取得する

- DNS レジストラーの Web サイトで、ドメイン名の DNS エントリを DNS ゾーン ファイルに追加する

- Azure AD クラシック ポータルまたは Azure AD Connect ツールでドメイン名を検証する


カスタム ドメイン名が検証されるまで、ユーザーはディレクトリの初期ドメイン名に基づくユーザー名 ("alice@contoso.onmicrosoft.com" など) でサインインする必要があります。"contoso.com" や "contosobank.com" など、複数のカスタム ドメイン名が必要な場合、それらのドメイン名を最大で 900 個まで追加できます。各ドメイン名は、この記事の同じ手順に従って追加してください。

## カスタム ドメイン名をディレクトリに追加する

1. Azure AD ディレクトリのグローバル管理者のユーザー アカウントで [Azure クラシック ポータル](https://manage.windowsazure.com/)にサインインします。

2. **[Active Directory]** でディレクトリを開き、**[ドメイン]** タブを選択します。

3. コマンド バーで **[追加]** を選択し、"contoso.com" のようなカスタム ドメインの名前を入力します。必ず、.com、.net、その他の上位拡張子を含めます。

4. オンプレミスの Active Directory との[フェデレーション サインイン](https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Configuring-AD-FS-for-user-sign-in-with-Azure-AD-Connect)に使用できるようにこのドメインを構成する場合は、該当するチェック ボックスをオンにします。

5. **[追加]** を選択します。

ドメイン名を追加したので、Azure AD では、そのドメイン名が組織に所有されていることを検証する必要があります。Azure AD でこの検証を実行するには、ドメイン名の DNS ゾーン ファイルに DNS エントリを追加する必要があります。この作業は、ドメイン名のドメイン名レジストラーの Web サイトで実行できます。

## ドメイン名の DNS エントリを取得する

オンプレミスの Windows Server Active Directory とのフェデレーションを行っていない場合、DNS エントリは**ドメインの追加**ウィザードの 2 ページ目にあります。

フェデレーション用にドメインを構成している場合、Azure AD Connect ツールをダウンロードするよう求められます。Azure AD Connect ツールを実行し、[ドメイン名レジストラーで追加する必要がある DNS エントリを取得します](active-directory-aadconnect-get-started-custom.md#verify-the-azure-ad-domain-selected-for-federation)。Azure AD Connect ツールでは、Azure AD のドメイン名の検証も行われます。

## DNS エントリを DNS ゾーン ファイルに追加します。

1.  ドメインのドメイン名レジストラーにサインインします。DNS エントリを更新するうえで必要な権限がない場合は、そのアクセス権を持つ担当者またはチームに、DNS エントリの追加を依頼してください。

2.  Azure AD から提供された DNS エントリを追加し、ドメインの DNS ゾーン ファイルを更新します。この DNS エントリにより、Azure AD でドメインの所有権を検証することが可能になります。メール ルーティングや Web ホスティングなどの動作は変更されません。DNS レコードが反映されるまでに 1 時間程度かかる場合があります。

[認定された DNS レジストラーに DNS エントリを登録するための手順](https://support.office.com/article/Create-DNS-records-for-Office-365-when-you-manage-your-DNS-records-b0f3fdca-8a80-4e8e-9ef3-61e8a2a9ab23/)

## Azure AD との間でドメイン名を検証する

DNS エントリを追加したら、Azure AD によってドメイン名が検証されたことを確認する必要があります。これが最後の手順となります。

**ドメインの追加**ウィザードがまだ開いている場合は、ウィザードの 3 ページ目で **[検証]** を選択します。検証の前に、DNS エントリが反映されるまでに最大で 1 時間待つ必要があります。

**ドメインの追加**ウィザードが開いていない場合、[Azure クラシック ポータル](https://manage.windowsazure.com/)でドメインを検証できます。

1.  Azure AD ディレクトリのグローバル管理者であるユーザー アカウントでサインインします。

2.  ディレクトリを開き、**[ドメイン]** タブを選択します。

3.  検証するドメインを選択します。

4.  コマンド バーで **[検証]** を選択し、ダイアログ ボックスの **[検証]** を選択します。

お疲れさまでした。 これで[カスタム ドメイン名を含んだユーザー名を割り当てる](active-directory-add-domain-add-users.md)ことができます。ドメイン名の検証で問題が発生した場合は、「[トラブルシューティング](#troubleshooting)」セクションを参照してください。

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
-   [Azure AD のドメイン管理の概念を理解する](active-directory-add-domain-concepts.md)
-   [ユーザーがサインインしたときに会社のブランドを表示する](active-directory-add-company-branding.md)
-   [PowerShell を使用して Azure AD のドメイン名を管理する](https://msdn.microsoft.com/library/azure/e1ef403f-3347-4409-8f46-d72dafa116e0#BKMK_ManageDomains)

<!---HONumber=AcomDC_0504_2016-->