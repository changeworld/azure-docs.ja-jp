---
title: Azure Active Directory のサインイン ログ - プレビュー | Microsoft Docs
description: プレビュー版の新機能を含む、Azure Active Directory のサインイン ログの概要
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: karenhoran
editor: ''
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 06/23/2021
ms.author: markvi
ms.reviewer: besiler
ms.collection: M365-identity-device-management
ms.openlocfilehash: 209b9f66a9d7d8d004162e465c85311886b423fa
ms.sourcegitcommit: 27ddccfa351f574431fb4775e5cd486eb21080e0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/08/2021
ms.locfileid: "131997212"
---
# <a name="sign-in-logs-in-azure-active-directory---preview"></a>Azure Active Directory のサインイン ログ - プレビュー

IT 管理者は、IT 環境がどのように動作しているかを知る必要があります。 システムの正常性に関する情報を使用すると、潜在的な問題に対処する必要があるかどうか、およびその方法を評価できます。 

この目標を達成するために、Azure Active Directory ポータルでは、次の 3 つのアクティビティ ログにアクセスできます。

- **[サインイン](concept-sign-ins.md)** – サインインと、ユーザーのリソース使用状況に関する情報。
- **[監査](concept-audit-logs.md)** - ユーザーやグループの管理、テナントのリソースに適用された更新など、テナントに適用された変更に関する情報。
- **[プロビジョニング](concept-provisioning-logs.md)** - ServiceNow でのグループの作成や、Workday からインポートされたユーザーなど、プロビジョニング サービスによって実行されるアクティビティ。


Azure Active Directory の従来のサインイン ログでは、対話型ユーザー サインインの概要を確認できます。これに加えて、現在プレビュー版である 3 種類のサインイン ログが、新たに利用できるようになりました。

- 非対話型のユーザー サインイン

- サービス プリンシパルのサインイン

- Azure リソースのマネージド ID によるサインイン

この記事では、非対話型、アプリケーション、および Azure リソースのマネージド ID の各サインインのプレビューを含むサインイン アクティビティ レポートの概要を説明します。プレビュー機能を含まないサインイン レポートの詳細については、「[Azure Active Directory のサインイン ログ](concept-sign-ins.md)」を参照してください。



## <a name="what-can-you-do-with-it"></a>それで何ができますか。

サインイン ログでは、例えば次のことが分かります。

- ユーザー、アプリケーション、またはサービスのサインインにどのようなパターンがあるか。

- 1 週間にどれだけの数のユーザー、アプリ、またはサービスからサインインが行われたか。

- これらのサインインはどのような状態か。


## <a name="who-can-access-the-data"></a>誰がデータにアクセスできますか。

- セキュリティ管理者、セキュリティ閲覧者、およびレポート閲覧者ロールのユーザー

- グローバル管理者

- 任意のユーザー (非管理者) が自分のサインインにアクセス可能 

## <a name="what-azure-ad-license-do-you-need"></a>どのような Azure AD ライセンスが必要ですか。

サインイン アクティビティを表示するには、ご利用のテナントに、Azure AD Premium ライセンスが関連付けられている必要があります。 Azure Active Directory エディションにアップグレードするには、「[Azure Active Directory Premium の概要](../fundamentals/active-directory-get-started-premium.md)」を参照してください。 アップグレード前のアクティビティ データがない状態でプレミアム ライセンスにアップグレードした後、データがログに表示されるまでには数日間かかります。




## <a name="where-can-you-find-it-in-the-azure-portal"></a>Azure portal のどこにありますか。

Azure portal でログにアクセスする方法はいくつかあります。 たとえば、[Azure Active Directory] メニューの **[監視]** セクションでログを開けます。  

![サインイン ログを開く](./media/concept-sign-ins/sign-ins-logs-menu.png)

また、このリンクで直接サインイン ログにアクセスすることもできます: [https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/SignIns](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/SignIns)

サインイン ページでは、次のものを切り替えられます。

- **対話型のユーザー サインイン** - パスワード、MFA アプリを使用した応答、生体認証要素、QR コードなどの認証要素をユーザーが提供するサインインです。

- **非対話型のユーザー サインイン** - ユーザーに代わりクライアントによって実行されるサインインです。 このサインインの場合、ユーザーは操作または認証要素を要求されません。 たとえば、更新およびアクセスのトークンを使用した認証と承認には、ユーザーによる資格情報の入力は必要とされません。

- **サービス プリンシパル サインイン** - ユーザーが関与しないアプリとサービス プリンシパルによるサインインです。 このサインインの場合、認証またはリソースへのアクセス用の資格情報が、アプリまたはサービスによって、それ自身のために提供されます。

- **Azure リソースのマネージド ID によるサインイン** - Azure でシークレットが管理されている Azure リソースによるサインインです。 詳細については、「[Azure リソースのマネージド ID とは](../managed-identities-azure-resources/overview.md)」を参照してください。 


![サインイン ログの種類](./media/concept-all-sign-ins/sign-ins-report-types.png)



サインイン ページの各タブでは、下に挙げる既定の列を表示します。 一部のタブには、他の列もあります。

- サインイン日

- 要求 ID

- ユーザー名またはユーザー ID

- アプリケーション名またはアプリケーション ID

- サインインの状態

- サインインに使用されるデバイスの IP アドレス



### <a name="interactive-user-sign-ins"></a>対話型のユーザー サインイン


対話型ユーザー サインインは、ユーザーが Azure AD に認証要素を提供するか、Azure AD またはヘルパー アプリ (Microsoft Authenticator アプリなど) と直接やり取りするサインインです。 ユーザーが提供する要素としては、ユーザーが Azure AD またはヘルパー アプリに提供するパスワード、MFA チャレンジへの応答、生体認証要素、QR コードなどがあります。

> [!NOTE]
> このログには、Azure AD と連携している ID プロバイダーによるフェデレーション サインインも含まれます。  



> [!NOTE] 
> 以前は、対話型ユーザー サインインのログに、Microsoft Exchange クライアントの非対話型サインインのログも含まれていました。 このサインインは非対話型ですが、追加的な情報として対話型ユーザー サインインのログに含まれていました。 パブリック プレビュー版の非対話型ユーザー サインイン ログを 2020 年 11 月にリリースしてからは、正確に分類するため、この非対話型サインイン ログを、非対話型ユーザー サインイン ログに移行しました。 


**レポート サイズ:** 小 <br> 
**例:**

- ユーザーが Azure AD サインイン画面でユーザー名とパスワードを入力します。

- ユーザーが SMS MFA チャレンジを渡します。

- ユーザーが、Windows Hello for Business で Windows PC のロックを解除するための生体認証ジェスチャを提供します。

- ユーザーが AD FS SAML アサーションを使用して Azure AD にフェデレーションします。


対話型サインイン ログでは、既定の欄に加えて次のものを表示します。 

- サインインの場所

- 条件付きアクセスが適用されているかどうか



リスト ビューをカスタマイズするには、ツール バーの **[列]** をクリックします。

![対話型のユーザー サインインの列](./media/concept-all-sign-ins/columns-interactive.png "対話型のユーザー サインインの列")





ビューをカスタマイズすると、追加フィールドの表示や、既に表示されているフィールドの削除が可能です。

![対話型のすべての列](./media/concept-all-sign-ins/all-interactive-columns.png)


関連するサインインについての詳細な情報を取得するには、リスト ビューで項目を選択します。

![サインイン アクティビティ](./media/concept-all-sign-ins/interactive-user-sign-in-details.png "対話型のユーザー サインイン")



### <a name="non-interactive-user-sign-ins"></a>非対話型のユーザー サインイン

非対話型のユーザー サインインは、ユーザーに代わってクライアント アプリまたは OS コンポーネントによって実行されたサインインです。 このようなサインインは対話型のユーザー サインインと同様に、ユーザーに代わって行われます。 このようなサインインは対話型のユーザー サインインとは異なり、ユーザーが認証要素を提供する必要はありません。 代わりに、デバイスまたはクライアント アプリでトークンまたはコードを使用して、ユーザーに代わって認証またはリソースへのアクセスが行われます。 一般に、ユーザーは、これらのサインインがユーザーのアクティビティのバックグラウンドで実行されていると認識します。


**レポート サイズ:** Large <br>
**例:** 

- クライアント アプリで OAuth 2.0 更新トークンを使用してアクセス トークンを取得します。

- クライアントで OAuth 2.0 認証コードを使用して、アクセス トークンと更新トークンを取得します。

- ユーザーは Azure AD に参加している PC で Web または Windows アプリへのシングル サインオン (SSO) を実行します (認証要素を指定したり、Azure AD プロンプトを操作したりする必要はありません)。

- ユーザーが、FOCI (クライアント ID のファミリ) を使用してモバイル デバイス上でセッションを行っているときに、2 つ目の Microsoft Office アプリにサインインします。




非対話型サインイン ログでは、既定の欄に加えて次のものを表示します。 

- Resource ID

- グループ化されたサインインの数




このレポートに表示されるフィールドをカスタマイズすることはできません。


![無効化されている列](./media/concept-all-sign-ins/disabled-columns.png "無効化されている列")

データを理解しやすくするために、非対話型のサインイン イベントがグループ化されます。 クライアントによって、同じユーザーに代わって非対話型のサインインが多数作成される場合がよくあります。この場合、サインインが試行された時間を除き、すべて同じ特性が共有されています。 たとえば、クライアントがユーザーに代わって 1 時間に 1 回アクセス トークンを取得する場合があります。 ユーザーまたはクライアントの状態が変わらない場合、IP アドレス、リソース、およびその他すべての情報は、各アクセス トークン要求で同じになります。 Azure AD で時刻と日付以外が同じ複数のサインインがログに記録された場合、それらのサインインは同じエンティティからのものであり、1 つの行に集計されます。 複数回の同一サインインを示す行 (発行された日時を除く) には、サインイン数の列に 1 より大きい値が表示されます。 行を展開すると、異なる複数のサインインとそれらの異なるタイムスタンプをすべて表示できます。 非対話型のユーザーの場合、次のデータが一致するとサインインが集計されます。


- Application

- User

- IP アドレス

- Status

- Resource ID


次のようにすることができます。

- ノードを展開すると、グループの個々の項目が表示されます。  

- 個々の項目をクリックすると、すべての詳細が表示されます 


![非対話型ユーザー サインインの詳細](./media/concept-all-sign-ins/non-interactive-sign-ins-details.png)




## <a name="service-principal-sign-ins"></a>サービス プリンシパルのサインイン

対話型または非対話型のユーザー サインインとは異なり、サービス プリンシパルのサインインにはユーザーが関与しません。 ここには、アプリやサービス プリンシパルのような、非ユーザー アカウントを使用するあらゆるサインインが含まれます (ただし、マネージド ID サインインは、これとは別に、マネージド ID サインイン ログに記録されます)。 これらのサインインにおいては、アプリまたはサービスによって、それ自身の資格情報 (認証またはリソースへのアクセスのための証明書やアプリ シークレットなど) が提供されます。


**レポート サイズ:** Large <br>
**例:**

- サービス プリンシパルによる証明書を使用した認証が行われ、Microsoft Graph にアクセスされます。 

- アプリケーションでクライアント シークレットを使用して、OAuth クライアント資格情報フローでの認証が行われます。 


このレポートには、次のものを示すデフォルト リスト ビューがあります。

- サインイン日

- 要求 ID

- サービス プリンシパル名または ID

- Status

- IP アドレス

- リソース名

- Resource ID

- サインインの数

このレポートに表示されるフィールドをカスタマイズすることはできません。

![無効化されている列](./media/concept-all-sign-ins/disabled-columns.png "無効化されている列")

サービス プリンシパル サインイン ログのデータを簡単に理解できるように、サービス プリンシパルのサインイン イベントはグループ化されます。 同じ条件下での同じエンティティからのサインインが 1 つの行に集計されます。 行を展開すると、異なる複数のサインインとそれらの異なるタイムスタンプをすべて表示できます。 次のデータが一致すると、サービス プリンシパル レポートでサインインが集計されます。

- サービス プリンシパル名または ID

- Status

- IP アドレス

- リソース名または ID

次のようにすることができます。

- ノードを展開すると、グループの個々の項目が表示されます。  

- 個々の項目をクリックすると、すべての詳細が表示されます 


![列の詳細](./media/concept-all-sign-ins/service-principals-sign-ins-view.png "列の詳細")




## <a name="managed-identity-for-azure-resources-sign-ins"></a>Azure リソースのマネージド ID によるサインイン 

Azure リソースのマネージド ID によるサインインは、資格情報管理の簡略化のために Azure でシークレットが管理されているリソースによって実行されたサインインです。

**レポート サイズ:** Small <br> 
**例:**

資格情報が管理されている VM で Azure AD を使用してアクセス トークンを取得します。   


このレポートには、次のものを示すデフォルト リスト ビューがあります。


- マネージド ID

- マネージド ID 名

- リソース

- Resource ID

- グループ化されたサインインの数

このレポートに表示されるフィールドをカスタマイズすることはできません。

Azure リソース サインイン ログのマネージド ID のデータを簡単に理解できるように、対話型でないサインイン イベントがグループ化されます。 同じエンティティからのサインインは、1 つの行に集計されます。 行を展開すると、異なる複数のサインインとそれらの異なるタイムスタンプをすべて表示できます。 次のすべてのデータが一致すると、マネージド ID レポートでサインインが集計されます。

- マネージド ID 名または ID

- Status

- リソース名または ID

1 つのノードの下にグループ化されているすべてのサインインを表示するには、リスト ビューで項目を選択します。

グループ化された項目を選択すると、サインインの詳細がすべて表示されます。 


## <a name="sign-in-error-code"></a>サインイン エラー コード

サインインに失敗した場合は、関連するログ項目の **[基本情報]** セクションで、理由に関する詳細情報を取得できます。 

![スクリーンショットには、詳細情報ビューが示されています。](./media/concept-all-sign-ins/error-code.png)
 
ログ項目にはエラーの理由が表示されますが、[サインイン エラー ルックアップ ツール](https://login.microsoftonline.com/error)を使用して、さらに多くの情報が得られる場合があります。 たとえば、使用可能な場合は、このツールにより修復手順が通知されます。  

![エラー コード ルックアップ ツール](./media/concept-all-sign-ins/error-code-lookup-tool.png)



## <a name="filter-sign-in-activities"></a>サインイン アクティビティのフィルター処理

フィルターを設定することにより、返されるサインイン データの範囲を絞り込むことができます。 Azure AD では、さまざまな追加のフィルターを設定できます。 フィルターを設定するときは常に、構成されている **[日付]** 範囲フィルターに特に注意する必要があります。 適切な日付範囲フィルターを使用すると、本当に関心のあるデータのみが確実に Azure AD から返されるようになります。     

**[日付]** 範囲フィルターを使用すると、返されるデータの期間を定義できます。
次のいずれかの値になります。

- 1 か月

- 7 日間

- 24 時間

- カスタム

![時間範囲フィルター](./media/concept-all-sign-ins/date-range-filter.png)





### <a name="filter-user-sign-ins"></a>ユーザー サインインのフィルター処理

対話型と非対話型のサインインのフィルターは同じです。 このため、対話型サインイン用に構成したフィルターが非対話型サインイン用に保持されます。その逆も同様です。 






## <a name="access-the-new-sign-in-activity-logs"></a>新しいサインイン アクティビティ ログにアクセスする 

Azure portal のサインイン アクティビティ レポートには、プレビュー レポートをオンまたはオフに切り替える簡単な方法が用意されています。 プレビュー ログが有効になっている場合は、すべての種類のサインイン アクティビティ レポートにアクセスできる新しいメニューが表示されます。     


非対話型およびアプリケーション サインインを含む新しいサインイン ログにアクセスするには: 

1. [Azure Portal](https://portal.azure.com) で、 **[Azure Active Directory]** を選択します。

    ![Azure AD の選択](./media/concept-all-sign-ins/azure-services.png)

2. **[監視]** セクションで、 **[サインイン]** をクリックします。

    ![サインインの選択](./media/concept-all-sign-ins/sign-ins.png)

3. **プレビュー** バーをクリックします。

    ![新しいビューを有効にする](./media/concept-all-sign-ins/enable-new-preview.png)

4. 既定のビューに戻すには、**プレビュー** バーをもう一度クリックします。 

    ![クラシック ビューの復元](./media/concept-all-sign-ins/switch-back.png)







## <a name="download-sign-in-activity-logs"></a>サインイン アクティビティ ログのダウンロード

サインイン アクティビティ レポートをダウンロードするときは、次のことが当てはまります。

- サインイン レポートは、CSV または JSON ファイルとしてダウンロードできます。

- 最大 100,000 個のレコードをダウンロードできます。 さらに多くのデータをダウンロードする場合は、レポート API を使用します。

- ダウンロードは、選択したフィルターに基づいて行われます。

- ダウンロードできるレコードの数は、[Azure Active Directory レポートの保持ポリシー](reference-reports-data-retention.md)によって制限されます。 


![ログをダウンロードする](./media/concept-all-sign-ins/download-reports.png "ログをダウンロードする")


各 CSV ダウンロードは、6 つの異なるファイルで構成されています。

- 対話型サインイン

- 対話型サインインの認証の詳細

- 非対話型サインイン

- 非対話型サインインの認証の詳細

- サービス プリンシパルのサインイン

- Azure リソースのマネージド ID によるサインイン

各 JSON ダウンロードは、4 つの異なるファイルで構成されています。

- 対話型のサインイン (認証の詳細を含む)

- 非対話型のサインイン (認証の詳細を含む)

- サービス プリンシパルのサインイン

- Azure リソースのマネージド ID によるサインイン

![ファイルのダウンロード](./media/concept-all-sign-ins/download-files.png "ファイルのダウンロード")


## <a name="return-log-data-with-microsoft-graph"></a>Microsoft Graph でログ データを取得する

サインイン ログは、Azure portal に加え、Microsoft Graph API から照会して、さまざまなサインイン情報を取得することができます。 パフォーマンスに問題が生じる可能性があるので、それを避けるため、クエリのスコープは、関心のあるデータに限定してください。 

次の例では、レコード数、特定の期間、サインイン イベントの種類でクエリのスコープを制限しています。

```msgraph-interactive
GET https://graph.microsoft.com/beta/auditLogs/signIns?$top=100&$filter=createdDateTime ge 2020-09-10T06:00:00Z and createdDateTime le 2020-09-17T06:00:00Z and signInEventTypes/any(t: t eq 'nonInteractiveUser')
```

この例のクエリ パラメーターでは、次の結果が得られます。

- [$top](/graph/query-parameters#top-parameter) パラメーターによって上位 100 件の結果が返されます。
- [$filter](/graph/query-parameters#filter-parameter) パラメーターによって、返される結果の期間が制限されます。また、signInEventTypes プロパティが使用されているため、対話ユーザー以外のサインインのみが返されます。

さまざまなサインインの種類に基づくフィルタリングには、次の値を利用できます。 

- interactiveUser
- nonInteractiveUser
- servicePrincipal 
- managedIdentity

## <a name="next-steps"></a>次のステップ

* [サインイン アクティビティ レポートのエラー コード](./concept-sign-ins.md)
* [Azure AD のデータ保有ポリシー](reference-reports-data-retention.md)
* [Azure AD のレポート待機時間](reference-reports-latencies.md)
