<properties
	pageTitle="ID 同期と重複属性の回復性 | Microsoft Azure"
	description="Azure AD Connect によるディレクトリ同期中に UPN または ProxyAddress が競合しているオブジェクトを処理する方法の新しい動作です。"
	services="active-directory"
	documentationCenter=""
	authors="markusvi"
	manager="femila"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/26/2016"
	ms.author="markusvi"/>



# ID 同期と重複属性の回復性
重複属性の回復性は、Microsoft のいずれかの同期ツールを実行しているときに **UserPrincipalName** や **ProxyAddress** の競合によって引き起こされる不整合を避けるために Azure Active Directory に備えられている機能です。

この 2 つの属性は、通常、特定の Azure Active Directory ディレクトリ内のすべての **User**、**Group**、および **Contact** オブジェクトにわたって一意である必要があります。

> [AZURE.NOTE] UPN を持てるのは、User のみです。

この機能によって有効になる新しい動作は、同期パイプラインのクラウド部分内にあります。そのため、クライアントに依存せず、Azure AD Connect、DirSync、MIM + Connector などの任意の Microsoft 同期製品に対応できます。このドキュメントでは、これらの製品のいずれかを表すために、一般的な用語 "同期クライアント" を使用します。

## 現在の動作
この一意性制約に違反する UPN または ProxyAddress 値で新しいオブジェクトをプロビジョニングしようとすると、Azure Active Directory はそのオブジェクトの作成をブロックします。同様に、一意でない UPN または ProxyAddress でオブジェクトが更新されると、更新は失敗します。プロビジョニングの試行または更新は、エクスポート サイクルごとに同期クライアントによって再試行され、競合が解決されるまでは失敗し続けます。試行のたびにエラー レポートの電子メールが生成され、エラーが同期クライアントによって記録されます。

## 重複属性の回復性による動作
属性が重複するオブジェクトのプロビジョニングまたは更新を完全に失敗させる代わりに、Azure Active Directory は一意性の制約に違反する重複属性を "検疫" します。この属性が、UserPrincipalName のように、プロビジョニングに必要な場合、サービスはプレースホルダー値を割り当てます。これらの一時的な値の形式は、"***<OriginalPrefix>+<4DigitNumber>@<InitialTenantDomain>.onmicrosoft.com***" です。**ProxyAddress** のように、この属性が必須でない場合、Azure Active Directory は競合属性を検疫し、オブジェクトの作成または更新を続行します。

属性の検疫時に、競合に関する情報は、従来の動作で使用されるのと同じエラー レポート電子メールで送信されます。ただし、この情報は、検疫が発生した際にエラー レポートに 1 回表示されるだけで、その後の電子メールではログに記録されません。また、このオブジェクトのエクスポートは成功しているため、同期クライアントはエラーをログに記録せず、後続の同期サイクルで作成/更新操作を再試行しません。

この動作をサポートするために、次の新しい属性が User、Group、および Contact オブジェクト クラスに追加されました。**DirSyncProvisioningErrors**

これは複数値の属性であり、普通に追加されたら一意性の制約に違反する、競合している属性を格納します。Azure Active Directory で、解決された重複属性の競合を検出し、該当する属性を検疫から自動的に削除するための、1 時間ごとに実行されるバックグラウンド タイマー タスクが有効になりました。

### 重複属性の回復性の有効化
この機能は、Azure Active Directory PowerShell モジュールの最新バージョンをダウンロードして、次のように実行することによって、有効にすることができます。

`Set-MsolDirSyncFeature -Feature DuplicateUPNResiliency -Enable $true`

`Set-MsolDirSyncFeature -Feature DuplicateProxyAddressResiliency -Enable $true`

現時点では、UPN と ProxyAddress の回復性を個別に有効にしたり無効にしたりすることができます。この動作は今後変更される予定です。両方がすべての Azure AD ディレクトリで有効になり、無効にはできなくなります。

## DirSyncProvisioningErrors を持つオブジェクトの特定
重複するプロパティの競合によってこれらのエラーが発生したオブジェクトを特定するための方法は、現在、2 つあります。それは、Azure Active Directory PowerShell と Office 365 管理ポータルです。今後のレポートに基づいてポータルを追加する拡張が予定されています。

### Azure Active Directory PowerShell
このトピックの PowerShell コマンドレットに関しては、以下のことが言えます。

- 以下のすべてのコマンドレットが、大文字と小文字を区別します。
- **–ErrorCategory PropertyConflict** を常に含める必要があります。現在、**ErrorCategory** には他の型はありませんが、将来は拡張される可能性があります。

まず、**Connect-MsolService** を実行し、テナント管理者の資格情報を入力します。

次に、以下のコマンドレットと演算子を使用して、エラーをさまざまな方法で表示します。

1. [すべて表示](#see-all)

2. [プロパティの型ごと](#by-property-type)

3. [競合する値ごと](#by-conflicting-value)

4. [文字列検索を使用](#using-a-string-search)

5. [並べ替え](#sorted)

6. [制限した数、またはすべて](#in-a-limited-quantity-or-all)


#### すべて表示
接続されたら、テナント内の属性プロビジョニング エラーの全般的な一覧を表示するために、次のように実行します。

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict`

これにより、次のような結果が生成されます。![Get-MsolDirSyncProvisioningError](./media/active-directory-aadconnectsyncservice-duplicate-attribute-resiliency/1.png "Get-MsolDirSyncProvisioningError")


#### プロパティの型ごと
プロパティの型ごとにエラーを表示するには、**UserPrincipalName** 引数か **ProxyAddresses** 引数に、次のように **-PropertyName** フラグを追加します。

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -PropertyName UserPrincipalName`

または

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -PropertyName ProxyAddresses`

#### 競合する値ごと
特定のプロパティに関連するエラーを表示するには、次のように **-PropertyValue** フラグを追加します (このフラグを追加する場合は、**-PropertyName** も使用する必要があります)。

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -PropertyValue User@domain.com -PropertyName UserPrincipalName`


#### 文字列検索を使用
広範な文字列検索を行うには、**-SearchString** フラグを使用します。このフラグは上記のすべてのフラグとは別に使用できますが、**-ErrorCategory PropertyConflict** は例外です。このフラグとは常に一緒に使用する必要があります。

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -SearchString User`

#### 並べ替え
クエリの結果の並べ替えに使用できる、次の 2 つのフラグがあります。

1.	**SortField**: 有効な引数は、DisplayName、UserPrincipalName などです。

2.	**SortDirection**: 有効な引数は、Ascending、Descending などです。

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -SortField UserPrincipalName -SortDirection Ascending`

#### 制限した数、またはすべて
1. **MaxResults <Int>** を使用して、クエリの値を特定の数までに制限できます。

2. **All** は、多数のエラーが存在する場合に使用すると、すべての結果を確実に取得できます。

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -MaxResults 5`

## Office 365 管理ポータル

Office 365 管理センターでは、ディレクトリ同期エラーを表示できます。Office 365 ポータルのレポートには、これらのエラーを持つ **User** オブジェクトだけが表示されます。**Group**、**Contact**、または **PublicFolder** の間の競合に関する情報は表示されません。


![アクティブ ユーザー](./media/active-directory-aadconnectsyncservice-duplicate-attribute-resiliency/1234.png "アクティブ ユーザー")

Office 365 管理センターでディレクトリ同期エラーを表示する方法については、「[Office 365 でディレクトリ同期エラーを確認する](https://support.office.com/ja-JP/article/Identify-directory-synchronization-errors-in-Office-365-b4fc07a5-97ea-4ca6-9692-108acab74067)」を参照してください。


### ID 同期のエラー レポート
重複属性の競合があるオブジェクトがこの新しい動作で処理されると、テナントの技術的通知の連絡先に送信される標準の ID 同期のエラー レポート メールに、通知が含められます。ただし、この動作には重要な変更があります。以前は、重複属性の競合に関する情報が、競合が解決されるまで、後続のすべてのエラー レポートに含められました。この新しい動作では、特定の競合のエラー通知は、競合する属性が検疫されたときに 1 回だけ表示されます。

ProxyAddress の競合に関する電子メール通知の例を、次に示します。![アクティブ ユーザー](./media/active-directory-aadconnectsyncservice-duplicate-attribute-resiliency/6.png "アクティブ ユーザー")

## 競合の解決
これらのエラーのトラブルシューティングの方針と解決の方法は、以前の重複属性エラーの処理方法と変わりはありません。唯一の違いは、タイマー タスクがサービス側のテナント全体をスイープして、競合が解決したら問題の属性を適切なオブジェクトに自動的に追加することです。

記事「[Office 365 ディレクトリ同期を妨げる重複または無効な属性を検出する方法](https://support.microsoft.com/kb/2647098)」では、さまざまなトラブルシューティングと解決方法の概要を説明しています。

## 既知の問題
これらの既知の問題が、データの損失やサービスの低下を引き起こすことはありません。いくつかは見た目の問題で、その他に一般的な "*回復前*" 重複属性エラーを引き起こし、競合属性を検疫する代わりにスローされる問題や、手動での修正を必要とするエラーを引き起こす問題があります。

**主要な動作:**

1. 特定の属性構成を持つユーザーは、検疫されている属性ではなく、エクスポート エラーを受信し続けます。次に例を示します。

    a.AD で、UPN を ****Joe@contoso.com**、ProxyAddress を **smtp:Joe@contoso.com** として新しいユーザーが作成されました。

    b.このオブジェクトのプロパティが、ProxyAddress が **SMTP:Joe@contoso.com** である既存の Group と競合します。

    c.エクスポート時に、競合属性を検疫するのではなく、**ProxyAddress 競合**エラーがスローされます。回復性機能が有効になる前と同様に、後続の同期サイクルごとに操作が再試行されます。

2. 解決された重複属性の競合を探すタイマー タスクは、UPN 競合と他の UPN 競合だけを比較します。そのため、次のシナリオの手順 4. に示すような問題が発生します。

    a. ****UserA@contoso.com** の UPN が、他のオブジェクトの ProxyAddress と同じ値であるため、一意ではありません。

    b.UserA に一時的な **MOERA UPN** である ****UserA1234@contoso.onmicrosoft.com** が付与され、実際の UPN 値は検疫されます (期待どおり)。

    c.他の競合しているオブジェクトでは、後で ProxyAddress が削除されます。

    d.UserA の UPN が自動的に修正されることはありません。手動で更新する必要があります。

3. オンプレミスで 2 つの Group が同じ SMTP アドレスで作成された場合、一方は標準の重複 **ProxyAddress** エラーのために、最初の試行でプロビジョニングに失敗します。ただし、重複している値は、次の同期サイクル時に適切に検疫されます。

**PowerShell コマンドレット**:

1. **ImmutableId** / **LastDirSyncTime** は、User オブジェクト クラスの場合は表示されません。

2. **SortField** フラグと **SortDirection** フラグは結果に影響しません。

3. **PropertyName** フラグを追加せずに **PropertyValue** フラグを使用すると、あいまいなエラーがスローされます。

4. **SearchString** フラグは、**PropertyValue** フラグと **PropertyName** フラグなしで実行すると、余分な結果を返します。

**Office ポータル レポート**:

1. UPN 競合セットの 2 つのオブジェクトの詳細なエラー メッセージは、同一です。つまり、両方で UPN が変更/検疫されたと示されますが、実際には一方だけでデータが変更されています。

2. UPN 競合の詳細なエラー メッセージは、UPN を変更/検疫したユーザーの正しくない displayName を表示します。次に例を示します。

    a.**ユーザー A** が最初に **UPN = User@contoso.com** で同期を実行します。

    b.**ユーザー B** が次に **UPN = User@contoso.com** で同期を試行します。

    c.**ユーザー B** の UPN が ****User1234@contoso.onmicrosoft.com** に変更され、****User@contoso.com** が **DirSyncProvisioningErrors** に追加されます。

    d.**ユーザー B** のエラー メッセージは、**ユーザー A** が既に ****User@contoso.com** を UPN として持っていることを示すべきですが、**ユーザー B** 自身の displayName が表示されます。

3. レポートは、**UPN** 競合があるユーザーの詳細なエラー情報だけを表示できます。**ProxyAddress** エラーがあるユーザーの情報は表示できません (これが一般的な問題なのか環境の問題なのかは、まだ調査中です)。

## 関連項目

- [Azure AD Connect Sync](active-directory-aadconnectsync-whatis.md)

- [オンプレミス ID と Azure Active Directory の統合](active-directory-aadconnect.md)

- [Office 365 でディレクトリ同期エラーを確認する](https://support.office.com/ja-JP/article/Identify-directory-synchronization-errors-in-Office-365-b4fc07a5-97ea-4ca6-9692-108acab74067)

<!---HONumber=AcomDC_0601_2016-->