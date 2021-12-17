---
title: ロール ベースの承認
titleSuffix: Azure Cognitive Search
description: サービス管理とコンテンツのタスクでのきめ細かいアクセス許可のために、Azure のロールベースのアクセス制御 (Azure RBAC) を使用します。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/04/2021
ms.openlocfilehash: b26334c655332810ad1f67ae6799c3919fda4bb4
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2021
ms.locfileid: "132517819"
---
# <a name="use-role-based-authorization-in-azure-cognitive-search"></a>Azure Cognitive Search でロールベースの承認を使用する

Azure では、プラットフォームで実行されるすべてのサービスに対し、グローバルな[ロールベースのアクセス制御 (RBAC) 承認システム](../role-based-access-control/role-assignments-portal.md)が提供されます。 Cognitive Search では、次の方法でロールを使用できます。

+ 一般提供のロールをサービス管理に使用する。

+ コンテンツ管理 (インデックスおよびその他の最上位レベルのオブジェクトの作成と管理) に新しいプレビューロールを使用します。 [**プレビューで利用でき**](#step-1-preview-sign-up)ます。

> [!NOTE]
> Search サービス共同作成者は、"プレビュー" 機能を持つ "一般提供" のロールです。 これは、サービスとコンテンツ管理タスクの真のハイブリッドをサポートする唯一のロールであり、これにより、特定の検索サービスですべての操作を実行できます。 このロールのコンテンツ管理のプレビュー機能を入手するには、[**プレビューにサインアップ**](#step-1-preview-sign-up)します。

一部の RBAC シナリオは、サポートされて **いない** か、この記事で説明されていません。

+ 送信インデクサー接続については、「[Set up an indexer connection to a data source using a managed identity](search-howto-managed-identities-data-sources.md)」(マネージド ID を使用してデータ ソースへのインデクサー接続を設定する) で説明されています。 マネージド ID が割り当てられている検索サービスの場合、Azure Blob Storage などの外部データサービスを許可するロールの割り当てを作成して、信頼された検索サービスによる BLOB への読み取りアクセスを許可することができます。

+ 検索結果に対するユーザー ID アクセス (行レベル セキュリティまたはドキュメントレベル セキュリティと呼ばれることもあります) はサポートされていません。 ドキュメント レベル セキュリティの場合の回避策としては、[セキュリティ フィルター](search-security-trimming-for-azure-search.md)を使用してユーザー ID 別に結果を整理し、要求者がアクセスしてはならないドキュメントを削除します。

## <a name="built-in-roles-used-in-search"></a>Search で使用される組み込みロール

Cognitive Search では、組み込みのロールに、一般提供されているロールとプレビューのロールが含まれており、それらが割り当てられるメンバーシップは、Azure Active Directory ユーザーとグループで構成されます。

ロールの割り当ては、検索サービスを作成または管理するために使用されるすべてのツールとクライアント ライブラリの間で累積され、そのすべてに適用されます。 これらのクライアントには、Azure portal、Management REST API、Azure PowerShell、Azure CLI、Azure SDK の管理クライアント ライブラリが含まれます。

ロールは、検索サービス全体に適用され、所有者によって割り当てられる必要があります。 特定のインデックスや他のトップレベル オブジェクトにロールを割り当てることはできません。

Azure Cognitive Search で RBAC を使用する場合、リージョン、レベル、または料金の制限はありませんが、検索サービスは Azure パブリック クラウド内に存在する必要があります。

| Role | 適用対象 | 説明 |
| ---- | ---------- | ----------- |
| [所有者](../role-based-access-control/built-in-roles.md#owner) | サービス操作 (一般提供) | Azure ロールを割り当てる機能を含む、リソースへのフル アクセス。 サブスクリプション管理者は既定でメンバーになります。 |
| [Contributor](../role-based-access-control/built-in-roles.md#contributor) | サービス操作 (一般提供) | ロールの割り当て、承認オプションの変更を除いて、所有者と同じアクセス レベル。 |
| [Reader](../role-based-access-control/built-in-roles.md#reader) | サービス操作 (一般提供) | 一部のサービス情報へのアクセスが制限されています。 ポータルでは、閲覧者ロールはサービスの [概要] ページの [要点] セクションと [監視] タブで情報にアクセスできます。他のすべてのタブとページにはアクセスできません。 </br></br>このロールは、サービス情報 (リソース グループ、サービスの状態、場所、サブスクリプションの名前と ID、タグ、URL、価格レベル、レプリカ、パーティション、検索単位) にアクセスできます。 </br></br>また、このロールは、サービス メトリック (検索の待ち時間、調整された要求の割合、1 秒あたりの平均クエリ数) にもアクセスできます。 </br></br>API キー、ロールの割り当て、コンテンツ (インデックスまたは同意語マップ)、コンテンツ メトリック (使用されているストレージ、オブジェクトの数) にはアクセスできません。 |
| [Search Service Contributor](../role-based-access-control/built-in-roles.md#search-service-contributor) | サービス操作 (一般提供)、およびトップレベルのオブジェクト (プレビュー) | このロールは、サービス レベルでの共同作成者の組み合わせですが、[`Microsoft.Search/searchServices/*`](../role-based-access-control/resource-provider-operations.md#microsoftsearch) を介して、インデックス、同意語マップ、インデクサー、データソース、スキルセットですべてのアクションにフル アクセスできます。 このロールは、サービスを完全に管理する必要がある検索サービス管理者用です。 </br></br>共同作成者と同様、この役割のメンバーは、ロール割り当ての作成または管理を作成することも、承認オプションを変更することもできません。 |
| [検索インデックス データ共同作成者](../role-based-access-control/built-in-roles.md#search-index-data-contributor) | ドキュメント コレクション (プレビュー) | 検索サービスですべてのインデックス内のコンテンツに対するフル アクセスを提供します。 このロールは、インデックスのドキュメント コレクションのインポート、更新、またはクエリを行う必要がある開発者またはインデックス所有者向けです。 |
| [検索インデックス データ閲覧者](../role-based-access-control/built-in-roles.md#search-index-data-reader) | ドキュメント コレクション (プレビュー) | 検索サービスで検索インデックスへの読み取り専用アクセスを提供します。 このロールは、クエリを実行するアプリおよびユーザー向けです。 |

> [!NOTE]
> Azure リソースの操作には、[コントロール プレーンとデータ プレーン](../azure-resource-manager/management/control-plane-and-data-plane.md)のカテゴリの概念があります。 Cognitive Search では、"コントロール プレーン" は、[Management REST API](/rest/api/searchmanagement/) または同等のクライアント ライブラリでサポートされる操作を意味します。 "データ プレーン" は、インデックス作成やクエリなどの検索サービス エンドポイントに対する操作、または [Search REST API](/rest/api/searchservice/) または同等のクライアント ライブラリで指定されるその他の操作を意味します。 ほとんどのロールは、1 つのプレーンにのみ適用されます。 例外は、Search サービス共同作成者です。これは、両方にわたってアクションをサポートします。

## <a name="step-1-preview-sign-up"></a>手順 1: プレビューのサインアップ

**適用対象:** Search インデックス データ共同作成者、Search インデックス データ閲覧者、Search サービス共同作成者

一般提供のロール (所有者、共同作成者、閲覧者)、または Search サービス共同作成者のサービスレベル アクションのみを使用する場合、この手順をスキップします。

新しい組み込みプレビュー ロールを使用すると、検索サービスのコンテンツ全体に対するアクセス許可の詳細なセットが提供されます。 組み込みロールは Azure portal に常に表示されますが、これらを運用するには、サービス登録が必要です。

サブスクリプションをプレビューに追加するには、次のようにします：

1. [Azure portal](https://portal.azure.com/) で、検索サービスに移動します。
1. ページの左側で **[キー]** を選択します。
1. プレビューを示す青いバナーで、 **[登録]** を選択して、サブスクリプションに機能を追加します。

![ポータルで rbac プレビューにサインアップする方法のスクリーンショット](media/search-howto-aad/rbac-signup-portal.png)

Azure Feature Exposure Control (AFEC) を使用してプレビューにサインアップし、*Search Service のロールベースのアクセス制御 (プレビュー)* を検索することもできます。 プレビュー機能の追加の詳細については、「 [Azure サブスクリプションでプレビュー機能を設定](../azure-resource-manager/management/preview-features.md?tabs=azure-portal)する」を参照してください。

> [!NOTE]
> サブスクリプションにプレビューを追加すると、サブスクリプション内のすべてのサービスがプレビューに永続的に登録されます。 特定のサービスで RBAC が必要ない場合は、次の手順に示すように、データ プレーン操作に RBAC を無効にすることができます。

## <a name="step-2-preview-configuration"></a>手順 2: プレビューの構成

**適用対象:** Search インデックス データ共同作成者、Search インデックス データ閲覧者、Search サービス共同作成者

一般提供のロール (所有者、共同作成者、閲覧者)、または Search サービス共同作成者のサービスレベル アクションのみを使用する場合、この手順をスキップします。

この手順では、OAuth2 アクセス トークンを提供するデータ要求の **承認** ヘッダーを認識するように検索サービスを構成します。

### <a name="azure-portal"></a>[**Azure portal**](#tab/config-svc-portal)

1. [Azure Portal](https://ms.portal.azure.com)を開きます。

1. お使いの検索サービスに移動します。

1. 左側のナビゲーション ペインで **[キー]** を選択します。

1. **[API access control]\(API アクセス制御\)** メカニズムを選択します。 

   | オプション | 状態 | 説明 |
   |--------|--------|-------------|
   | API キー | 一般提供 (既定値) | 承認するには、要求ヘッダーに[管理者またはクエリの API キー](search-security-api-keys.md)が必要です。 ロールは使用されません。 |
   | ロールベースのアクセス制御 | プレビュー | 次のステップで説明するように、タスクを完了するには、ロール割り当てのメンバーシップが必要です。 また、Authorization ヘッダーも必要です。 このオプションを選択すると、2021-04-30-preview REST API をサポートするクライアントに制限されます。 |
   | 両方 | プレビュー | API キーまたは認証トークンのいずれかを使用する要求が有効です。 |

オプションが表示されない場合、ポータルの URL をご確認ください。

選択を保存できない場合、または "API アクセス制御が検索サービス `<name>` の更新に失敗しました。 DisableLocalAuth はプレビューであり、このサブスクリプションでは有効になっていません" が発生した場合、サブスクリプションの登録は開始されていないか、処理されていません。

### <a name="rest-api"></a>[**REST API**](#tab/config-svc-rest)

Management REST API バージョン 2021-04-01-Preview の[Create または Update サービス](/rest/api/searchmanagement/2021-04-01-preview/services/create-or-update)を使用して、サービスを構成します。

Postman または別の Web テスト ツールを使用する場合は、要求の設定に関するヘルプについて、以下のヒントを参照してください。

1. ["AuthOptions"](/rest/api/searchmanagement/2021-04-01-preview/services/create-or-update#dataplaneauthoptions) を "aadOrApiKey" に設定します。

   必要に応じて、["AadAuthFailureMode"](/rest/api/searchmanagement/2021-04-01-preview/services/create-or-update#aadauthfailuremode) を設定して、認証に失敗した場合、403 ではなく 401 を返すかどうかを指定します。 "disableLocalAuth" の既定値は false であるため、これを設定する必要はありませんが、authOptions が設定されている場合は常に false である必要があることを強調するために、これを以下に示します。

    ```http
    PUT https://management.azure.com/subscriptions/{{subscriptionId}}/resourcegroups/{{resource-group}}/providers/Microsoft.Search/searchServices/{{search-service-name}}?api-version=2021-04-01-Preview
    {
      "location": "{{region}}",
      "sku": {
        "name": "standard"
      },
      "properties": {
        "disableLocalAuth": false,
        "authOptions": {
          "aadOrApiKey": {
            "aadAuthFailureMode": "http401WithBearerChallenge"
          }
        }
      }
   }
    ```

1. サービスに [ロールを割り当て](#assign-roles)て、データ プレーンに対して正しく機能することを確認します。

> [!TIP]
> Management REST API 呼び出しは、Azure Active Directory を介して認証されます。 セキュリティの原則と要求の設定に関するガイダンスについては、ブログ記事「[Postman を使用した Azure REST API (2021)](https://blog.jongallant.com/2021/02/azure-rest-apis-postman-2021/)」を参照してください。 前の例は、このブログ記事に記載されている命令と Postman コレクションを使用してテストされています。

---

<a name="assign-roles"></a>

## <a name="step-3-assign-roles"></a>手順 3: ロールの割り当て

ロールは、Azure のロールベースのアクセス制御のドキュメントで説明されている、[サポートされている方法](../role-based-access-control/role-assignments-steps.md)のいずれかを使用して割り当てることができます。

ロールの割り当てを管理するには、**所有者** であるか、[Microsoft.Authorization/roleAssignments/write](/azure/templates/microsoft.authorization/roleassignments) アクセス許可を持っている必要があります。

### <a name="azure-portal"></a>[**Azure portal**](#tab/roles-portal)

1. [Azure Portal](https://ms.portal.azure.com)を開きます。

1. お使いの検索サービスに移動します。

1. 左側のナビゲーション ペインで **[アクセス制御 (IAM)]** を選択します。

1. 右側の **[Grant access to this resource]\(このリソースへのアクセスを許可する\)** で、 **[ロールの割り当てを追加]** を選択します。

1. 適用可能なロールを見つけて、Azure Active Directory ユーザーまたはグループ ID を割り当てます。

   + 所有者
   + Contributor
   + Reader
   + Search Service Contributor
   + Search インデックス データ共同作成者 (プレビュー)
   + Search インデックス データ閲覧者 (プレビュー)

### <a name="powershell"></a>[**PowerShell**](#tab/roles-powershell)

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

## <a name="step-4-test"></a>手順 4: テスト

### <a name="azure-portal"></a>[**Azure portal**](#tab/test-portal)

1. [Azure Portal](https://ms.portal.azure.com)を開きます。

1. お使いの検索サービスに移動します。

1. [概要] ページで、 **[インデックス]** タブを選択します。

   + Search インデックス データ閲覧者のメンバーは、Search エクスプローラーを使用してインデックスのクエリを実行できます。 任意の API バージョンを使用して、アクセスを確認できます。 クエリを発行し、結果を表示できるようにする必要がありますが、インデックス定義を表示できる必要はありません。

   + Search インデックス データ共同作成者のメンバーは、 **[新しいインデックス]** を選択して、新しいインデックスを作成できます。 新しいインデックスを保存すると、サービスに対する書き込みアクセス権が検証されます。

### <a name="rest-api"></a>[**REST API**](#tab/test-rest)

+ Azure Active Directory にアプリケーションを登録します。

+ [Search REST API](/rest/api/searchservice/) (サポートされている任意のバージョン) を使用するようにコードを変更し、要求に **Authorization** ヘッダーを設定して、**api-key** ヘッダーを置き換えます。

  :::image type="content" source="media/search-security-rbac/rest-authorization-header.png" alt-text="Authorization ヘッダーを含む HTTP 要求のスクリーンショット" border="true":::

特定の環境のトークンを取得する方法の詳細については、「[Microsoft ID プラットフォームの認証ライブラリ](../active-directory/develop/reference-v2-libraries.md)」を参照してください。

### <a name="net-sdk"></a>[ **.NET SDK**](#tab/test-dotnet)

The Azure SDK for .NET では、[NuGet Gallery | Azure.Search.Documents 11.4.0-beta.2](https://www.nuget.org/packages/Azure.Search.Documents/11.4.0-beta.2) パッケージの Authorization ヘッダーがサポートされています。

アプリケーションを Azure Active Directory に登録したり、承認トークンを取得して渡したりするには、追加の構成が必要です。

+ OAuth トークンを取得する場合、スコープは "https://search.azure.com/.default" です。 SDK では、対象ユーザーを "https://search.azure.com" にする必要があります。 ".default" は、Azure AD 規約です。

+ SDK では、ユーザーが"user_impersonation" スコープを持っていることを検証します。このスコープは、アプリによって付与される必要がありますが、SDK 自体は "https://search.azure.com/.default"を要求するだけです。

[クライアント シークレット資格情報](/dotnet/api/azure.core.tokencredential)の使用例:

```csharp
var tokenCredential =  new ClientSecretCredential(aadTenantId, aadClientId, aadSecret);
SearchClient srchclient = new SearchClient(serviceEndpoint, indexName, tokenCredential);
```

[Azure SDK for .NET での AAD 認証](https://github.com/Azure/azure-sdk-for-net/tree/main/sdk/identity/Azure.Identity)の使用に関するその他の詳細については、SDK の GitHub レポジトリを参照してください。

> [!NOTE]
> 403 エラーが発生した場合、検索サービスがプレビュー プログラムに登録されていること、およびお使いのサービスがプレビュー ロールの割り当て用に構成されていることを確認します。

---

## <a name="disable-api-key-authentication"></a>API キー認証を無効にする

API キーを削除することはできませんが、サービスで無効にすることはできます。 Search サービス共同作成者、Search インデックス データ共同作成者、Search インデックス データ閲覧者のロールと Azure AD 認証を使用している場合、API キーを無効にすることができます。これにより、検索サービスでは、コンテンツ関連の要求のヘッダーで API キーを渡すすべてのデータ関連の要求が拒否されます。

[キーベースの認証](search-security-api-keys.md)を無効にするには、Management REST API version 2021-04-01-Preview を使用し、[更新サービス](/rest/api/searchmanagement/2021-04-01-preview/services/create-or-update)に対して 2 つの連続する要求を送信します。

機能を無効にするには、所有者または共同作成者のアクセス許可が必要です。 Postman または別の Web ツールを使用して、次の手順を完了します (以下の品後を参照してください)。

1. 最初の要求で、["AuthOptions"](/rest/api/searchmanagement/2021-04-01-preview/services/create-or-update#dataplaneauthoptions) を "aadOrApiKey"に設定して、Azure AD 認証を有効にします。 オプションは、Azure AD またはネイティブ API キーのいずれかのアプローチを使用できることを示していることに注意してください。

    ```http
    PUT https://management.azure.com/subscriptions/{{subscriptionId}}/resourcegroups/{{resource-group}}/providers/Microsoft.Search/searchServices/{{search-service-name}}?api-version=2021-04-01-Preview
    {
      "location": "{{region}}",
      "sku": {
        "name": "standard"
      },
      "properties": {
        "authOptions": {
          "aadOrApiKey": {
            "aadAuthFailureMode": "http401WithBearerChallenge"
          }
        }
      }
   }
    ```

1. 2 番目の要求で、["disableLocalAuth"](/rest/api/searchmanagement/2021-04-01-preview/services/create-or-update#request-body) を true に設定します。 この手順では、"aadOrApiKey" オプションの API キー部分がオフになり、Azure AD 認証のみが残ります。

    ```http
    PUT https://management.azure.com/subscriptions/{{subscriptionId}}/resourcegroups/{{resource-group}}/providers/Microsoft.Search/searchServices/{{search-service-name}}?api-version=2021-04-01-Preview
    {
      "location": "{{region}}",
      "sku": {
        "name": "standard"
      },
      "properties": {
        "disableLocalAuth": true
      }
    }
    ```

手順 1 と 2 を組み合わせることはできません。 手順 1 では、"AuthOptions" を設定するための要件を満たすために、"disableLocalAuth" を false にする必要がありますが、ステップ 2 では、その値を true に変更します。

キー認証を再度有効にするには、最後の要求を再実行して、"disableLocalAuth" を false に設定します。 検索サービスでは、要求での API キーの受け入れが自動的に再開されます (機能が指定されている場合)。

> [!TIP]
> Management REST API 呼び出しは、Azure Active Directory を介して認証されます。 セキュリティの原則と要求の設定に関するガイダンスについては、ブログ記事「[Postman を使用した Azure REST API (2021)](https://blog.jongallant.com/2021/02/azure-rest-apis-postman-2021/)」を参照してください。 前の例は、このブログ記事に記載されている手順と Postman コレクションを使用してテストされています。

## <a name="conditional-access"></a>条件付きアクセス

[条件付きアクセス](../active-directory/conditional-access/overview.md)は、組織のポリシーを適用するために Azure Active Directory によって使用されるツールです。 条件付きアクセス ポリシーを使用すると、必要な場合は適切なアクセス制御を適用して組織のセキュリティを維持することができます。 ロールベースのアクセス制御を使用して Azure Cognitive Search サービスにアクセスすると、条件付きアクセスにより、組織ポリシーを適用することができます。

Azure Cognitive Search の条件付きアクセス ポリシーを有効にするには、次の手順を実行します。
1. Azure portal に[サインイン](https://portal.azure.com)します。
1. **Azure AD 条件付きアクセス** を探します。
1. **[ポリシー]** を選択します。
1. **[新しいポリシー]** を選択します。
1. ポリシーの **[クラウド アプリまたは操作]** セクションで、ポリシーの設定方法に応じて、クラウド アプリとして **Azure Cognitive Search** を追加します。
1. ポリシーの残りのパラメーターを更新します。 たとえば、このポリシーを適用するユーザーとグループを指定します。 
1. ポリシーを保存します。

> [!IMPORTANT]
> 検索サービスにマネージド ID が割り当てられている場合、その検索サービスは、条件付きアクセス ポリシーの一部として含めたり、除外したりすることができるクラウド アプリとして表示されます。 条件付きアクセス ポリシーを特定の検索サービスに適用することはできません。 代わりに、一般的な **Azure Cognitive Search** クラウド アプリを選択してください。
