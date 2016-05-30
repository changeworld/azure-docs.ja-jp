<properties
	pageTitle="Azure Active Directory のパスワード ポリシーと制限 | Microsoft Azure"
	description="Azure Active Directory のパスワードに適用されるポリシー (使用できる文字、長さ、有効期限など) について説明します"
  services="active-directory"
	documentationCenter=""
	authors="curtand"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/16/2016"
	ms.author="curtand"/>


# Azure Active Directory のパスワード ポリシーと制限

この記事では、Azure AD ディレクトリに格納されているユーザー アカウントと関連付けられたパスワード ポリシーと複雑さの要件について説明します。

## すべてのユーザー アカウントに適用される UserPrincipalName ポリシー

Azure AD 認証システムにサインインする必要があるすべてのユーザー アカウントは、一意のユーザー プリンシパル名 (UPN) 属性値がそのアカウントに関連付けられている必要があります。次の表は、オンプレミス Active Directory ソースのユーザー アカウント (クラウドと同期) とクラウドのみのユーザー アカウントの両方に適用されるポリシーの概要です。

| プロパティ | UserPrincipalName の要件 |
|   ----------------------- |   ----------------------- |
| 使用できる文字 | <ul> <li>A – Z</li> <li>a -z </li><li>0 – 9</li> <li> . - \_ ! # ^ ~</li></ul> |
| 使用できない文字 | <ul> <li>@</li> <li>’@’ 記号の直前のピリオド文字 ('.')</li></ul> |
| 長さの制限 | <ul> <li>合計の長さが 113 文字以下</li><li>‘@’ 記号の前が 64 文字以下</li><li>‘@’ 記号の後が 48 文字以下</li></ul>

## クラウド ユーザー アカウントにのみ適用されるパスワード ポリシー

次の表は、Azure AD で作成し、管理しているユーザー アカウントに適用できるパスワード ポリシー設定の説明です。

| プロパティ | 必要条件 |
|   ----------------------- |   ----------------------- |
| 使用できる文字 | <ul><li>A – Z</li><li>a -z </li><li>0 – 9</li> <li>@ # $ % ^ & * - \_ ! + = [ ] { } | \\ : ‘ , . ? / ` ~ “ ( ) ;</li></ul> |
| 使用できない文字 | <ul><li>Unicode 文字</li><li>スペース</li><li>スペース</li><li> **強力なパスワードのみ**: ’@’ 記号の直前のピリオド文字 ('.') を含めることはできません</li></ul> |
| パスワードの制限 | <ul><li>8 文字以上、16 文字以下</li><li>**強力なパスワードのみ**: 次の 4 つのうち 3 つが必要です<ul><li>小文字の英字</li><li>大文字の英字</li><li>数字 (0-9)</li><li>記号 (上記のパスワードの制限を参照してください)</li></ul></li></ul> |
| パスワードの有効期間 | <ul><li>既定値: **90** 日 </li><li>値を構成するには、Windows PowerShell の Azure Active Directory モジュールから Set-MsolPasswordPolicy コマンドレットを使用します。</li></ul> |
| パスワードの期限切れの通知 | <ul><li>既定値: **14** 日 (パスワードの有効期限が切れるまでの日数)</li><li>値を構成するには、Set-MsolPasswordPolicy コマンドレットを使用します。</li></ul> |
| パスワードの期限切れ | <ul><li>既定値: **false** 日 (パスワードの有効期限が有効になっていることを示します) </li><li>各ユーザー アカウントの値を構成するには、Set-MsolUser コマンドレットを使用します。</li></ul> |
| パスワードの履歴 | 前回のパスワードは再使用できません。 |
| パスワード履歴の期間 | 無期限 |
| アカウントのロックアウト | 正しくないパスワードを入力してサインイン試行に 10 回失敗したユーザーを、1 分間ロックアウトします。不適切なサインイン試行をさらに行った場合は、ロックアウトの期間が延長されます。 |


## 次のステップ

* [任意の場所からのパスワードの管理](active-directory-passwords.md)
* [パスワード管理のしくみ](active-directory-passwords-how-it-works.md)
* [パスワード管理の概要](active-directory-passwords-getting-started.md)
* [パスワード管理のカスタマイズ](active-directory-passwords-customize.md)
* [パスワード管理のベスト プラクティス](active-directory-passwords-best-practices.md)
* [パスワード管理レポートで運用情報を把握する方法](active-directory-passwords-get-insights.md)
* [パスワード管理に関する FAQ](active-directory-passwords-faq.md)
* [パスワード管理のトラブルシューティング](active-directory-passwords-troubleshoot.md)
* [詳細情報](active-directory-passwords-learn-more.md)

<!---HONumber=AcomDC_0518_2016-->