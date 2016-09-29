
<properties
	pageTitle="属性を使用して Azure Active Directory プレビューのグループ メンバーシップの高度なルールを作成する | Microsoft Azure"
	description="サポートされている式のルールの演算子とパラメーターを含む、動的グループ メンバーシップの高度なルールを作成する方法。"
	services="active-directory"
	documentationCenter=""
	authors="curtand"
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


# 属性を使用して Azure Active Directory プレビューのグループ メンバーシップの高度なルールを作成する

Azure ポータルでは、Azure Active Directory (Azure AD) プレビュー グループの複雑な属性ベースの動的メンバーシップを有効にする高度なルールを作成できます。プレビューの機能については、[こちらの記事](active-directory-preview-explainer.md)をご覧ください。 この記事では、これらの高度なルールを作成するためのルールの属性と構文について詳しく説明します。

## 高度なルールを作成するには

1.  ディレクトリの全体管理者であるアカウントで [Azure ポータル](https://portal.azure.com)にサインインします。

2.  **[その他のサービス]** を選択し、テキスト ボックスに「**ユーザーとグループ**」と入力して、**Enter** キーを押します。

  ![ユーザー管理を開く](./media/active-directory-groups-dynamic-membership-azure-portal/search-user-management.png)

3.  **[ユーザーとグループ]** ブレードで、**[すべてのグループ]** を選択します。

  ![グループ ブレードを開く](./media/active-directory-groups-dynamic-membership-azure-portal/view-groups-blade.png)

4. **[ユーザーとグループ - すべてのグループ]** ブレードで、**[追加]** をクリックします。

  ![新しいグループを追加する](./media/active-directory-groups-dynamic-membership-azure-portal/add-group-type.png)

5. **[グループ]** ブレードで、新しいグループの名前と説明を入力します。**[メンバーシップの種類]** で、ユーザーとデバイスのどちらのルールを作成するかに応じて、**[Dynamic User (動的ユーザー)]** または **[Dynamic Device (動的デバイス)]** を選択し、**[Add dynamic query (動的クエリの追加)]** をクリックします。デバイスのルールに使用する属性については、「[属性を使用したデバイス オブジェクトのルールの作成](#using-attributes-to-create-rules-for-device-objects)」をご覧ください。

  ![動的メンバーシップのルールを追加する](./media/active-directory-groups-dynamic-membership-azure-portal/add-dynamic-group-rule.png)

6. **[Dynamic membership rules (動的メンバーシップのルール)]** ブレードで、**[Add dynamic membership advanced rule (動的メンバーシップの高度なルールの追加)]** ボックスにルールを入力し、ブレードの下部にある **[作成]** をクリックします。

7. **[グループ]** ブレードの **[作成]** をクリックして、グループを作成します。

## 高度なルール本体の作成

グループの動的なメンバーシップ管理を目的として作成される高度なルールは基本的に、3 つの構成要素から成る、true または false を結果として返す 2 項演算式です。その 3 つの構成要素を次に示します。

- 左辺のパラメーター
- 2 項演算子
- 右辺の定数

たとえば全体で見ると、(leftParameter binaryOperator "RightConstant") のようになります。2 項演算式全体を開きかっこと閉じかっこで囲んだ上で、右辺の定数は二重引用符で囲む必要があります。左辺のパラメーターの構文は user.property という形式で入力します。高度なルールは、複数の 2 項演算式を論理演算子 (-and、-or、-not) で組み合わせることができます。

以下に示したのは、正しい構文に沿って作成された高度なルールの例です。

- (user.department -eq "Sales") -or (user.department -eq "Marketing")
- (user.department -eq "Sales") -and -not (user.jobTitle -contains "SDE")

サポートされているパラメーターと式のルール演算子の全一覧については、以降のセクションを参照してください。デバイスのルールに使用する属性については、「[属性を使用したデバイス オブジェクトのルールの作成](#using-attributes-to-create-rules-for-device-objects)」をご覧ください。

高度なルール本体の合計文字数が 2048 文字を超えないようにしてください。

> [AZURE.NOTE]
文字列演算と正規表現演算は、大文字と小文字が区別されません。定数に $null を使用することで Null チェックを実行することもできます (例: user.department -eq $null)。二重引用符 (") を含んだ文字列は、バック クォート文字 (`) でエスケープする必要があります (例: user.department -eq `"Sales")。

## サポートされている式のルール演算子
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


## クエリ エラーの修復
次の表では、発生する可能性のあるエラーとその解決方法を示します。

| クエリ解析エラー | 間違った使用法 | 修正後 |
|-----------------------|-------------------|-----------------------------|
| エラー: 属性がサポートされていません。 | (user.invalidProperty -eq "Value") | (user.department -eq "value")<br/>プロパティは、[サポートされているプロパティの一覧](#supported-properties)のいずれかと一致している必要があります。 |
| エラー: 属性で演算子がサポートされていません。 | (user.accountEnabled -contains true) | (user.accountEnabled -eq true)<br/>プロパティはブール型です。前掲の一覧からブール型でサポートされている演算子 (-eq または -ne) を使用してください。 |
| エラー: クエリ コンパイル エラー。 | (user.department -eq "Sales") -and (user.department -eq "Marketing")(user.userPrincipalName -match "*@domain.ext") | (user.department -eq "Sales") -and (user.department -eq "Marketing")<br/>論理演算子は、サポートされているプロパティ一覧のいずれかと一致している必要があります。(user.userPrincipalName -match ".*@domain.ext") または (user.userPrincipalName -match "@domain.ext$") は正規表現ではエラーになります。 |
| エラー: バイナリ式の形式が正しくありません。 | (user.department –eq “Sales”) (user.department -eq "Sales")(user.department-eq"Sales") | (user.accountEnabled -eq true) -and (user.userPrincipalName -contains "alias@domain")<br/>クエリにいくつかの誤りが存在します。かっこの位置が正しくありません。 |
| エラー: 動的メンバーシップの設定中に不明なエラーが発生しました。 | (user.accountEnabled -eq "True" AND user.userPrincipalName -contains "alias@domain") | (user.accountEnabled -eq true) -and (user.userPrincipalName -contains "alias@domain")<br/>クエリにいくつかの誤りが存在します。かっこの位置が正しくありません。 |

## サポートされているプロパティ
高度なルールで使用できるすべてのユーザー プロパティを次に示します。

### ブール型のプロパティ

使用可能な演算子

* -eq


* -ne


| プロパティ | 使用できる値 | 使用法 |
|----------------|-----------------|--------------------------------|
| accountEnabled | true false | user.accountEnabled -eq true) |
| dirSyncEnabled | true false null | (user.dirSyncEnabled -eq true) |

### 文字列型のプロパティ

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
| city | 任意の文字列値または $null | (user.city -eq "value") |
| country | 任意の文字列値または $null | (user.country -eq "value") |
| department | 任意の文字列値または $null | (user.department -eq "value") |
| displayName | 任意の文字列値 | (user.displayName -eq "value") |
| facsimileTelephoneNumber | 任意の文字列値または $null | (user.facsimileTelephoneNumber -eq "value") |
| givenName | 任意の文字列値または $null | (user.givenName -eq "value") |
| jobTitle | 任意の文字列値または $null | (user.jobTitle -eq "value") |
| mail | 任意の文字列値または $null (ユーザーの SMTP アドレス) | (user.mail -eq "value") |
| mailNickName | 任意の文字列値 (ユーザーのメール エイリアス) | (user.mailNickName -eq "value") |
| mobile | 任意の文字列値または $null | (user.mobile -eq "value") |
| objectId | ユーザー オブジェクトの GUID | (user.objectId -eq "1111111-1111-1111-1111-111111111111") |
| passwordPolicies | なし DisableStrongPassword DisablePasswordExpiration DisablePasswordExpiration、DisableStrongPassword | (user.passwordPolicies -eq "DisableStrongPassword") |
| physicalDeliveryOfficeName | 任意の文字列値または $null | (user.physicalDeliveryOfficeName -eq "value") |
| postalCode | 任意の文字列値または $null | (user.postalCode -eq "value") |
| preferredLanguage | ISO 639-1 コード | (user.preferredLanguage -eq "ja-JP") |
| sipProxyAddress | 任意の文字列値または $null | (user.sipProxyAddress -eq "value") |
| state | 任意の文字列値または $null | (user.state -eq "value") |
| streetAddress | 任意の文字列値または $null | (user.streetAddress -eq "value") |
| surname | 任意の文字列値または $null | (user.surname -eq "value") |
| telephoneNumber | 任意の文字列値または $null | (user.telephoneNumber -eq "value") |
| usageLocation | 2 文字の国コード | (user.usageLocation -eq "US") |
| userPrincipalName | 任意の文字列値 | (user.userPrincipalName -eq "alias@domain") |
| userType | member guest $null | (user.userType -eq "Member") |

### 文字列コレクション型のプロパティ

使用可能な演算子

* -contains


* -notContains

| プロパティ | 使用できる値 | 使用法 |
|----------------|---------------------------------------|------------------------------------------------------|
| otherMails | 任意の文字列値 | (user.otherMails -contains "alias@domain") |
| proxyAddresses | SMTP: alias@domain smtp: alias@domain | (user.proxyAddresses -contains "SMTP: alias@domain") |

## 拡張属性とカスタム属性
拡張属性とカスタム属性は、動的なメンバーシップ ルールでサポートされます。

拡張属性はオンプレミスの Window Server AD から同期され、"ExtensionAttributeX" という形式です (X は 1 ～ 15)。拡張属性を使用するルールの例を次に示します。

(user.extensionAttribute15 -eq "Marketing")

カスタム属性はオンプレミスの Windows Server AD または接続された SaaS アプリケーションから同期され、"user.extension_[GUID]\__[Attribute]" という形式です。[GUID] は AAD で属性を作成したアプリケーションの AAD での一意の識別子、[Attribute] は作成された属性の名前です。カスタム属性を使用するルールの例を次に示します

user.extension\_c272a57b722d4eb29bfe327874ae79cb\_\_OfficeNumber

カスタム属性名は、Graph Explorer を使用してユーザーの属性をクエリして属性名を検索することにより、ディレクトリで見つけることができます。

## 直接の部下のルール
ユーザーのマネージャー属性に基づいてグループにメンバーを設定できるようになりました。

**"Manager" グループとしてグループを構成するには**

1. 「[高度なルールを作成するには](#to-create-the-advanced-rule)」の手順 1. ～ 5. に従い、**[メンバーシップの種類]** で **[Dynamic User (動的ユーザー)]** を選択します。

2. **[Dynamic membership rules (動的メンバーシップのルール)]** ブレードで、次の構文を使用してルールを入力します。

	直属の部下用の場合は、*Direct Reports for {obectID\_of\_manager}* です。直属の部下の有効なルールの例を示します。

					Direct Reports for "62e19b97-8b3d-4d4a-a106-4ce66896a863”

	ここで、"62e19b97-8b3d-4d4a-a106-4ce66896a863" はマネージャーのオブジェクト ID を示しています。オブジェクト ID は、Azure AD にある、マネージャーであるユーザーのユーザー ページの **[プロファイル] タブ**で確認できます。

3. このルールを保存すると、ルールに該当するすべてのユーザーがグループのメンバーとして結合されます。最初は、グループを設定するのに数分かかることがあります。


## 属性を使用したデバイス オブジェクトのルールの作成

グループのメンバーシップのデバイス オブジェクトを選択するルールを作成することもできます。次のデバイス属性を使用できます。

| プロパティ | 使用できる値 | 使用法 |
|----------------------|---------------------------------|------------------------------------------------------|
| displayName | 任意の文字列値 | (device.displayName -eq "Rob Iphone”) |
| deviceOSType | 任意の文字列値 | (device.deviceOSType -eq "IOS") |
| deviceOSVersion | 任意の文字列値 | (device.OSVersion -eq "9.1") |
| isDirSynced | true false null | (device.isDirSynced -eq "true") |
| isManaged | true false null | (device.isManaged -eq "false") |
| isCompliant | true false null | (device.isCompliant -eq "true") |


## 追加情報
次の記事は、Azure Active Directory のグループに関する追加情報を提供します。

* [既存のグループの表示](active-directory-groups-view-azure-portal.md)
* [新しいグループの作成とメンバーの追加](active-directory-groups-create-azure-portal.md)
* [グループの設定の管理](active-directory-groups-settings-azure-portal.md)
* [グループのメンバーシップの管理](active-directory-groups-membership-azure-portal.md)
* [グループ内のユーザーの動的ルールの管理](active-directory-groups-dynamic-membership-azure-portal.md)

<!---HONumber=AcomDC_0914_2016-->