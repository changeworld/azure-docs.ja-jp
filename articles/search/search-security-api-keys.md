---
title: 管理者 API キーとクエリ API キーの作成、管理、およびセキュリティ保護 - Azure Search
description: API キーは、サービス エンドポイントへのアクセスを制御します。 管理者キーは、書き込みアクセス権を付与します。 読み取り専用アクセスのために、クエリ キーを作成できます。
author: HeidiSteen
manager: cgronlun
tags: azure-portal
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 06/20/2018
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 6ba63fa776e92dd2f8035cfbbdb8cea2860d106f
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/12/2018
ms.locfileid: "53316929"
---
# <a name="create-and-manage-api-keys-for-an-azure-search-service"></a>Azure Search サービスの管理者 API キーを作成する

Search サービスへのすべての HTTP 要求には、対象のサービス用に生成された読み取り専用の API キーが必要です。 この API キーは、その Search サービス エンドポイントへのアクセスを認証するための唯一のメカニズムであり、すべての要求に含まれる必要があります。 [REST ソリューション](search-get-started-nodejs.md#update-the-configjs-with-your-search-service-url-and-api-key)では、通常、API キーは要求のヘッダーで指定されます。 [.NET ソリューション](search-howto-dotnet-sdk.md#core-scenarios)では、多くの場合、キーは構成の設定として指定され、[SearchServiceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient) 上の [Credentials](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient.credentials) (管理者キー) または [SearchCredentials](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient.searchcredentials) (クエリ キー) として渡されます。

キーは、サービスのプロビジョニング中に検索サービスで作成されます。 [Azure portal](https://portal.azure.com) でキーの値を表示および取得することができます。

![ポータル ページの [設定] の [キー] セクション](media/search-manage/azure-search-view-keys.png)

## <a name="what-is-an-api-key"></a>API キーとは

API キーは、ランダムに生成された数字と文字から成る文字列です。 [ロールに基づいたアクセス許可](search-security-rbac.md)により、キーの削除や読み取りは可能ですが、ユーザー定義のパスワードでキーを置き換えたり、検索操作にアクセスするためのプライマリ認証方法として Active Directory を使用したりすることはできません。 

検索サービスへのアクセスには、管理者 (読み取り/書き込み) とクエリ (読み取り専用) の 2 種類のキーが使用されます。

|キー|説明|制限|  
|---------|-----------------|------------|  
|[Admin]|サービスの管理のほか、インデックス、インデクサー、データ ソースの作成と削除など、すべての操作に対する完全な権限を付与します。<br /><br /> *プライマリ* キーおよび*セカンダリ* キーと呼ばれる、ポータルの 2 つの管理者キーは、サービスの作成時に生成され、要求に応じて個別に再生成できます。 キーが 2 つあることで、サービスへの継続的なアクセスに 1 つのキーを使用している間に、もう 1 つのキーをロールオーバーできます。<br /><br /> 管理者キーは、HTTP 要求ヘッダーでのみ指定されます。 管理者 API キーを URL に加えることはできません。|最大でサービスあたり 2 つ|  
|クエリ|インデックスとドキュメントに対する読み取り専用アクセスを付与するものであり、通常は、検索要求を発行するクライアント アプリケーションに配布されます。<br /><br /> クエリ キーは要求に応じて作成されます。 これらはポータルで手動で作成できるほか、[管理 REST API](https://docs.microsoft.com/rest/api/searchmanagement/) を通じてプログラムで作成できます。<br /><br /> クエリ キーは、検索、推奨、または参照の操作に使用するために HTTP 要求ヘッダーで指定できます。 または、クエリ キーは URL 上のパラメーターとして渡すことができます。 クライアント アプリケーションが要求を作成する方法によっては、キーをクエリ パラメーターとして渡すほうが簡単な場合があります。<br /><br /> `GET /indexes/hotels/docs?search=*&$orderby=lastRenovationDate desc&api-version=2017-11-11&api-key=[query key]`|サービスあたり 50 個|  

 管理者キーとクエリ キーに見た目の違いはありません。 どちらのキーも、ランダムに生成された 32 個の英数字からなる文字列です。 アプリケーションで指定されているキーの種類がわからなくなった場合、[ポータルでキーの値を確認](https://portal.azure.com)したり、[REST API](https://docs.microsoft.com/rest/api/searchmanagement/) を使用して値とキーの種類を返したりできます。  

> [!NOTE]  
>  `api-key` などの機微なデータを要求 URI で渡すことは、セキュリティ上推奨されません。 そのため、Azure Search はクエリ キーをクエリ文字列の `api-key` としてのみ受け入れます。また、インデックスのコンテンツを公開する必要がない限り、そうすることは避けてください。 一般的なルールとして、`api-key` は要求ヘッダーとして渡すことをお勧めします。  

## <a name="find-api-keys-for-your-service"></a>サービスの API キーを見つける

アクセス キーはポータルで取得するか、または[管理 REST API](https://docs.microsoft.com/rest/api/searchmanagement/) 経由で取得できます。 詳細については、[管理者 API キーとクエリ API キーを管理する](search-security-api-keys.md)をご覧ください。

1. [Azure Portal](https://portal.azure.com) にサインインします。
2. サブスクリプションの[検索サービス](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)を一覧表示します。
3. サービスを選択し、サービスのページで **[設定]** >**[キー]** の順に移動して管理者キーとクエリ キーを表示します。

![ポータル ページの [設定] の [キー] セクション](media/search-security-overview/settings-keys.png)

## <a name="regenerate-admin-keys"></a>管理者キーを再生成する

セカンダリ キーを使用した継続的アクセスのためにプライマリ キーをロール オーバーできるように、各サービスに 2 つの管理キーが作成されます。

プライマリ キーとセカンダリ キーの両方を同時に再生成する場合、いずれかのキーを使用してサービス操作にアクセスしているアプリケーションはすべて、そのサービスにアクセスできなくなります。

1. **[設定]** >**[キー]** ページで、セカンダリ キーをコピーします。
2. すべてのアプリケーションについて、セカンダリ キーを使用するように API キーの設定を更新します。
3. プライマリ キーを再生成します。
4. 新しいプライマリ キーを使用するように、すべてのアプリケーションを更新します。

## <a name="secure-api-keys"></a>API キーをセキュリティ保護する
キーのセキュリティは、ポータルまたはリソース マネージャーのインターフェイス (PowerShell またはコマンドライン インターフェイス) でアクセスを制限することによって確保されます。 前述のように、サブスクリプションの管理者はすべての API キーを表示および再生成できます。 用心のために、ロールの割り当てを調べて、管理キーへのアクセス権を持つユーザーを確認してください。

+ サービス ダッシュボードで、**[アクセス制御 (IAM)]** をクリックし、次に **[ロールの割り当て]** タブをクリックして、サービスのロールの割り当てを表示します。

次のロールのメンバーは、キーの表示と再生成が可能です: 所有者、投稿者、[Search Service の共同作成者](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#search-service-contributor)

> [!Note]
> 検索結果に対する ID ベースのアクセスの場合は、セキュリティ フィルターを作成して ID 別に結果に手を入れ、要求者がアクセスしてはいけないドキュメントを削除します。 詳細については、[セキュリティ フィルター](search-security-trimming-for-azure-search.md)と[Active Directory を使用してセキュリティ保護する](search-security-trimming-for-azure-search-with-aad.md)を参照してください。

## <a name="see-also"></a>関連項目

+ [Azure Search でのロール ベースのアクセス制御](search-security-rbac.md)
+ [PowerShell を使用した管理](search-manage-powershell.md) 
+ [パフォーマンスと最適化についての記事](search-performance-optimization.md)