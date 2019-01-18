---
title: Azure Active Directory における属性マッピングの式の書き方 | Microsoft Docs
description: Azure Active Directory で SaaS アプリ オブジェクトを自動プロビジョニングしているときに、式マッピングを使用して属性値を許容される形式に変換する方法について説明します。
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: barbkess
ms.openlocfilehash: 867fdd57df163f37d86572798aaae6d78d43f479
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/02/2019
ms.locfileid: "53973725"
---
# <a name="writing-expressions-for-attribute-mappings-in-azure-active-directory"></a>Azure Active Directory における属性マッピングの式の書き方
SaaS アプリケーションに対してプロビジョニングを構成するときに指定できる属性マッピングの種類の 1 つは、式マッピングです。 この場合は、ユーザーのデータを SaaS アプリケーションが許容可能な形式に変換することができる、スクリプトのような式を記述する必要があります。

## <a name="syntax-overview"></a>構文の概要
属性マッピングの式の構文は、Visual Basic のApplications (VBA) 関数に似ています。

* 式全体は、関数の形式で定義する必要があります。名前の後にかっこで囲んだ引数を続けます。 <br>
  *FunctionName(`<<argument 1>>`,`<<argument N>>`)*
* 各関数内で他の関数を入れ子にすることができます。 例:  <br> *FunctionOne(FunctionTwo(`<<argument1>>`))*
* 関数には、次の 3 つの異なる種類の引数を渡すことができます。
  
  1. 属性。角かっこで囲む必要があります。 例: [attributeName]
  2. 文字列定数。二重引用符で囲む必要があります。 例: "米国"
  3. 他の関数 例: FunctionOne(`<<argument1>>`, FunctionTwo(`<<argument2>>`))
* 文字列定数では、文字列に円記号 (\) または引用符 (") を含める必要がある場合は、円記号 (\) でエスケープする必要があります。 例: "会社名:\"Contoso\""

## <a name="list-of-functions"></a>関数の一覧
[Append](#append) &nbsp;&nbsp;&nbsp;&nbsp; [FormatDateTime](#formatdatetime) &nbsp;&nbsp;&nbsp;&nbsp; [Join](#join) &nbsp;&nbsp;&nbsp;&nbsp; [Mid](#mid) &nbsp;&nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp;&nbsp; [NormalizeDiacritics](#normalizediacritics) [Not](#not) &nbsp;&nbsp;&nbsp;&nbsp; [Replace](#replace) &nbsp;&nbsp;&nbsp;&nbsp; [SelectUniqueValue](#selectuniquevalue)&nbsp;&nbsp;&nbsp;&nbsp; [SingleAppRoleAssignment](#singleapproleassignment)&nbsp;&nbsp;&nbsp;&nbsp; [StripSpaces](#stripspaces) &nbsp;&nbsp;&nbsp;&nbsp; [Switch](#switch)

- - -
### <a name="append"></a>Append
**関数:**<br> Append(source, suffix)

**説明:**<br> source 文字列値を受け取り、その末尾に suffix を追加します。

**パラメーター:**<br> 

| Name | 必須/繰り返し | type | メモ |
| --- | --- | --- | --- |
| **source セクション** |必須 |String |通常は、source オブジェクトの属性の名前。 |
| **suffix** |必須 |String |source 値の末尾に追加する文字列。 |

- - -
### <a name="formatdatetime"></a>FormatDateTime
**関数:**<br> FormatDateTime(source, inputFormat, outputFormat)

**説明:**<br> 1 つの形式の日付文字列を受け取り、別の形式に変換します。

**パラメーター:**<br> 

| Name | 必須/繰り返し | type | メモ |
| --- | --- | --- | --- |
| **source セクション** |必須 |String |通常は、source オブジェクトの属性の名前。 |
| **inputFormat** |必須 |String |有効な形式の source 値。 サポートされる形式については、[https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) をご覧ください。 |
| **outputFormat** |必須 |String |出力日付の形式。 |

- - -
### <a name="join"></a>結合
**関数:**<br> Join(separator, source1, source2, …)

**説明:**<br> Join() は Append() によく似ていますが、Join() では複数の **source** 文字列値を 1 つの文字列に結合できます。文字列値は **separator** で区切って指定します。

source 値の 1 つが複数値属性である場合は、その属性のすべての値を結合し、separator 値で区切ります。

**パラメーター:**<br> 

| Name | 必須/繰り返し | type | メモ |
| --- | --- | --- | --- |
| **separator** |必須 |String |source 値を 1 つの文字列に連結するときに、各値を区切るのに使用する文字列。 区切り記号が必要ない場合は、“” とすることができます。 |
| **source1  … sourceN** |必須、回数は可変 |String |結合する文字列値。 |

- - -
### <a name="mid"></a>Mid
**関数:**<br> Mid(source, start, length)

**説明:**<br> source 値の部分文字列を返します。 部分文字列は、source 文字列の文字のみを含む文字列です。

**パラメーター:**<br> 

| Name | 必須/繰り返し | type | メモ |
| --- | --- | --- | --- |
| **source セクション** |必須 |String |通常、属性の名前。 |
| **start** |必須 |integer |部分文字列が始まる **source** 文字列のインデックス。 文字列内の最初の文字のインデックスは 1、2 番目の文字のインデックスは 2です (以降同様)。 |
| **length** |必須 |integer |部分文字列の長さ。 length が **source** 文字列の外で終わる場合は、**start** インデックスから **source** 文字列の末尾までの部分文字列を返します。 |

- - -
### <a name="normalizediacritics"></a>NormalizeDiacritics
**関数:**<br> NormalizeDiacritics(source)

**説明:**<br> 1 つの文字列引数が必要です。 文字列を返しますが、分音記号はそれ以外の同等の記号で置換されます。 通常、分音記号 (アクセント記号) を含む姓と名を、さまざまなユーザー識別子 (ユーザー プリンシパル名、SAM アカウント名、電子メール アドレスなど) で使用できる有効な値に変換するために使用します。

**パラメーター:**<br> 

| Name | 必須/繰り返し | type | メモ |
| --- | --- | --- | --- |
| **source セクション** |必須 |String | 通常は、名または姓の属性 |

- - -
### <a name="not"></a>Not
**関数:**<br>  Not(source)

**説明:**<br> **source** のブール値を反転します。 **source** 値が "*True*" の場合は "*False*" を返します。 "False" の場合は "*True*" を返します。

**パラメーター:**<br> 

| Name | 必須/繰り返し | type | メモ |
| --- | --- | --- | --- |
| **source セクション** |必須 |Boolean String |有効な **source** 値は "True" または "False" です。 |

- - -
### <a name="replace"></a>Replace
**関数:**<br> Replace(source, oldValue, regexPattern, regexGroupName, replacementValue, replacementAttributeName, template)

**説明:**<br>
 文字列内の値を置換します。 指定されたパラメーターに応じて異なる動作をします。

* **oldValue** と **replacementValue** が指定された場合:
  
  * source に含まれるすべての OldValue を replacementValue に置換します。
* **oldValue** と **template** が指定された場合:
  
  * **template** に含まれるすべての **OldValue** を **source** 値に置換します。
* **regexPattern**、**regexGroupName**、**replacementValue** が指定された場合:
  
  * source 文字列に含まれる OldValueRegexPattern に一致するすべての値を replacementValue に置換します。
* **regexPattern**、**regexGroupName**、**replacementPropertyName** が指定された場合:
  
  * **source** に値が指定されていない場合は、**source** を返します。
  * **source** に値が指定されている場合は、**regexPattern** と **regexGroupName** を使用して、**replacementPropertyName** を持つプロパティから置換値を抽出します。 置換値を、結果として返します。

**パラメーター:**<br> 

| Name | 必須/繰り返し | type | メモ |
| --- | --- | --- | --- |
| **source セクション** |必須 |String |通常は、source オブジェクトの属性の名前。 |
| **oldValue** |省略可能 |String |**source** または **template** に含まれる置換前の値。 |
| **regexPattern** |省略可能 |String |**source**に含まれる置換前の値の正規表現パターン。 または、replacementPropertyName を使用するときは、置換プロパティから値を抽出するパターン。 |
| **regexGroupName** |省略可能 |String |**regexPattern**内のグループの名前。 ReplacementPropertyName を使用した場合にのみ、置換プロパティから replacementValue としてこのグループの値を抽出します。 |
| **replacementValue** |省略可能 |String |古い値を置き換える新しい値。 |
| **replacementAttributeName** |省略可能 |String |source に値を指定しないときに、置換値に使用する属性の名前。 |
| **template** |省略可能。 |String |**template** 値が指定されている場合は、template 内の **oldValue** を検索し、source 値で置換します。 |

- - -
### <a name="selectuniquevalue"></a>SelectUniqueValue
**関数:**<br> SelectUniqueValue(uniqueValueRule1, uniqueValueRule2, uniqueValueRule3, …)

**説明:**<br> 少なくとも 2 つの引数が必要です。引数は、式を使用して定義されている一意値生成ルールです。 関数では、各ルールが評価された後、生成された値の対象となるアプリ/ディレクトリでの一意性が確認されます。 最初に見つかった一意の値が返されます。 すべての値がターゲットに既に存在する場合、エントリはエスクローされて、理由が監査ログに記録されます。 渡すことができる引数の数に上限はありません。

> [!NOTE]
>1. これは最上位の関数であり、入れ子にはできません。
>2. この関数は、エントリの作成に使用されることだけを目的としたものです。 属性で使用するときは、**[Apply Mapping]\(マッピングの適用\)** プロパティを **[オブジェクトの作成中のみ]** に設定します。


**パラメーター:**<br> 

| Name | 必須/繰り返し | type | メモ |
| --- | --- | --- | --- |
| **uniqueValueRule1  … uniqueValueRuleN** |2 つ以上必要であり、上限はありません |String | 評価する一意値生成ルールの一覧 |


- - -
### <a name="singleapproleassignment"></a>SingleAppRoleAssignment
**関数:**<br> SingleAppRoleAssignment([appRoleAssignments])

**説明:**<br> 1 つの文字列引数が必要です。 文字列を返しますが、分音記号はそれ以外の同等の記号で置換されます。

**パラメーター:**<br> 

| Name | 必須/繰り返し | type | メモ |
| --- | --- | --- | --- |
| **[appRoleAssignments]** |必須 |String |**[appRoleAssignments]** オブジェクト |

- - -
### <a name="stripspaces"></a>StripSpaces
**関数:**<br>  StripSpaces(source)

**説明:**<br> source 文字列からすべての空白文字 (" ") を削除します。

**パラメーター:**<br> 

| Name | 必須/繰り返し | type | メモ |
| --- | --- | --- | --- |
| **source セクション** |必須 |String |**source セクション セクション** 値。 |

- - -
### <a name="switch"></a>Switch
**関数:**<br> Switch(source, defaultValue, key1, value1, key2, value2, …)

**説明:**<br> **source** 値が **key** と一致するときに、その **key** の **value** を返します。 **source** 値がどの key とも一致しない場合は、**defaultValue** を返します。  **key** と **value** パラメーターは、常にペアで指定する必要があります。 この関数には、常に、偶数個のパラメーターを指定する必要があります。

**パラメーター:**<br> 

| Name | 必須/繰り返し | type | メモ |
| --- | --- | --- | --- |
| **source セクション** |必須 |String |**Source** 値。 |
| **defaultValue** |省略可能 |String |source がどの key とも一致しないときに使用される既定値。 空の文字列 ("") を指定できます。 |
| **key** |必須 |String |**source** 値と比較する **key**。 |
| **値** |必須 |String |key と一致する **source** の置換値。 |

## <a name="examples"></a>例
### <a name="strip-known-domain-name"></a>既知のドメイン名をストリップする
ユーザーの電子メールから既知のドメイン名をストリップして、ユーザー名を取得する必要があります。 <br>
たとえば、ドメインが "contoso.com" の場合は、次の式を使用することができます。

**Expression:** <br>
`Replace([mail], "@contoso.com", , ,"", ,)`

**サンプル入力/出力:** <br>

* **入力** (mail): "john.doe@contoso.com"
* **出力**: "john.doe"

### <a name="append-constant-suffix-to-user-name"></a>ユーザー名に定数のサフィックスを追加する
Salesforce Sandbox を使用している場合は、ユーザー名を同期する前に、すべてのユーザー名に追加のサフィックスを追加する必要があります。

**Expression:** <br>
`Append([userPrincipalName], ".test")`

**サンプル入力/出力:** <br>

* **入力**: (userPrincipalName): "John.Doe@contoso.com"
* **出力**:  "John.Doe@contoso.com.test"

### <a name="generate-user-alias-by-concatenating-parts-of-first-and-last-name"></a>姓の一部と名の一部を連結することでユーザー エイリアスを生成する
ユーザーの名の最初の 3 文字とユーザーの姓の最初の 5 文字を取得することでユーザー エイリアスを生成する必要があります。

**Expression:** <br>
`Append(Mid([givenName], 1, 3), Mid([surname], 1, 5))`

**サンプル入力/出力:** <br>

* **入力** (givenName):"John"
* **入力** (surname):"Doe"
* **出力**:"JohDoe"

### <a name="remove-diacritics-from-a-string"></a>文字列から分音記号を削除する
アクセント記号を含む文字を、アクセント記号を含まない同等の文字に置換する必要があります。

**Expression:** <br>
NormalizeDiacritics([givenName])

**サンプル入力/出力:** <br>

* **入力** (givenName):"Zoë"
* **出力**:"Zoe"

### <a name="output-date-as-a-string-in-a-certain-format"></a>特定の形式の文字列として日付を出力する

SaaS アプリケーションに特定の形式で日付を送信します。 <br>
たとえば、ServiceNow 向けに日付の書式を設定します。

**Expression:** <br>

`FormatDateTime([extensionAttribute1], "yyyyMMddHHmmss.fZ", "yyyy-MM-dd")`

**サンプル入力/出力:**

* **入力** (extensionAttribute1):"20150123105347.1Z"
* **出力**:"2015-01-23"

### <a name="replace-a-value-based-on-predefined-set-of-options"></a>定義済みのオプション セットに基づいて値を置換する

Azure AD に格納されている都道府県コードに基づいて、ユーザーのタイム ゾーンを定義する必要があります。 <br>
都道府県コードが定義済みオプションのいずれにも一致しない場合は、既定値 "Australia/Sydney" を使用します。

**Expression:** <br>

`Switch([state], "Australia/Sydney", "NSW", "Australia/Sydney","QLD", "Australia/Brisbane", "SA", "Australia/Adelaide")`

**サンプル入力/出力:**

* **入力** (state):"QLD"
* **出力**:"Australia/Brisbane"

### <a name="generate-unique-value-for-userprincipalname-upn-attribute"></a>userPrincipalName (UPN) 属性用に一意の値を生成する

ユーザーの名、ミドル ネーム、姓を基にして UPN 属性の値を生成し、値を UPN 属性に割り当てる前に、対象の AD ディレクトリで値が一意であることを確認する必要があります。

**Expression:** <br>

    SelectUniqueValue( 
        Join("@", NormalizeDiacritics(StripSpaces(Join(".",  [PreferredFirstName], [PreferredLastName]))), "contoso.com"), 
        Join("@", NormalizeDiacritics(StripSpaces(Join(".",  Mid([PreferredFirstName], 1, 1), [PreferredLastName]))), "contoso.com")
        Join("@", NormalizeDiacritics(StripSpaces(Join(".",  Mid([PreferredFirstName], 1, 2), [PreferredLastName]))), "contoso.com")
    )

**サンプル入力/出力:**

* **入力** (PreferredFirstName):"John"
* **入力** (PreferredLastName):"Smith"
* **出力**: John.Smith@contoso.com の UPN 値がディレクトリにまだ存在しない場合は "John.Smith@contoso.com"
* **出力**: John.Smith@contoso.com の UPN 値がディレクトリに既に存在する場合は "J.Smith@contoso.com"
* **出力**: 上記の 2 つの UPN 値がディレクトリに既に存在する場合は "Jo.Smith@contoso.com"

## <a name="related-articles"></a>関連記事
* [Azure Active Directory による SaaS アプリへのユーザー プロビジョニングとプロビジョニング解除の自動化](user-provisioning.md)
* [ユーザーのプロビジョニング用の属性マッピングのカスタマイズ](customize-application-attributes.md)
* [ユーザーのプロビジョニング用のフィルターのスコープ](define-conditional-rules-for-provisioning-user-accounts.md)
* [SCIM を使用して、Azure Active Directory からアプリケーションへのユーザーとグループの自動プロビジョニングを有効にする](use-scim-to-provision-users-and-groups.md)
* [アカウント プロビジョニング通知](user-provisioning.md)
* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](../saas-apps/tutorial-list.md)

