<properties 
	pageTitle="しくみ: Azure AD でのパスワード管理 | Microsoft Azure" 
	description="ユーザーによるパスワードの登録、リセット、および変更、そして管理者によるオンプレミスの Active Directory パスワードの構成、レポート作成、および管理を含む、Azure AD でのパスワード管理について学習します。" 
	services="active-directory" 
	documentationCenter="" 
	authors="asteen" 
	manager="kbrint" 
	editor="billmath"/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/16/2015" 
	ms.author="asteen"/>

# パスワード管理のしくみ
Azure Active Directory でのパスワード管理は、以下に説明するいくつかの論理コンポーネントで構成されます。コンポーネントの詳細については、各リンクをクリックしてください。

- [**パスワード管理の構成ポータル **](#password-management-configuration-portal) – 管理者は[ Microsoft Azure 管理ポータル](https://manage.windowsazure.com)にある自分のディレクトリの [構成] タブから、テナント内のパスワード管理方法のさまざまな側面を制御できます。
- [**ユーザー登録ポータル**](#user-registration-portal) – ユーザーはこの Web ポータルから、パスワード リセットのための自己登録ができます。
- [**ユーザー パスワードのリセット ポータル**](#user-password-reset-portal) – ユーザーは、管理者の定めるパスワード リセット ポリシーに従っていくつかのチャレンジをクリアすることにより、自分のパスワードをリセットできます。
- [**ユーザー パスワードの変更ポータル**](#user-password-change-portal) – ユーザーはこの Web ポータルを使用すると、古いパスワードを入力し、新しいパスワードを選択することにより、自分のパスワードをいつでも変更できます。
- [**パスワード管理レポート**](#password-management-reports) – 管理者は[ Microsoft Azure 管理ポータル](https://manage.windowsazure.com)にある自分のディレクトリの [レポート] タブの [活動レポート] から、テナント内のパスワードのリセットと登録のアクティビティを参照および分析できます
- [**Azure AD Connect のパスワード ライトバック コンポーネント**](#password-writeback-component-of-azure-ad-connect) – 管理者は Azure AD Connect のインストール時に、必要に応じてパスワード ライトバック機能を有効にし、クラウドからのフェデレーション ユーザーまたはパスワード同期ユーザーのパスワード管理を有効化できます。

## パスワード管理の構成ポータル
特定のディレクトリについて、[Microsoft Azure 管理ポータル](https://manage.windowsazure.com)を使用してパスワード管理ポリシーを構成するには、ディレクトリの [**構成**] タブにある [**ユーザー パスワードのリセット ポリシー**] セクションに移動します。この構成ページでは、パスワードが組織内でどのように管理されるかを、以下をはじめとするさまざまな側面から制御できます。

- ディレクトリのユーザー全員に対してパスワードのリセットの有効と無効を切り替える
- パスワードをリセットする前にユーザーがクリアする必要があるチャレンジの数 (1 つまたは 2 つ) を設定する
- 組織内のユーザーに対して有効にするチャレンジを以下のなかから選択して設定する
 - 携帯電話 (テキストによる確認コードまたは音声通話)
 - 会社電話 (音声通話)
 - 代替電子メール (電子メールによる確認コード)
 - セキュリティの質問 (ナレッジ ベースの認証)
- セキュリティの質問の認証方法 (セキュリティの質問が有効な場合のみ表示される) を使用するために、登録する必要がある質問の数を設定する
- セキュリティの質問の認証方法 (セキュリティの質問が有効な場合のみ表示される) を使用するために、リセット時に指定する必要がある質問の数を設定する
- ユーザーがパスワード リセットに登録する際に使用する可能性がある事前に準備されたローカライズ済みのセキュリティに関する質問を使用する (セキュリティの質問が有効になっている場合にのみ表示される)
- ユーザーがパスワード リセットに登録する際に使用する可能性があるカスタムのセキュリティに関する質問を定義する (セキュリティの質問が有効になっている場合にのみ表示される)
- [http://myapps.microsoft.com](http://myapps.microsoft.com) のアプリケーション アクセス パネルへのアクセス時に、パスワード リセットの登録をユーザーに求める
- 設定可能な日数が経過した後、以前登録したデータを再確認することをユーザーに求める (適用されている登録が有効な場合のみ表示される)
- パスワードのリセットに際して問題が発生した場合に表示される、カスタムのヘルプデスクの電子メール アドレスまたは URL を指定する
- パスワード ライトバック機能を有効化または無効化する (AAD Connect を使用してパスワード ライトバックがデプロイされている場合)
- パスワード ライトバック エージェントの状態を表示する (AAD Connect を使用してパスワード ライトバックがデプロイされている場合)
- パスワードがリセットされた場合のユーザーへの電子メール通知を有効化する ([Microsoft Azure 管理ポータル](https://manage.windowsazure.com)の [**通知**] セクションで確認できる)
- 管理者のパスワードを他の管理者がリセットした場合の管理者への電子メール通知を有効化する ([Microsoft Azure 管理ポータル](https://manage.windowsazure.com)の [**通知**] セクション)
- テナント ブランド化のカスタマイズ機能 ([Microsoft Azure 管理ポータル](https://manage.windowsazure.com)の [**ディレクトリのプロパティ**] セクション) を使用して、ユーザー パスワードのリセット ポータルとパスワード リセットの電子メールを組織のロゴおよび名前によりブランド化する

組織内のパスワード管理を構成する方法の詳細については、[概要: Azure AD でのパスワード管理](active-directory-passwords-getting-started.md)を参照してください。

##ユーザー登録ポータル
ユーザーがパスワードをリセットできるようにするには、パスワードのリセットに対して管理者が設定した数のチャレンジをクリアできるように、クラウドのユーザー アカウントを正しい認証データに更新する必要があります。管理者は、Azure Web ポータルまたは Office Web ポータル、DirSync または Azure AD Connect、あるいは Windows PowerShell を使用して、ユーザーの代わりにこの認証情報を定義することもできます。

ただし、ユーザーが自分でデータを登録する方が好ましい場合は、ユーザーがアクセスして情報を登録できる Web ページも用意されています。このページでユーザーは、それぞれの組織で有効になっているパスワード リセット ポリシーに従って認証情報を指定できます。入力されたデータが確認されると、クラウドのユーザー アカウントに保存され、後にアカウントを復旧するときに使用されます。登録ポータルの外観を次に示します。

  ![][001]

詳細については、[概要: Azure AD でのパスワード管理](active-directory-passwords-getting-started.md)および [Best Practices: Azure AD Password Management (ベスト プラクティス: Azure AD でのパスワード管理)](active-directory-passwords-best-practices.md) を参照してください。

##ユーザー パスワードのリセット ポータル
セルフサービスのパスワード リセットを有効にして、セルフサービスのパスワード リセットについて組織のポリシーを設定し、ディレクトリに正しいユーザーの連絡先データが含まれることを確認できたら、企業または学校のアカウントを使用してサインインする Web ページ ([portal.microsoftonline.com](https://portal.microsoftonline.com) など) であればどこからでも、組織内のユーザーがパスワードを自動的にリセットできるようになります。サインインに組織のアカウントを使用するページでは、[**アカウントにアクセスできない場合**] というリンクが表示されます。

  ![][002]

このリンクをクリックすると、セルフサービスのパスワード リセット ポータルが起動します。

  ![][003]

ユーザーが自分のパスワードをリセットする方法の詳細については、 [概要: Azure AD でのパスワード管理](active-directory-passwords-getting-started.md)参照してください。

##ユーザー パスワードの変更ポータル
ユーザーが自分のパスワードを変更したい場合は、パスワード変更ポータルを使用すればいつでも変更できます。パスワード変更ポータルへは、アクセス パネルのプロファイル ページからアクセスできるほか、Office 365 アプリケーション内の [パスワードを変更する] リンクをクリックしてもアクセスできます。パスワードの有効期限が切れた場合は、ユーザーがサインインすると自動的にパスワードの変更を求められます。

  ![][004]

いずれの場合も、パスワード ライトバックが有効で、ユーザーがフェデレーションまたはパスワード同期であれば、これらの変更されたパスワードはオンプレミスの Active Directory にライトバックされます。パスワード変更ポータルの外観を次に示します。

  ![][005]

ユーザーが自分のオンプレミスの Active Directory パスワードを変更する方法の詳細については、[概要: Azure AD でのパスワード管理](active-directory-passwords-getting-started.md)を参照してください。

##パスワード管理レポート
[**レポート**] タブに移動すると、[**アクティビティ ログ**] セクションに、[**パスワード リセット アクティビティ**] および [**パスワード リセット登録アクティビティ**] の 2 つのパスワード管理レポートが表示されます。これら 2 つのレポートを使用すると、組織内でパスワード リセットに登録しているユーザー、およびパスワード リセットを使用しているユーザーのビューを参照できます。[Microsoft Azure 管理ポータル](https://manage.windowsazure.com)でのこれらのレポートの外観を示します。

  ![][006]

詳細については、[Get Insights: Azure AD Password Management Reports (詳細: Azure AD でのパスワード管理のレポート)](active-directory-passwords-get-insights.md) を参照してください。

##Azure AD Connect のパスワード ライトバック コンポーネント
組織内のユーザーのパスワードがオンプレミスの環境 (フェデレーションまたはパスワード同期のいずれか) から発生している場合、Azure AD Connect の最新バージョンをインストールすると、クラウドから直接パスワードを更新できます。これは、ユーザーが AD のパスワードを忘れた場合や変更を希望する場合、Web から直接更新可能だということです。次に、Azure AD Connect インストール ウィザードでパスワード ライトバックを選択できる場所を示します。

  ![][007]

Azure AD Connect の詳細については、[Azure Active Directory Connect](active-directory-aadconnect.md) を参照してください。パスワード ライトバックの詳細については、[概要: Azure AD でのパスワード管理](active-directory-passwords-getting-started.md)を参照してください。


<br/> <br/> <br/>

## パスワードのリセットに関するドキュメントへのリンク
Azure AD のパスワードのリセットに関するすべてのドキュメント ページへのリンクを以下に示します。

* [**自分のパスワードのリセット**](active-directory-passwords-update-your-own-password) - システムのユーザーとして自分のパスワードをリセットまたは変更する方法について説明します。
* [**概要**](active-directory-passwords-getting-started.md) -ユーザーによるクラウドまたはオンプレミスのパスワードのリセットと変更を許可する方法について説明します。
* [**カスタマイズ**](active-directory-passwords-customize.md) - 組織のニーズに合わせてサービスの外観と動作をカスタマイズする方法について説明します。
* [**ベスト プラクティス**](active-directory-passwords-best-practices.md) - 組織内でのパスワードの迅速なデプロイと効果的な管理方法について説明します。
* [**洞察を得る**](active-directory-passwords-get-insights.md) - 統合レポート機能について説明します。
* [**FAQ**](active-directory-passwords-faq.md) -よく寄せられる質問の回答を得ます。
* [**トラブルシューティング**](active-directory-passwords-troubleshoot.md) - サービスに関する問題を迅速にトラブルシューティングする方法について説明します。
* [**詳細情報**](active-directory-passwords-learn-more.md) - サービスの機能の技術的な詳細を掘り下げます。



[001]: ./media/active-directory-passwords-how-it-works/001.jpg "Image_001.jpg"
[002]: ./media/active-directory-passwords-how-it-works/002.jpg "Image_002.jpg"
[003]: ./media/active-directory-passwords-how-it-works/003.jpg "Image_003.jpg"
[004]: ./media/active-directory-passwords-how-it-works/004.jpg "Image_004.jpg"
[005]: ./media/active-directory-passwords-how-it-works/005.jpg "Image_005.jpg"
[006]: ./media/active-directory-passwords-how-it-works/006.jpg "Image_006.jpg"
[007]: ./media/active-directory-passwords-how-it-works/007.jpg "Image_007.jpg"

<!---HONumber=Nov15_HO4-->