
<properties
	pageTitle="属性を使用した高度なルールの作成| Microsoft Azure"
	description="セキュリティ グループの管理方法とそれらのグループを使用してリソースへのアクセスを管理する方法を上級者向けに説明しています。"
	services="active-directory"
	documentationCenter=""
	authors="femila"
	manager="swadhwa"
	editor=""/>

<tags
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/29/2015" 
	ms.author="femila"/>


# 属性を使用した高度なルールの作成
Azure 管理ポータルでは高度なルールを臨機応変に設定し、グループのメンバーシップを動的に管理することができます。

**高度なルールを作成するには** Azure 管理ポータルのグループの **[構成]** タブで、**[高度なルール]** オプション ボタンをオンにし、用意されているテキスト ボックスに目的のルールを入力します。高度なルールの作成には、以下の情報を使用できます。

## 高度なルール本体の作成
グループの動的なメンバーシップ管理を目的として作成される高度なルールは基本的に、3 つの構成要素から成る、true または false を結果として返す 2 項演算式です。その 3 つの構成要素を次に示します。

- 左辺のパラメーター
- 2 項演算子
- 右辺の定数 

たとえば全体で見ると、(leftParameter binaryOperator "RightConstant") のようになります。2 項演算式全体を開きかっこと閉じかっこで囲んだうえで、右辺の定数は二重引用符で囲む必要があります。左辺のパラメーターの構文は user.property という形式で入力します。高度なルールは、複数の 2 項演算式を論理演算子 (-and、-or、-not) で組み合わせることができます。以下に示したのは、正しい構文に沿って作成された高度なルールの例です。

- (user.department -eq "Sales") -or (user.department -eq "Marketing") 
- (user.department -eq "Sales") -and -not (user.jobTitle -contains "SDE") 

サポートされているパラメーターと式のルール演算子の全一覧については、以降のセクションを参照してください。

高度なルール本体の合計文字数が 255 文字を超えないようにしてください。
> [AZURE.NOTE]文字列演算と正規表現演算は、大文字と小文字が区別されません。定数に $null を使用することで Null チェックを実行することもできます (例: user.department -eq $null)。二重引用符 (") を含んだ文字列は、バック クォート文字 (`) でエスケープする必要があります (例: user.department -eq "Sa`"les")。

##サポートされている式のルール演算子
次の表に、高度なルール本体で使用できる、サポートされているすべての式のルール演算子とその構文を示します。

| 演算子 | 構文 |
|-----------------|----------------|
| 等しくない | -ne |
| 等しい | -eq |
| 指定値で始まらない | -notStartsWith |
| 指定値で始まる | -startsWith |
| 指定値を含まない | -notContains |
| 指定値を含む | -contains |
| 一致しない | -notMatch |
| 一致する | -match |


| クエリ解析エラー | 間違った使用法 | 修正後 |
|----------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| エラー: 属性がサポートされていません。 | (user.invalidProperty -eq "Value") | (user.department -eq "value")プロパティは、サポートされているプロパティの一覧に示したいずれかのプロパティと一致している必要があります。 |
| エラー: 属性で演算子がサポートされていません。 | (user.accountEnabled -contains true) | (user.accountEnabled -eq true)プロパティはブール型です。前掲の一覧からブール型でサポートされている演算子 (-eq または -ne) を使用してください。 |
| エラー: クエリ コンパイル エラー。 | (user.department -eq "Sales") -and (user.department -eq "Marketing")(user.userPrincipalName -match "\*@domain.ext") | (user.department -eq "Sales") -and (user.department -eq "Marketing")論理演算子は、サポートされているプロパティ一覧のいずれかと一致している必要があります。(user.userPrincipalName -match ".\*@domain.ext") または (user.userPrincipalName -match "@domain.ext$") 正規表現の誤り。 |
| エラー: バイナリ式の形式が正しくありません。 | (user.department –eq “Sales”) (user.department -eq "Sales")(user.department-eq"Sales") | (user.accountEnabled -eq true) -and (user.userPrincipalName -contains "alias@domain")クエリにいくつかの誤りが存在します。かっこの位置が正しくありません。 |
| エラー: 動的メンバーシップの設定中に不明なエラーが発生しました。 | (user.accountEnabled -eq "True" AND user.userPrincipalName -contains "alias@domain") | (user.accountEnabled -eq true) -and (user.userPrincipalName -contains "alias@domain")クエリにいくつかの誤りが存在します。かっこの位置が正しくありません。 |

##サポートされているパラメーター
高度なルールで使用できるすべてのユーザー プロパティを次に示します。

**ブール型のプロパティ**

使用可能な演算子

* -eq


* -ne


| プロパティ | 使用できる値 | 使用法 |
|----------------|-----------------|--------------------------------|
| accountEnabled | true false | user.accountEnabled -eq true) |
| dirSyncEnabled | true false null | (user.dirSyncEnabled -eq true) |

**文字列型のプロパティ**

使用可能な演算子

* -eq


* -ne


* -notStartsWith


* -StartsWith


* -contains


* -notContains


* -match


* -notMatch

| プロパティ | 使用できる値 | 使用法 |
|----------------------------|-------------------------------------------------------------------------------------------------------|-----------------------------------------------------------|
| city | 任意の文字列値または $null。 | (user.city -eq "value") |
| country | 任意の文字列値または $null。 | (user.country -eq "value") |
| department | 任意の文字列値または $null。 | (user.department -eq "value") |
| displayName | 任意の文字列値。 | (user.displayName -eq "value") |
| facsimileTelephoneNumber | 任意の文字列値または $null。 | (user.facsimileTelephoneNumber -eq "value") |
| givenName | 任意の文字列値または $null。 | (user.givenName -eq "value") |
| jobTitle | 任意の文字列値または $null。 | (user.jobTitle -eq "value") |
| mail | 任意の文字列値または $null。ユーザーの SMTP アドレス。 | (user.mail -eq "value") |
| mailNickName | 任意の文字列値。ユーザーのメール エイリアス。 | (user.mailNickName -eq "value") |
| mobile | 任意の文字列値または $null。 | (user.mobile -eq "value") |
| objectId | ユーザー オブジェクトの GUID | (user.objectId -eq "1111111-1111-1111-1111-111111111111") |
| passwordPolicies | なし DisableStrongPassword DisablePasswordExpiration DisablePasswordExpiration、DisableStrongPassword | (user.passwordPolicies -eq "DisableStrongPassword") |
| physicalDeliveryOfficeName | 任意の文字列値または $null。 | (user.physicalDeliveryOfficeName -eq "value") |
| postalCode | 任意の文字列値または $null。 | (user.postalCode -eq "value") |
| preferredLanguage | ISO 639-1 コード | (user.preferredLanguage -eq "en-US") |
| sipProxyAddress | 任意の文字列値または $null。 | (user.sipProxyAddress -eq "value") |
| state | 任意の文字列値または $null。 | (user.state -eq "value") |
| streetAddress | 任意の文字列値または $null。 | (user.streetAddress -eq "value") |
| surname | 任意の文字列値または $null。 | (user.surname -eq "value") |
| telephoneNumber | 任意の文字列値または $null。 | (user.telephoneNumber -eq "value") |
| usageLocation | 2 文字の国コード | (user.usageLocation -eq "US") |
| userPrincipalName | 任意の文字列値。 | (user.userPrincipalName -eq "alias@domain") |
| userType | member guest $null | (user.userType -eq "Member") |

**文字列コレクション型のプロパティ**

使用可能な演算子

* -contains


* -notContains

| プロパティ | 使用できる値 | 使用法 |
|----------------|---------------------------------------|------------------------------------------------------|
| otherMails | 任意の文字列値 | (user.otherMails -contains "alias@domain") |
| proxyAddresses | SMTP: alias@domain smtp: alias@domain | (user.proxyAddresses -contains "SMTP: alias@domain") |

## 直接の部下のルール
ユーザーのマネージャー属性に基づいてグループにメンバーを設定できるようになりました。
"Manager" グループとしてグループを構成するには
--------------------------------------------------------------------------------
1. 管理者ポータルで、**[構成]** タブをクリックし、**[高度なルール]** を選択します。 
2. 次の直属の部下の構文を使用してルールを入力します。*Direct Reports for {UserID\_of\_manager}*
3. このルールを保存すると、ルールに該当するすべてのユーザーがグループのメンバーとして結合されます。最初は、グループを設定するのに数分かかることがあることに注意してください。 


## 追加情報
ここでは、Azure Active Directory の追加情報を提供するいくつかのトピックを紹介します。

* [グループの動的メンバーシップのトラブルシューティング](active-directory-accessmanagement-troubleshooting.md)

* [Azure Active Directory グループによるリソースへのアクセス管理](active-directory-manage-groups.md)

* [Azure Active Directory とは](active-directory-whatis.md)

* [オンプレミス ID と Azure Active Directory の統合](active-directory-aadconnect.md)

<!---HONumber=July15_HO5-->