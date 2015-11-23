<properties
	pageTitle="Azure AD Connect Sync: 宣言型のプロビジョニングの式について | Microsoft Azure"
	description="宣言型のプロビジョニングの式について説明します。"
	services="active-directory"
	documentationCenter=""
	authors="markusvi"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="11/10/2015"
	ms.author="markusvi;andkjell"/>


# Azure AD Connect Sync: 宣言型のプロビジョニングの式について

Azure AD Connect Sync は、Forefront Identity Manager 2010 で初めて導入された宣言型のプロビジョニングを基盤としています。これは、コンパイルされたコードを記述することなく完全な ID 統合ビジネス ロジックを実装できるようにするためのものです。

宣言型のプロビジョニングの不可欠な要素は、属性フローに使用される式言語です。使用される言語は、Microsoft ® Visual Basic ® for Applications (VBA) のサブセットです。この言語は、Microsoft Office で使用され、VBScript の経験を持つユーザーも理解できます。宣言型のプロビジョニングの式言語は、関数を使用するだけであり、構造化言語ではありません。メソッドやステートメントはありません。代わりに、関数を入れ子にして、プログラム フローを記述します。

詳細については、「[Office 2013 Visual Basic for Applications 言語リファレンスへようこそ](https://msdn.microsoft.com/library/gg264383.aspx)」を参照してください。

属性は厳密に型指定されます。単一値の文字列属性を想定している関数では、複数値や異なる型の属性は受け入れられません。また、大文字と小文字が区別されます。関数名と属性名のいずれも、適切に大文字と小文字を使用する必要があります。そのようにしない場合、エラーがスローされます。

## 言語の定義と識別子

- 関数は、名前の後に山かっこで囲まれた引数が続きます。たとえば、FunctionName(argument 1,argument N) などです。
- 属性は、角かっこで識別されます。たとえば、[attributeName] などです。
- パラメーターは、パーセント記号で識別されます。たとえば、%ParameterName% などです。
- 文字列定数は、引用符で囲まれます。たとえば、"Contoso" などです (注: 左右の区別がある引用符 “” ではなく、左右の区別がない引用符 "" を使用する必要があります)
- 数値は、引用符を使用しないで表され、10 進数であることが求められます。16 進数の値には、&H がプレフィックスとして付加されます。例: 98052, &HFF
- ブール値は、定数 (True、False) で表されます。
- 組み込み定数は、その名前のみで表されます。たとえば、NULL、CRLF、IgnoreThisFlow などです。

### 関数
宣言型のプロビジョニングでは、属性値を変換する機能を持つ多くの関数を使用します。関数を入れ子にして、ある関数の結果を別の関数に渡すことができます。

また、関数は複数値の属性も操作することができます。この場合、関数は個々の値を操作し、同じ関数を各値に適用します。たとえば、`Trim([proxyAddresses])`は proxyAddress 属性の各値の Trim を実行します。

全関数の一覧については、[関数参照](active-directory-aadconnectsync-functions-reference.md)を参照してください。

### パラメーター

パラメーターはコネクタから定義されるか、管理者が PowerShell を使用して設定します。通常、パラメーターには、ユーザーが属しているドメインの名前など、システム間で異なる値が含まれます。これらは属性フローで使用できます。

Active Directory Connector は、受信同期ルールについて次のパラメーターを提供しています。

| パラメーター名 | コメント |
| --- | --- |
| Domain.Netbios | 現在インポートされているドメインの NetBIOS 形式 (FABRIKAMSALES など) |
| Domain.FQDN | 現在インポートされているドメインの FQDN 形式 (sales.fabrikam.com など) |
| Domain.LDAP | 現在インポートされているドメインの LDAP 形式 (DC=sales,DC=fabrikam,DC=com など) |
| Forest.Netbios | 現在インポートされているフォレスト名の NetBIOS 形式 (FABRIKAMCORP など) |
| Forest.FQDN | 現在インポートされているフォレスト名の FQDN 形式 (fabrikam.com など) |
| Forest.LDAP | 現在インポートされているフォレスト名の LDAP 形式 (DC=fabrikam,DC=com など) |

システムには次のパラメーターが用意されています。現在実行中のコネクタの識別子を取得するために使用されます。

`Connector.ID`

次の例では、ユーザーが属しているドメインの NetBIOS 名をメタバース属性ドメインに取り込みます。

`domain <- %Domain.Netbios%`

### 演算子

次の演算子を使用できます。

- **比較**: <、<=、<>、=、>、>=
- **算術**: +、-、*、-
- **文字列**: & (連結)
- **論理**: && (AND)、|| (OR)
- **評価順序**: ( )

演算子は左から右に評価されます。評価の優先順位は同じです。つまり、* (乗算) は - (減算) よりも先に評価されません。2*(5+3) は、2*5+3 と同じではありません。かっこ () は、左から右への評価順が適切ではない場合に、評価順を変更するために使用されます。

## 一般的なシナリオ

### 属性の長さ

文字列属性は、既定では、インデックス可能で、最大長は 448 文字に設定されています。それより多い文字を含む可能性がある文字列属性を使用する場合は、属性フローに次の式を含めるようにします。

`attributeName <- Left([attributeName],448)`

### userPrincipalSuffix の変更

Active Directory の userPrincipalName 属性は、常にユーザーに認識されるわけではなく、ログイン ID として適切でない場合があります。Azure AD Connect Sync インストール ウィザードを使用すると、mail など異なる属性を選択できます。ただし、場合によっては、属性を計算する必要があります。たとえば、Contoso 社に 2 つの Azure AD ディレクトリがあり、一方は運用環境用、もう一方はテスト用であるとします。テスト テナント内のユーザーについて、ログイン ID に含まれるサフィックスのみを変更しようと検討しています。

`userPrincipalName <- Word([userPrincipalName],1,"@") & "@contosotest.com"`

この式では、最初の @ 記号の左側のすべてを使用し (Word)、固定文字列をそこに連結します。

### 複数値から単一値への変換

Active Directory の一部の属性は、Active Directory ユーザーとコンピューターでは単一値に見えますが、スキーマでは複数値になっています。例として挙げられるのが、説明属性です。

`description <- IIF(IsNullOrEmpty([description]),NULL,Left(Trim(Item([description],1)),448))`

この式で属性が値を持つ場合は、属性の最初のアイテムを使用し (Item)、先頭と末尾のスペースを削除して (Trim)、文字列の最初の 448 文字を維持します (Left)。

## 先進的概念

### NULL と IgnoreThisFlow

受信同期ルールの場合は、定数 **NULL** を常に使用する必要があります。これは、提供する値がフローになく、別のルールで値を提供できることを示します。値を提供するルールがない場合、メタバース属性は削除されます。

送信同期ルールでは、使用する定数として NULL および IgnoreThisFlow の 2 つがあります。いずれも、提供する値が属性フローにないことを示しますが、相違点は、提供する値が他のルールにもないときの動作です。接続されたディレクトリに既存の値がある場合、NULL は属性に delete をステージングして既存の値を削除しますが、IgnoreThisFlow は既存の値を維持します。

#### ImportedValue

ImportedValues 関数は、属性名を角かっこではなく引用符で囲む必要がある点で、他のすべての関数とは異なっています。たとえば、ImportedValue("proxyAddresses") などです。

通常、同期する際は、まだエクスポートされていない場合であっても、エクスポート中にエラーが発生した場合であっても、属性では予想される値を使用します ("top of the tower")。受信同期では、接続されたディレクトリにまだ届いていない属性も最終的には届くと見なされます。また、接続されたディレクトリで確認されている値のみを同期することが重要な場合もあり、この場合は ImportedValue 関数が使用されます ("hologram and delta import tower")。

この場合の例は、標準の同期ルールである Exchange の In from AD – User Common で確認できます。そこでは、Exchange ハイブリッドにおいて、Exchange Online で追加された値を、その値が正常にエクスポートされたことが確認された場合にのみ同期する必要があります。

`proxyAddresses <- RemoveDuplicates(Trim(ImportedValues("proxyAddresses")))`

全関数の一覧については、「[Azure AD Connect Sync: 関数参照](active-directory-aadconnectsync-functions-reference.md)」を参照してください。


## その他のリソース

* [Azure AD Connect Sync: 同期オプションのカスタマイズ](active-directory-aadconnectsync-whatis.md)
* [オンプレミス ID と Azure Active Directory の統合](active-directory-aadconnect.md)

<!--Image references-->

<!---HONumber=Nov15_HO3-->