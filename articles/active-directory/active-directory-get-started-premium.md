<properties
	pageTitle="Azure Active Directory Premium の概要"
	description="Azure Active Directory Premium エディションにサインアップする方法について説明します。"
	services="active-directory"
	documentationCenter=""
	authors="markusvi"
	manager="stevenpo" 
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="04/18/2016"
	ms.author="markvi"/>

# Azure Active Directory Premium の概要

Azure Active Directory には、Free、Basic、Premium という 3 つのエディションがあります。Free エディションは、Azure または Office 365 のサブスクリプションに含まれます。Basic および Premium エディションは、[Microsoft Enterprise Agreement](https://www.microsoft.com/ja-JP/licensing/licensing-programs/enterprise.aspx) または[オープン ボリューム ライセンス](https://www.microsoft.com/ja-JP/licensing/licensing-programs/open-license.aspx) プログラムで利用できます。Azure および Office 365 のサブスクリプション会員も、Active Directory Premium をオンラインで購入できます。購入するには、[こちらでサインイン](https://portal.office.com/Commerce/Catalog.aspx)してください。

> [AZURE.NOTE]
Azure Active Directory のワールドワイド インスタンスを使用している中国のお客様は、Azure Active Directory Premium および Basic エディションを使用できます。中国の 21Vianet が運営する Microsoft Azure サービスでは、Azure Active Directory Premium および Basic エディションは現在サポートされていません。詳細については、[Azure Active Directory フォーラム](https://feedback.azure.com/forums/169401-azure-active-directory/)からお問い合わせください。

Azure Active Directory Premium は、**Enterprise Mobility Suite** にも含まれます。Enterprise Mobility Suite は、1 つのライセンス プランで次のサービスをまとめて利用できるという対費用効果の高い方法を組織に提供します。

- Active Directory Premium 
- Azure Rights Management
- Microsoft Intune


詳細については、[Enterprise Mobility Suite](https://www.microsoft.com/ja-JP/server-cloud/enterprise-mobility/overview.aspx) の Web サイトをご覧ください。

Azure Active Directory Premium の機能は、次のセクションの手順に従ってすぐに使い始めることができます。これらの手順は Azure Active Directory Basic エディションにも適用されます。

## 手順 1: Active Directory Premium にサインアップする

サインアップについては、[ボリューム ライセンス](http://www.microsoft.com/ja-JP/licensing/how-to-buy/how-to-buy.aspx)の Web サイトを参照してください。

## 手順 2: ライセンス プランをアクティブ化する

マイクロソフトから初めて Enterprise Volume Licensing プログラムのライセンス プランを購入すると、確認メールが届きます。最初のライセンス プランをアクティベートするにはそのメールが必要になります。このディレクトリでの 2 回目以降の購入では、ライセンスは同じディレクトリで自動的にアクティブ化されます。

アクティベーションを開始するには、**[サインイン]** または **[サインアップ]** をクリックします。


![][1]

既存のテナントがある場合は、**[サインイン]** をクリックし、既存の管理者アカウントでサインインします。ライセンスをアクティブ化する必要があるディレクトリからグローバル管理者の資格情報でサインインすることが重要です。

自分のライセンス プランで使用する新しい Azure Active Directory テナントを作成する場合、**[サインアップ]** をクリックし、**[アカウント プロファイルの作成]** ダイアログを開きます。

![][2]

完了すると、テナントのライセンス プランのアクティベーションの確定として次のダイアログが表示されます。

![][3]

## 手順 3: Azure Active Directory のアクセスをアクティブ化する

ライセンスがディレクトリにプロビジョニングされると、**ウェルカム メール**が届きます。そのメールで、Azure Active Directory Premium または Enterprise Mobility Suite のライセンスと機能の管理を開始できることが確認されます。

以前に Microsoft Azure を使用したことがある場合は、[http://manage.windowsazure.com](http://manage.windowsazure.com) にアクセスして新しいライセンスを割り当てることができます (詳しくは[手順 4](#step-4-assign-license-to-user-accounts) を参照)。

初めて Microsoft Azure を使用する場合、メール内の **[サインイン]** をクリックするか、[Azure Active Directory へのアクセス](https://account.windowsazure.com/signup?offer=MS-AZR-0110P) アクティベーション ページに進みます。いずれの方法でも、一連の手順を実行し、Azure クラシック ポータルでディレクトリにアクセスします。

![][4]

サインインできたら、携帯電話の番号を入力して検証を受け、第 2 要素の認証画面を完了する必要があります。携帯による検証を完了したら、**[サインアップ]** をクリックし、Azure Active Directory へのアクセスをアクティベートできます。

![][5]

アクティベーションには数分かかることがあります。アクセスがアクティブになると、茶色のバーが消え、**[ポータル]** をクリックできます。

![][6]

この例では、Azure へのアクセスは Azure Active Directory に制限されています。

![][7]

以前の使用により、Azure に既にアクセスできる状態である場合があります。さらに、追加の Azure サブスクリプションをアクティブ化することによって、Azure Active Directory へのアクセスを完全な Azure アクセスにアップグレードできます。このような場合、Azure クラシック ポータルで与えられる機能が増えます。

![][8]

ウェルカム メールを受け取る前に Azure Active Directory へのアクセスをアクティブ化しようとすると、次のエラー メッセージが表示される場合があります。電子メールを受け取った後、少ししてからもう一度やり直してください。

![][9]

サブスクリプションの新しい管理者も、このリンクを介して Azure クラシック ポータルへのアクセスをアクティブ化できます。

## 手順 4: ユーザー アカウントにライセンスを割り当てる

購入したプランの使用を開始する前に、組織内のユーザーが Premium で提供される豊富な機能を使用できるように、ユーザー アカウントにライセンスを手動で割り当てる必要があります。以下の手順を使用して、ユーザーにライセンスを割り当て、ユーザーが Azure Active Directory Premium の機能を使用できるようにします。

ライセンスをユーザーに割り当てるには:

1. カスタマイズするディレクトリのグローバル管理者として、Azure クラシック ポータルにサインインします。
2. **[Active Directory]** をクリックし、ライセンスを割り当てるディレクトリを選択します。
3. **[ライセンス]** タブを選択し、**[Active Directory Premium]** または **[Enterprise Mobility Suite]** を選択して、**[割り当て]** をクリックします。

    ![][10]

4. ダイアログ ボックスで、ライセンスを割り当てるユーザーを選択し、チェック マーク アイコンをクリックして変更を保存します。

    ![][11]

## ライセンスの制限

一部のライセンス プランは、他のライセンス プランのサブセットまたはスーパーセットです。通常、ユーザーに既に割り当てられているライセンス プランをユーザーに割り当てることはできません。スーパーセットであるライセンス プランを割り当てる場合は、先にサブセットのライセンス プランを削除する必要があります。

## ライセンスの要件

ユーザーにライセンスを割り当てるときは、アカウントのプロパティで主な利用場所を指定できます。利用場所を指定しないと、テナントの場所が自動的にユーザーに割り当てられます。

![][12]

Microsoft クラウド サービスで利用できるサービスと機能は、国または地域によって異なります。Voice over Internet Protocol (VoIP) などのサービスは、ある国または地域では利用できても、他の国または地域では利用できないことがあります。サービスに含まれる機能は、法的な理由から特定の国や地域では制限される場合があります。サービスまたは機能を利用するときの制限の有無を確認するには、サービスのライセンス制限サイトで国または地域を検索してください。

## 参照トピック

- [サインイン ページとアクセス パネル ページに対する会社のブランドの追加](active-directory-add-company-branding.md)
- [アクセスおよび使用状況レポートの表示](active-directory-view-access-usage-reports.md)

<!--Image references-->
[1]: ./media/active-directory-get-started-premium/MOLSEmail.png
[2]: ./media/active-directory-get-started-premium/MOLSAccountProfile.png
[3]: ./media/active-directory-get-started-premium/MOLSThankYou.png
[4]: ./media/active-directory-get-started-premium/AADEmail.png
[5]: ./media/active-directory-get-started-premium/SignUppage.png
[6]: ./media/active-directory-get-started-premium/Subscriptionspage.png
[7]: ./media/active-directory-get-started-premium/Premiuminportal.png
[8]: ./media/active-directory-get-started-premium/Premiuminportal_large.png
[9]: ./media/active-directory-get-started-premium/Signuppage_oops.png
[10]: ./media/active-directory-get-started-premium/contosolicenseplan.png
[11]: ./media/active-directory-get-started-premium/Assignlicensespicker.png
[12]: ./media/active-directory-get-started-premium/Usagelocation.png

<!---HONumber=AcomDC_0420_2016-->