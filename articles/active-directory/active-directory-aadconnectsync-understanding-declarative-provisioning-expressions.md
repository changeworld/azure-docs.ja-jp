<properties
	pageTitle="Azure AD Connect Sync: 宣言型のプロビジョニングの式について | Microsoft Azure"
	description="宣言型のプロビジョニングの式について説明します。"
	services="active-directory"
	documentationCenter=""
	authors="andkjell"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/23/2016"
	ms.author="markusvi;andkjell"/>


# Azure AD Connect Sync: 宣言型のプロビジョニングの式について
Azure AD Connect Sync は、Forefront Identity Manager 2010 で初めて導入された宣言型のプロビジョニングに基づいています。これにより、コンパイル済みコードを記述することなく完全な ID 統合ビジネス ロジックを実装できます。

宣言型のプロビジョニングの不可欠な要素は、属性フローに使用される式言語です。使用される言語は、Microsoft ® Visual Basic ® for Applications (VBA) のサブセットです。この言語は、Microsoft Office で使用され、VBScript の経験を持つユーザーも理解できます。宣言型のプロビジョニングの式言語は、関数を使用するだけであり、構造化言語ではありません。メソッドやステートメントはありません。代わりに、関数を入れ子にして、プログラム フローを記述します。

詳細については、「[Office 2013 Visual Basic for Applications 言語リファレンスへようこそ](https://msdn.microsoft.com/library/gg264383.aspx)」を参照してください。

属性は厳密に型指定されます。関数は、正しい型の属性のみを受け取ります。また、大文字と小文字が区別されます。関数名と属性名のいずれも、適切に大文字と小文字を使用する必要があります。そのようにしない場合、エラーがスローされます。

## 言語の定義と識別子

- 関数は、名前の後にかっこで囲まれた引数が続きます。たとえば、FunctionName(argument 1,argument N) などです。
- 属性は、角かっこで識別されます。たとえば、[attributeName] などです。
- パラメーターは、パーセント記号で識別されます。たとえば、%ParameterName% などです。
- 文字列定数は、引用符で囲まれます。たとえば、"Contoso" などです (注: 左右区別がある引用符 “” ではなく、左右区別がない引用符 "" を使用する必要があります)。
- 数値は、引用符を使用しないで表され、10 進数であることが求められます。16 進数の値には、&H がプレフィックスとして付加されます。たとえば、98052、&HFF などです。
- ブール値は、定数 (True、False) で表されます。
- 組み込み定数とリテラルは、その名前のみで表されます。たとえば、NULL、CRLF、IgnoreThisFlow などです。

### 関数
宣言型のプロビジョニングでは、属性値を変換する機能を持つ多くの関数を使用します。これらの関数を入れ子にすると、ある関数の結果を別の関数に渡すことができます。

`Function1(Function2(Function3()))`

全関数の一覧については、[関数参照](active-directory-aadconnectsync-functions-reference.md)を参照してください。

### パラメーター
パラメーターは、コネクタによって定義されるか、PowerShell を使用する管理者によって定義されます。通常、パラメーターには、ユーザーが属しているドメインの名前など、システムによって異なる値が含まれます。これらのパラメーターは属性フローで使用できます。

Active Directory Connector は、受信同期ルールについて次のパラメーターを提供しています。

| パラメーター名 | コメント |
| --- | --- |
| Domain.Netbios | 現在インポートされているドメインの NetBIOS 形式 (FABRIKAMSALES など) |
| Domain.FQDN | 現在インポートされているドメインの FQDN 形式 (sales.fabrikam.com など) |
| Domain.LDAP | 現在インポートされているドメインの LDAP 形式 (DC=sales,DC=fabrikam,DC=com など) |
| Forest.Netbios | 現在インポートされているフォレスト名の NetBIOS 形式 (FABRIKAMCORP など) |
| Forest.FQDN | 現在インポートされているフォレスト名の FQDN 形式 (fabrikam.com など) |
| Forest.LDAP | 現在インポートされているフォレスト名の LDAP 形式 (DC=fabrikam,DC=com など) |

システムには次のパラメーターが用意されています。現在実行中のコネクタの識別子を取得するために使用されます。`Connector.ID`

ユーザーが属しているドメインの NetBIOS 名をメタバース属性ドメインに取り込む例を次に示します。`domain` <- `%Domain.Netbios%`

### 演算子
次の演算子を使用できます。

- **比較**: <、<=、<>、=、>、>=
- **算術**: +、-、*、-
- **文字列**: & (連結)
- **論理**: && (AND)、|| (OR)
- **評価順序**: ( )

演算子は左から右に評価されます。評価の優先順位は同じです。つまり、* (乗算) は - (減算) よりも先に評価されません。2*(5+3) は、2*5+3 と同じではありません。かっこ () は、左から右への評価順が適切ではない場合に、評価順を変更するために使用されます。

## 複数値の属性

### 複数値の属性の属性フロー
関数は、単一値の属性と複数値の属性両方に対して使用できます。複数値の属性の場合、関数は、すべての値で動作し、すべての値に同じ関数を適用します。

次に例を示します。`Trim([proxyAddresses])` proxyAddress 属性の各値の Trim を実行します。`Word([proxyAddresses],1,"@") & "@contoso.com"` アット マークを含むすべての文字列では、ドメインを @contoso.com に置き換えます。`IIF(InStr([proxyAddresses],"SIP:")=1,NULL,[proxyAddresses])` SIP アドレスを検索し、値から削除します。

### 属性値の結合
属性フローには、複数値の属性をいくつかの異なるコネクタから結合する必要があるかどうかを判断する設定があります。既定値は **Update** です。これは、優先順位の最も高い同期規則が優先的に実行されることを示します。

![Merge Types](./media/active-directory-aadconnectsync-understanding-declarative-provisioning-expressions/mergetype.png)

**Merge** と **MergeCaseInsensitive** もあります。これらのオプションを使用すると、異なるソースの値を結合できます。たとえば、それを使用して、いくつかの異なるフォレストのメンバー属性や proxyAddresses 属性を結合できます。このオプションを使用する場合、オブジェクトのスコープ内にあるすべての同期規則では、同じ結合の種類を使用する必要があります。あるコネクタから **Update** を定義し、別のコネクタから **Merge** を定義することはできません。試した場合は、エラーが発生します。

**Merge** と **MergeCaseInsensitive** では、重複する属性値の処理方法が異なります。同期エンジンは、重複する値がターゲット属性に挿入されていないことを確認します。**MergeCaseInsensitive** を使用すると、大文字小文字のみが異なる重複する値は表示されなくなります。たとえば、"SMTP:bob@contoso.com" と "smtp:bob@contoso.com" は両方ともターゲット属性に表示されなくなります。**Merge** は、正確な値だけを調べるため、大文字小文字のみが異なる複数の値は表示される可能性があります。

オプション **Replace** は **Update** と同じですが、使用されていません。

## その他のリソース

[Azure AD Connect Sync: 関数リファレンス](active-directory-aadconnectsync-functions-reference.md) [Azure AD Connect Sync: 同期オプションのカスタマイズ](active-directory-aadconnectsync-whatis.md) [オンプレミス ID と Azure Active Directory の統合](active-directory-aadconnect.md)

<!---HONumber=AcomDC_0824_2016-->