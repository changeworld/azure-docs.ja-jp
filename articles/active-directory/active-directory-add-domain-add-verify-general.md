<properties
	pageTitle="Azure Active Directory にカスタム ドメイン名を追加して確認する | Microsoft Azure"
	description="Azure Active Directory の使用開始の一部として既存のドメインを Azure AD に追加する方法。ユーザー アカウント情報をオンプレミスの ID インフラストラクチャと同期するようにカスタム ドメインをセットアップします。"
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
	ms.topic="article"
	ms.date="02/05/2016"
	ms.author="curtand;jeffsta"/>

# Azure Active Directory にカスタム ドメイン名を追加して確認する

カスタム ドメイン名を追加し、Azure Active Directory で使用できることを確認するには、次の手順を完了する必要があります。

1.  次のいずれかを実行します。

    -   [オンプレミスの Active Directory とフェデレーションされるカスタム ドメイン名を追加する](#add-a-custom-domain-name-that-will-be-federated)

    -   [オンプレミスの Active Directory とフェデレーションされないカスタム ドメイン名を追加する](#add-a-custom-domain-name-that-will-not-be-federated)

2.  カスタム ドメイン名を確認します。

    -   組織がドメイン用のドメイン名レジストラーでカスタム ドメイン名を所有していることを Azure AD が確認するために使用する DNS エントリを追加します。

    -   Azure AD でドメインを確認します。

## フェデレーションされるカスタム ドメイン名を追加する

Azure AD Connect を使用したオンプレミスのディレクトリ統合の詳細については、

「**カスタム ドメイン名を Azure AD ディレクトリに追加するには**」を参照してください。

1.  [Azure クラシック ポータル](https://manage.windowsazure.com/)に、Azure AD のグローバル管理権限を持っているアカウントでサインインします。

2.  Active Directory を選択します。

3.  ディレクトリを開きます。

4.  **[ドメイン]** タブを選択します。

5.  コマンド バーで、**[追加]** を選択します。

6.  カスタム ドメインの名前を入力します。拡張子 .com を必ず指定してください。

7.  **[このドメインを構成して、ローカル Active Directory にシングル サインオンします]** チェックボックスをオンにします。

8.  **[追加]** をクリックします。

## フェデレーションされないカスタム ドメイン名を追加する

ほとんどのカスタム ドメイン名は、第 2 レベルのドメイン "contoso.com" になります。

**カスタム ドメイン名を Azure AD ディレクトリに追加するには**

1.  [Azure クラシック ポータル](https://manage.windowsazure.com/)に、Azure AD のグローバル管理権限を持っているアカウントでサインインします。

2.  Active Directory を選択します。

3.  ディレクトリを開きます。

4.  **[ドメイン]** タブを選択します。

5.  コマンド バーで、**[追加]** を選択します。

6.  カスタム ドメインの名前を入力します。拡張子 .com を必ず指定してください。

7.  **[このドメインを構成して、ローカル Active Directory にシングル サインオンします]** チェックボックスをオフになっていることを確認します。

8.  **[追加]** を選択します。

9.  ドメインがディレクトリに追加されていることを確認します。

## 任意のドメイン名レジストラーでのドメインの確認

ドメインを確認するには、ドメイン名レジストラー (DNS がホストされている場所) で DNS レコードを作成します。Azure AD は、そのレコードを使用してユーザーがドメインを所有していることを確認します。[DNS エントリを一般的な DNS レジストラーで追加するための手順](https://support.office.com/article/Create-DNS-records-for-Office-365-when-you-manage-your-DNS-records-b0f3fdca-8a80-4e8e-9ef3-61e8a2a9ab23/)

ドメイン名レジストラーに登録されているドメインが既にある場合は、必要な DNS レコードは既に存在しています。

カスタム ドメインを追加していても、ドメインがまだ確認されていない場合は、状態に **[未確認]** と表示されます。

## オンプレミスのディレクトリとフェデレーションしないカスタム ドメイン名を確認する
ドメイン用に作成したレコードが、ドメイン レジストラーで DNS システムを通して正常に追加された後、次の操作を行います。

1.  [Azure クラシック ポータル](https://manage.windowsazure.com/)の [Azure Active Directory] で **[ドメイン]** をクリックします。

2.  **[ドメイン]** 一覧で確認するドメインを見つけ、使用しているポータルに基づいて、**[ドメインを確認するにはクリックします]** または **[確認]** をクリックします。

3.  表示された指示に従って確認プロセスを完了します。

    -   ドメインの確認が成功した場合、ドメインがアカウントに追加されたことが通知されます。

    -   ドメインの確認に失敗した場合、ドメイン レジストラーで行った変更の反映に時間がかかる可能性があります。確認をキャンセルし、後でもう一度やり直してください。

ドメインに変更を加えてから 72 時間以上経過している場合は、ドメイン レジストラーの Web サイトにサインインし、エイリアス情報が正しく入力されていることを確認します。情報が正しく入力されていない場合は、間違った DNS レコードを削除し、正しい情報で新しいレコードを作成する必要があります。

## オンプレミスのディレクトリとフェデレーションするカスタム ドメインを確認する

1.  Azure AD Connect をダウンロードして実行します。Azure AD Connect ツールによって、[指定された DNS エントリの追加を求められます](active-directory-aadconnect-get-started-custom.md#verify-the-azure-ad-domain-selected-for-federation)。

## 第 3 レベルのドメイン名

Azure AD では、"europe.contoso.com" などの第 3 レベルのドメインを使用できます。第 3 レベルのドメインを追加して使用するには、次の操作を行います。

1.  第 2 レベルのドメイン “contoso.com” を追加して確認します。

2.  Azure AD に "europe.contoso.com" などの任意のサブドメインを追加します。確認済みの第 2 レベルのドメインのサブドメインを追加すると、第 3 番レベルのドメインは Azure AD によって自動的に確認されます。DNS エントリを追加する必要はありません。

これらの手順は、PowerShell および Graph でも完了できます。

ドメインを確認したら、アカウントで使用できるようにドメインを構成できます。

## 次のステップ

- [カスタム ドメイン名を使用してユーザーのサインインを省力化する](active-directory-add-domain.md)
- [サインイン ページとアクセス パネル ページに対する会社のブランドの追加](active-directory-add-company-branding.md)
- [カスタム ドメインにユーザーを割り当てる](active-directory-add-domain-add-users.md)
- [カスタム ドメイン名の DNS レジストラーを変更する](active-directory-add-domain-change-registrar.md)
- [Azure Active Directory からカスタム ドメインを削除する](active-directory-add-domain-delete-domain.md)

<!---HONumber=AcomDC_0302_2016-->