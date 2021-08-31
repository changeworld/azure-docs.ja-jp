---
title: ロール ベースの承認
titleSuffix: Azure Cognitive Search
description: サービス管理とコンテンツのタスクでのきめ細かいアクセス許可のために、Azure のロールベースのアクセス制御 (Azure RBAC) を使用します。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 07/23/2021
ms.openlocfilehash: 6ffad57e87b61b9198102ddf8ae4ec8f1a9002ac
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/14/2021
ms.locfileid: "122177829"
---
# <a name="use-role-based-authorization-in-azure-cognitive-search"></a>Azure Cognitive Search でロールベースの承認を使用する

Azure では、プラットフォームで実行されるすべてのサービスに対し、グローバルな[ロールベースのアクセス制御 (RBAC) 承認システム](../role-based-access-control/role-assignments-portal.md)が提供されます。 Cognitive Search では、次の方法でロールの承認を使用できます。

+ 所有者、共同作成者、閲覧者のロールを使用して、検索サービス自体での容量の追加やキーのローテーションなどのコントロール プレーン操作へのアクセスを許可します。

+ インデックスの作成やクエリなど、データ プレーン操作へのアクセスを許可します。 この機能は現在パブリック プレビュー ([要求による](https://aka.ms/azure-cognitive-search/rbac-preview)) の段階です。 サブスクリプションがオンボードされた後、この記事の手順に従って機能を使用します。

+ [マネージド ID を使用](search-howto-managed-identities-data-sources.md)して行われるアウトバウンド インデクサー接続を許可します。 マネージド ID が割り当てられている検索サービスの場合、Azure Blob Storage などの外部データサービスを拡張するロールの割り当てを作成して、信頼された検索サービスによる BLOB への読み取りアクセスを許可することができます。

この記事では、最初の 2 つの項目、つまりコントロール プレーンとデータ プレーンの操作に対するロールについて説明します。 アウトバウンド インデクサー呼び出しの詳細については、[マネージド ID の構成](search-howto-managed-identities-data-sources.md)に関する記事を参照してください。

次のようないくつかの RBAC シナリオは、直接サポートされて **いません**。

+ [カスタム ロール](../role-based-access-control/custom-roles.md)

+ 検索結果に対するユーザー ID アクセス (行レベルセキュリティまたはドキュメント レベル セキュリティと呼ばれることもあります)

  > [!Tip]
  > ドキュメント レベル セキュリティの場合の回避策としては、[セキュリティ フィルター](search-security-trimming-for-azure-search.md)を使用してユーザー ID 別に結果を整理し、要求者がアクセスしてはならないドキュメントを削除します。
  >

## <a name="roles-used-in-search"></a>検索で使用されるロール

組み込みのロールには一般提供されているロールとプレビューのロールが含まれ、それらが割り当てられるメンバーシップは Azure Active Directory ユーザーとグループで構成されます。

ロールの割り当ては、検索サービスを作成または管理するために使用されるすべてのツールとクライアント ライブラリの間で累積され、そのすべてに適用されます。 クライアントには、Azure portal、Management REST API、Azure PowerShell、Azure CLI、Azure SDK の管理クライアント ライブラリが含まれます。 

Azure Cognitive Search で RBAC を使用する場合、リージョン、レベル、または料金の制限はありませんが、検索サービスは Azure パブリック クラウド内に存在する必要があります。

| Role | Status | 適用対象 | 説明 |
| ---- | -------| ---------- | ----------- |
| [所有者](../role-based-access-control/built-in-roles.md#owner) | Stable | コントロール プレーン | Azure ロールを割り当てる機能を含む、リソースへのフル アクセス。 サブスクリプション管理者は既定でメンバーになります。 |
| [Contributor](../role-based-access-control/built-in-roles.md#contributor) | Stable | コントロール プレーン | 所有者と同じレベルのアクセス権から、ロールを割り当てる能力を除いたもの。 |
| [Reader](../role-based-access-control/built-in-roles.md#reader) | Stable | コントロール プレーン | 一部のサービス情報へのアクセスが制限されています。 ポータルでは、閲覧者ロールはサービスの [概要] ページの [要点] セクションと [監視] タブで情報にアクセスできます。他のすべてのタブとページにはアクセスできません。 </br></br>このロールは、サービス情報 (リソース グループ、サービスの状態、場所、サブスクリプションの名前と ID、タグ、URL、価格レベル、レプリカ、パーティション、検索単位) にアクセスできます。 </br></br>また、このロールは、サービス メトリック (検索の待ち時間、調整された要求の割合、1 秒あたりの平均クエリ数) にもアクセスできます。 </br></br>API キー、ロールの割り当て、コンテンツ (インデックスまたは同意語マップ)、コンテンツ メトリック (使用されているストレージ、オブジェクトの数) にはアクセスできません。 |
| [Search Service Contributor](../role-based-access-control/built-in-roles.md#search-service-contributor) | プレビュー | コントロール プレーン | 検索サービスとオブジェクト定義へのフル アクセスが提供されますが、インデックス付きデータにはアクセスできません。 このロールは、閲覧者ロールで提供されるものより詳細な情報を必要とするが、インデックスや同義語マップ コンテンツにアクセスしてはならない、サービス管理者を対象としたものです。|
| [検索インデックス データ共同作成者](../role-based-access-control/built-in-roles.md#search-index-data-contributor) | プレビュー | データ プレーン | インデックス データへのフル アクセスを提供しますが、それ以外は何もありません。 このロールは、コンテンツの作成と読み込みを担当するが、検索サービスの情報にアクセスしてはならない、開発者またはインデックス所有者のためのものです。 スコープは、検索サービスのすべての最上位リソース (インデクサー、同義語マップ、インデクサー、データ ソース、スキルセット) です。 |
| [検索インデックス データ閲覧者](../role-based-access-control/built-in-roles.md#search-index-data-reader) | プレビュー | データ プレーン | インデックス データへの読み取り専用アクセス権を提供します。 このロールは、インデックスに対してクエリを実行するユーザーのためのものです。 スコープは、検索サービスのすべての最上位リソース (インデクサー、同義語マップ、インデクサー、データ ソース、スキルセット) です。 |

## <a name="scope-control-plane-and-data-plane"></a>スコープ: コントロール プレーンとデータ プレーン

Azure リソースの操作には、[コントロール プレーンとデータ プレーン](../azure-resource-manager/management/control-plane-and-data-plane.md)のカテゴリの概念があります。 Cognitive Search の組み込みロールは、どちらか一方のプレーンに適用されます。

| カテゴリ | Operations |
|----------|------------|
| コントロール プレーン | 操作には、サービスの作成、更新、削除、API キーの管理、パーティションとレプリカの調整などが含まれます。 [Management REST API](/rest/api/searchmanagement/) および同等のクライアント ライブラリでは、コントロール プレーンに適用される操作が定義されています。 |
| データ プレーン | サービスでホストされているすべてのオブジェクトとデータを含む、検索サービスのエンドポイントに対する操作。 インデックスの作成、クエリの実行、および関連するすべてのアクションは、データ プレーンが対象であり、[Search REST API](/rest/api/searchservice/) およびそれと同等のクライアント ライブラリを介してアクセスされます。 </br></br>現時点では、ロールの割り当てを使用して、個別のインデックス、同義語マップ、インデクサー、データ ソース、またはスキルセットへのアクセスを制限することはできません。 |

## <a name="configure-search-for-data-plane-authentication"></a>データ プレーンの認証用に検索を構成する

プレビューのデータ プレーン ロール (検索インデックス データ共同作成者または検索インデックス データ閲覧者) のいずれかと Azure AD 認証を使用している場合は、OAuth2 アクセス トークンを提供するデータ要求で **Authorization** ヘッダーを認識するように、検索サービスを構成する必要があります。 このセクションでは、検索サービスを構成する方法について説明します。 コントロール プレーンのロール (所有者、共同作成者、閲覧者) を使用している場合は、このステップを省略できます。

始める前に、RBAC プレビューに[サインアップ](https://aka.ms/azure-cognitive-search/rbac-preview)します。 この機能を使用する前に、サブスクリプションがプログラムに登録されている必要があります。 登録要求の処理には、最大で 2 営業日かかります。 サービスの準備ができたら、メールを受け取ります。

### <a name="azure-portal"></a>[**Azure portal**](#tab/config-svc-portal)

1. 次の構文を使用してポータルを開きます: [https://ms.portal.azure.com/?feature.enableRbac=true](https://ms.portal.azure.com/?feature.enableRbac=true)。

1. お使いの検索サービスに移動します。

1. 左側のナビゲーション ペインで **[キー]** を選択します。

1. **[API access control]\(API アクセス制御\)** メカニズムを選択します。 これらのオプションが表示されない場合は、ポータルの URL を確認してください。 選択を保存できない場合は、サブスクリプションの登録に問題があります。 

   | オプション | 状態 | 説明 |
   |--------|--------|-------------|
   | API キー | 一般提供 (既定値) | 承認するには、要求ヘッダーに[管理者またはクエリの API キー](search-security-api-keys.md)が必要です。 ロールは使用されません。 |
   | ロールベースのアクセス制御 | プレビュー | 次のステップで説明するように、タスクを完了するには、ロール割り当てのメンバーシップが必要です。 また、Authorization ヘッダーも必要です。 このオプションを選択すると、2021-04-30-preview REST API をサポートするクライアントに制限されます。 |
   | 両方 | プレビュー | API キーまたは認証トークンのいずれかを使用する要求が有効です。 |

検索サービスで RBAC が有効になったら、ポータルで、ロールの割り当てとコンテンツの表示のために URL で機能フラグが必要になります。 **インデックスやインデクサーなどのコンテンツは、機能フラグを使用して開いた場合にのみ、ポータルに表示されます。** 後で既定の動作に戻したい場合は、API キーの選択を **[API キー]** に戻します。

### <a name="rest-api"></a>[**REST API**](#tab/config-svc-rest)

Management REST API バージョン 2021-04-01-Preview を使用して、サービスを構成します。

1. [サービスの作成または更新](/rest/api/searchmanagement/2021-04-01-preview/services/create-or-update)を呼び出します。

1. [DataPlaneAuthOptions](/rest/api/searchmanagement/2021-04-01-preview/services/create-or-update#dataplaneauthoptions) を `aadOrApiKey` に設定します。 構文については、[こちらの例](/rest/api/searchmanagement/2021-04-01-preview/services/create-or-update#searchcreateorupdateserviceauthoptions)を参照してください。

1. [AadAuthFailureMode](/rest/api/searchmanagement/2021-04-01-preview/services/create-or-update#aadauthfailuremode) を設定して、認証失敗のときに 401 または 403 エラーを返すかどうかを指定します。

---

## <a name="assign-roles"></a>ロールを割り当てる

ロールは、Azure のロールベースのアクセス制御のドキュメントで説明されている、[サポートされている方法](../role-based-access-control/role-assignments-steps.md)のいずれかを使用して割り当てることができます。

ロールの割り当てを管理するには、所有者であるか、または [Microsoft.Authorization/roleAssignments/write](/azure/templates/microsoft.authorization/roleassignments) アクセス許可を持っている必要があります。

### <a name="azure-portal"></a>[**Azure portal**](#tab/rbac-portal)

プレビュー ロール (Search Service 共同作成者、検索インデックス データ共同作成者、検索インデックス データ閲覧者) を使用するには、ポータルの URL で機能フラグを設定します。

1. 次の構文を使用してポータルを開きます: [https://ms.portal.azure.com/?feature.enableRbac=true](https://ms.portal.azure.com/?feature.enableRbac=true)。 URL に `feature.enableRbac=true` が表示されます。

1. お使いの検索サービスに移動します。

1. 左側のナビゲーション ペインで **[アクセス制御 (IAM)]** を選択します。

1. 右側の **[Grant access to this resource]\(このリソースへのアクセスを許可する\)** で、 **[ロールの割り当てを追加]** を選択します。

1. 該当するロール (所有者、共同作成者、閲覧者、Search Service 共同作成者、検索インデックス データ共同作成者、検索インデックス データ閲覧者) を検索し、Azure Active Directory のユーザーまたはグループの ID を割り当てます。

### <a name="powershell"></a>[**PowerShell**](#tab/rbac-powershell)

[PowerShell を使用してロールを割り当てる](../role-based-access-control/role-assignments-powershell.md)場合は、[New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment) を呼び出して、Azure のユーザーまたはグループの名前と、割り当てのスコープを指定します。

始める前に、Azure と AzureAD のモジュールを読み込み、Azure に接続していることを確認してください。

```powershell
Import-Module -Name Az
Import-Module -Name AzureAD
Connect-AzAccount
```

スコープがサービスに設定されている場合、構文は次の例のようになります。

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Search Index Data Contributor" `
    -Scope  "/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Search/searchServices/<search-service>"
```

スコープが個々のインデックスに設定されている場合:

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Search Index Data Contributor" `
    -Scope  "/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Search/searchServices/<search-service>/indexes/<index-name>"
```

インデックス、同義語マップ、インデクサー、データ ソース、スキルセットなど、最上位レベルのリソースにのみ、アクセスのスコープを設定できることを思い出してください。 Azure ロールでドキュメント (インデックス コンテンツ) の検索へのアクセスを制御することはできません。

---

## <a name="configure-requests-and-test"></a>要求とテストを構成する

プログラムでテストするには、Search REST API (サポートされている任意のバージョン) を使用するようにコードを変更し、要求に Authorization ヘッダーを設定します。 いずれかの Azure SDK を使用している場合は、そのベータ リリースで認証ヘッダーが使用可能かどうかを確認してください。 

アプリケーションによっては、アプリケーションを Azure Active Directory に登録したり、承認トークンを取得して渡すために、追加の構成が必要になります。

または、Azure portal と自分に割り当てられたロールを使用して、テストを行うこともできます。

1. 次の構文を使用してポータルを開きます: [https://ms.portal.azure.com/?feature.enableRbac=true](https://ms.portal.azure.com/?feature.enableRbac=true)。 

   前のステップでサービスは RBAC 対応になっていますが、ポータルで RBAC 動作を呼び出すためには機能フラグが必要です。 **インデックスやインデクサーなどのコンテンツは、機能フラグを使用して開いた場合にのみ、ポータルに表示されます。** 既定の動作に戻したい場合は、API キーの選択を **[API キー]** に戻します。

1. お使いの検索サービスに移動します。

1. [概要] ページで、 **[インデックス]** タブを選択します。

   + 検索インデックス データ閲覧者のメンバーシップの場合は、Search エクスプローラーを使用してインデックスのクエリを実行します。 任意の API バージョンを使用して、アクセスを確認できます。

   + 検索インデックス データ共同作成者の場合、新しいインデックスを作成するには **[新しいインデックス]** を選択します。 新しいインデックスを保存すると、サービスに対する書き込みアクセス権が検証されます。

## <a name="disable-api-key-authentication"></a>API キー認証を無効にする

API キーを削除することはできませんが、サービスで無効にすることはできます。 検索インデックス データ共同作成者と検索インデックス データ閲覧者のロールおよび Azure AD 認証を使用している場合は、API キーを無効にすることができ、これにより検索サービスでキーを提供するすべてのデータ関連要求が拒否されるようになります。

このタスクには、プレビューの Management REST API バージョン 2021-04-01-preview を使用します。

1. [DataPlaneAuthOptions](/rest/api/searchmanagement/2021-04-01-preview/services/create-or-update#dataplaneauthoptions) を `aadOrApiKey` に設定します。

1. [ロールを割り当て](#assign-roles)、正常に機能していることを確認します。

1. `disableLocalAuth` を **True** に設定します。

最後のステップを元に戻した場合 (`disableLocalAuth` を **False** に設定)、検索サービスで要求の API キーの受け入れが自動的に再開されます (機能が指定されている場合)。