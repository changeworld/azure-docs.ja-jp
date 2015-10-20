<properties
	pageTitle="Azure AD テナントを取得する方法 | Microsoft Azure"
	description="アプリケーションを登録および構築するために Azure Active Directory テナントを取得する方法を説明します。"
	services="active-directory"
	documentationCenter=""
	authors="dstrockis"
	manager="terrylan"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="09/28/2015"
	ms.author="dastrock"/>

# Azure Active Directory テナントを取得する方法

Azure Active directory (Azure AD) では、[テナント](https://msdn.microsoft.com/library/azure/jj573650.aspx#BKMK_WhatIsAnAzureADTenant)は組織を代表するものです。テナントは、Azure AD サービスの専用インスタンスであり、組織が Azure、Microsoft Intune、Office 365 などの Microsoft クラウド サービスにサインアップしたときに提供されて所有します。各 Azure AD テナントは、他の Azure AD テナントと区別され分離されています。

テナントは、企業内のユーザーおよびパスワード、ユーザー プロファイル データ、アクセス許可などのユーザーに関する情報を保持しています。また、グループ、アプリケーション、および組織とそのセキュリティに関するその他の情報も含まれます。

Azure AD ユーザーがアプリケーションにサインインできるようにするには、自分のテナントにアプリケーションを登録する必要があります。Azure AD テナントへのアプリケーションの発行は**完全に無料**です。実際、ほとんどの開発者は、実験、開発、ステージング、テストのために複数のテナントとアプリケーションを作成します。アプリケーションにサインアップして使用する組織は、高度なディレクトリ機能を利用したければライセンスの購入を選択できます。

それでは、Azure AD テナントを取得するにはどうすればよいでしょうか。 ユーザーが次のどれに該当するかによって、プロセスは少しずつ異なる場合があります。

- [既存の Office 365 サブスクリプションを所有している](#use-an-existing-office-365-subscription)
- [Microsoft アカウントに関連付けられた既存の Azure サブスクリプションを所有している](#use-an-msa-azure-subscription)
- [組織のアカウントに関連付けられた既存の Azure サブスクリプションを所有している](#use-an-organizational-azure-subscription)
- [上記のいずれも所有していず、最初から開始する](#start-from-scratch)

## 既存の Office 365 サブスクリプションを使用する
既存の Office 365 サブスクリプションを所有していて、Azure サブスクリプションを所有していない ([Azure 管理ポータル](https://manage.windowsazure.com)にサインインできない) 場合は、[こちらの説明](https://technet.microsoft.com/library/dn832618.aspx)に従って Azure AD テナントにアクセスしてください。

## MSA Azure サブスクリプションを使用する
以前に個人の Microsoft アカウントで Azure サブスクリプションにサインアップしている場合は、既にテナントを持っています。 [Azure 管理ポータル](https://manage.windowsazure.com)の [すべてのアイテム] および [Active Directory] にテナント名「既定のテナント」が表示されているはずです。 このテナントは自由に使用できますが、組織の管理者アカウントを作成する必要がある場合があります。

これを行うには、次の手順に従います。または、新しいテナントを作成し、同様のプロセスでそのテナントに管理者を作成することもできます。

1.	個人アカウントで [Azure 管理ポータル](https://manage.windowsazure.com)にログインします。
2.	ポータルの [Active Directory] セクションに移動します (左側のナビゲーション バー)。
3.	使用可能なディレクトリの一覧で、[既定のディレクトリ] エントリを選択します。
4.	ページの上部にある [ユーザー] リンクをクリックします。[ソース ディレクトリ] 列が [Microsoft アカウント] という値の 1 人のユーザーが表示されます。
5.	ページ下部で [ユーザーの追加] をクリックします。
6.	[ユーザーの追加] フォームで、次の詳細を指定します。
    - ユーザーの種類: 組織内の新しいユーザー
    - ユーザー名: (この管理者のユーザー名を選択)
    - 名/姓/表示名: (適切な値を選択)
    - ロール: グローバル管理者
    - 連絡用電子メール アドレス: (適切な値を入力)
    - オプション: 多要素認証の有効化
    - 最後に、緑の [作成] ボタンをクリックしてユーザーの作成を終了します (一時的なパスワードが表示されます)。
7.	[ユーザーの追加] フォームの入力を完了し、新しい管理ユーザーの一時パスワードを受け取ったら、パスワードを変更するためにこの新しいユーザーでログインする必要があるので、このパスワードを忘れずに記録しておきます。連絡用電子メールを使用して、ユーザーにパスワードを直接送信することもできます。
8.	一時パスワードを変更するには、この新しいユーザー アカウントで https://login.microsoftonline.com にログインし、要求されたらパスワードを変更します。


## 組織の Azure サブスクリプションを使用する
以前に組織のアカウントで Azure サブスクリプションにサインアップしている場合は、既にテナントを持っています。 [Azure 管理ポータル](https://manage.windowsazure.com)の [すべてのアイテム] および [Active Directory] にテナントが表示されているはずです。 必要に応じて、このテナントを自由に使用できます。ポータルの左下隅にある [新規] ボタンを使用して、新しいテナントを作成することもできます。


## 最初から行う
上記のいずれにも当てはまらなくても、心配しないでください。[https://account.windowsazure.com/organization](https://account.windowsazure.com/organization) にアクセスして、新しい組織で Azure にサインアップするだけです。プロセスが完了すると、サインアップ時に選択したドメイン名で専用の Azure AD テナントが作成されます。[Azure 管理ポータル](https://manage.windowsazure.com)の左側にあるナビゲーションで [Active Directory] に移動すると、テナントを見つけることができます。

Azure へのサインアップ プロセスの中で、クレジット カードの詳細を提供する必要があります。Azure AD でのアプリケーションの発行または新しいテナントの作成には料金は発生しないので安心してください。

<!---HONumber=Oct15_HO3-->