<properties
	pageTitle="Azure Active Directory プレビューにカスタム ドメイン名を追加する | Microsoft Azure"
	description="Azure Active Directory に会社のドメイン名を追加する方法とドメイン名を検証する方法"
	services="active-directory"
	documentationCenter=""
	authors="jeffsta"
	manager="femila"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/12/2016"
	ms.author="curtand"/>

# Azure Active Directory プレビューにカスタム ドメイン名を追加する

組織にはビジネス向けに使用する 1 つ以上のドメイン名があり、ユーザーはその会社のドメイン名を使用してその会社のネットワークにサインインします。Azure Active Directory (Azure AD) プレビューを使用している場合、会社のドメイン名を Azure AD にも追加することができます。プレビューの機能については、[こちらの記事](active-directory-preview-explainer.md)をご覧ください。 これにより、ディレクトリ内で "alice@contoso.com" のようなユーザーにとって馴染みのあるユーザー名を割り当てることができます。 この場合のプロセスは単純です。

1. カスタム ドメイン名をディレクトリに追加する
2. ドメイン名レジストラーでドメイン名の DNS エントリを追加する
3. Azure AD でカスタム ドメイン名を検証する

## ドメイン名を追加する方法

1.  ディレクトリの全体管理者であるアカウントで [Azure ポータル](https://portal.azure.com)にサインインします。

2.  **[その他のサービス]** を選択し、テキスト ボックスに「**Azure Active Directory**」と入力して、**Enter** キーを押します。

    ![ユーザー管理を開く](./media/active-directory-domains-add-azure-portal/user-management.png)

3. **[*ディレクトリ名*]** ブレードで、**[ドメイン名]** を選択します。

4. **[*ディレクトリ名* -ドメイン名]** ブレードで、**[追加]** をクリックします。

  ![[追加] をクリックする](./media/active-directory-domains-add-azure-portal/add-command.png)

5. **[ドメイン名]** ブレードで、ボックスにカスタム ドメインの名前 ("contoso.com" など) を入力し、**[ドメインの追加]** をクリックします。必ず、.com、.net、その他の上位拡張子を含めます。

6. **[*domainname*]** ブレード (タイトルが新しいドメイン名である、開いているブレード) で、組織がそのカスタム ドメイン名を所有していることを確認するために Azure AD が使用する DNS エントリ情報を取得します。

  ![DNS エントリ情報を取得する](./media/active-directory-domains-add-azure-portal/get-dns-info.png)

ドメイン名を追加したので、Azure AD では、そのドメイン名が組織に所有されていることを検証する必要があります。Azure AD でこの検証を実行するには、ドメイン名の DNS ゾーン ファイルに DNS エントリを追加する必要があります。この作業は、ドメイン名のドメイン名レジストラーの Web サイトで実行できます。

## ドメインのドメイン名レジストラーで DNS エントリを追加する

Azure AD でカスタム ドメイン名を使用できるようにするために、次の手順としてドメインの DNS ゾーン ファイルを更新します。これにより、組織がそのカスタム ドメイン名を所有していることを Azure AD で検証できます。

1.  ドメインのドメイン名レジストラーにサインインします。DNS エントリを更新するアクセス権がない場合は、アクセス権を持つ人物またはチームに連絡し、手順 2. の実行と、完了後の連絡を依頼します。

2.  Azure AD から提供された DNS エントリを追加し、ドメインの DNS ゾーン ファイルを更新します。この DNS エントリにより、Azure AD でドメインの所有権を検証することが可能になります。メール ルーティングや Web ホスティングなどの動作は変更されません。

DNS エントリの追加に関するヘルプについては、[一般的な DNS レジストラーで DNS エントリを追加する手順](https://support.office.com/article/Create-DNS-records-for-Office-365-when-you-manage-your-DNS-records-b0f3fdca-8a80-4e8e-9ef3-61e8a2a9ab23/)に関するページをご覧ください。

## Azure AD との間でドメイン名を検証する

DNS エントリを追加すると、Azure AD でドメイン名を検証することができるようになります。

DNS レコードが反映された後でないと、ドメイン名を検証できません。反映には数秒しかかからない場合がほとんどですが、1 時間以上かかる場合もあります。最初から検証が機能しない場合は、後でもう一度やり直してください。

1.  ディレクトリの全体管理者であるアカウントで [Azure ポータル](https://portal.azure.com)にサインインします。

2.  **[参照]** を選択し、テキスト ボックスに「ユーザー管理」と入力して、**Enter** キーを押します。

    ![ユーザー管理を開く](./media/active-directory-domains-add-azure-portal/user-management.png)

3. **[ユーザー管理 - ドメイン名]** ブレードで、検証する必要がある未確認のドメイン名を選択します。

4. **[*domainname*]** ブレード (タイトルが新しいドメイン名である、開いているブレード) で、**[確認]** をクリックして検証を実行します。

これで[カスタム ドメイン名を含むユーザー名を割り当てる](active-directory-create-users-azure-portal.md)ことができます。

## トラブルシューティング

カスタム ドメイン名を検証できない場合は、次のことを試してください。以下では、最も一般的な原因から順に列挙します。

1.	**1 時間待つ**: DNS レコードは、Azure AD がドメインを検証する前に反映されている必要があります。これに 1 時間以上かかる場合があります。

2.	**DNS レコードが正しく入力されていることを確認する**: ドメインのドメイン名レジストラーの Web サイトで、この手順を実行します。Azure AD は、DNS エントリが DNS ゾーン ファイルに存在しない場合、または Azure AD によって提供された DNS エントリと完全に一致しない場合は、ドメイン名を検証できません。ドメインの DNS レコードをドメイン名レジストラーで更新するアクセス権がない場合は、組織内でそのアクセス権を持つ人物またはチームと DNS エントリを共有し、DNS エントリの追加を依頼します。

3.	**Azure AD の別のディレクトリからドメイン名を削除する**: ドメイン名は、1 つのディレクトリ内だけでしか検証できません。ドメイン名が以前に他のディレクトリで検証されていた場合は、新しいディレクトリで検証する前に、前のディレクトリ内から削除されている必要があります。ドメイン名の削除については、[カスタム ドメイン名の管理](active-directory-domains-manage-azure-portal.md)に関する記事をご覧ください。

## カスタム ドメイン名を追加する

複数のカスタム ドメイン名 (たとえば "contoso.com" と "contosobank.com" など) を使用する場合、ドメイン名は最大 900 個まで使用することができます。この記事の同じ手順に従って各ドメイン名を追加してください。

## 次のステップ

[カスタム ドメイン名を管理する](active-directory-domains-manage-azure-portal.md)

<!---HONumber=AcomDC_0914_2016-->