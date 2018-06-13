---
title: Azure Search におけるデータと操作のセキュリティ保護 | Microsoft Docs
description: Azure Search のセキュリティは、SOC 2 コンプライアンス、暗号化、認証のほか、Azure Search フィルターのユーザーおよびグループ セキュリティ識別子を通じた ID アクセスに基づいています。
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.topic: conceptual
ms.date: 01/19/2018
ms.author: heidist
ms.openlocfilehash: 7db1b6c6f72f3cea7446b5f96dac7cd6e9b4252d
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/23/2018
ms.locfileid: "31795801"
---
# <a name="security-and-controlled-access-in-azure-search"></a>Azure Search のセキュリティとアクセス制御

Azure Search は [SOC 2 に準拠](https://servicetrust.microsoft.com/ViewPage/MSComplianceGuide?command=Download&downloadType=Document&downloadId=93292f19-f43e-4c4e-8615-c38ab953cf95&docTab=4ce99610-c9c0-11e7-8c2c-f908a777fa4d_SOC%20%2F%20SSAE%2016%20Reports)しており、物理的なセキュリティ、伝送の暗号化、ストレージの暗号化、プラットフォーム全体のソフトウェア保護にまたがった包括的なセキュリティ アーキテクチャを備えています。 運用上、Azure Search は認証された要求のみを受け入れます。 必要に応じて、コンテンツに対してユーザーごとのアクセス制御を追加できます。 この記事では、各層のセキュリティについて触れますが、主に Azure Search でデータと操作がどのようにセキュリティ保護されるのかについて重点的に説明します。

![セキュリティ層のブロック図](media/search-security-overview/azsearch-security-diagram.png)

## <a name="physical-security"></a>物理的なセキュリティ

業界最高レベルの物理的なセキュリティを提供する Microsoft データ センターは、標準と規制の広範なポートフォリオに準拠しています。 詳しくは、[グローバル データ センター](https://www.microsoft.com/cloud-platform/global-datacenters)に関するページに移動するか、データ センターのセキュリティに関する短いビデオをご覧ください。

> [!VIDEO https://www.youtube.com/embed/r1cyTL8JqRg]

## <a name="encrypted-transmission-and-storage"></a>伝送とストレージの暗号化

暗号化は、接続から、伝送、Azure Search に格納されているインデックス付きデータまで、インデックス作成のパイプライン全体に拡張されています。

| セキュリティ レイヤー | [説明] |
|----------------|-------------|
| 転送中の暗号化 | Azure Search は HTTPS ポート 443 をリッスンします。 プラットフォーム全体で、Azure サービスへの接続が暗号化されます。 |
| 保存時の暗号化 | 暗号化はインデックス作成処理に完全に含まれ、完了までの時間やインデックス サイズにはほぼ影響しません。 完全に暗号化されていないインデックス (2018 年 1 月より前に作成されたインデックス) の増分更新を含め、すべてのインデックス作成で自動的に行われます。<br><br>内部的には、暗号化は [Azure Storage Service Encryption](https://docs.microsoft.com/azure/storage/common/storage-service-encryption) に基づいており、[256 ビットの AES 暗号化](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)を使用しています。|
| [SOC 2 への準拠](https://www.aicpa.org/interestareas/frc/assuranceadvisoryservices/aicpasoc2report.html) | Azure Search を提供するすべてのデータ センターで、すべての検索サービスは AICPA SOC 2 に完全に準拠しています。 完全なレポートを確認するには、[Azure - Azure Government SOC 2 タイプ II レポート](https://servicetrust.microsoft.com/ViewPage/MSComplianceGuide?command=Download&downloadType=Document&downloadId=93292f19-f43e-4c4e-8615-c38ab953cf95&docTab=4ce99610-c9c0-11e7-8c2c-f908a777fa4d_SOC%20%2F%20SSAE%2016%20Reports)をご覧ください。 |

暗号化は Azure Search の内部で行われ、Microsoft が内部的に管理する証明書と暗号化キーが使用され、汎用的に適用されます。 暗号化のオン/オフの切り替え、独自のキーの管理または代替、ポータル内またはプログラムによる暗号化設定の表示を行うことはできません。 

保存時の暗号化は、2018 年 1 月 24 日に発表され、すべてのリージョンで、共有 (無料) サービスを含むすべてのサービス層に適用されています。 完全な暗号化を行うには、この日付より前に作成されたインデックスを削除し、再構築する必要があります。 そうしないと、1 月 24 日の後に追加された新しいデータのみが暗号化されます。

## <a name="azure-wide-logical-security"></a>Azure 全体の論理的なセキュリティ

いくつかのセキュリティ メカニズムが Azure Stack 全体で利用できます。これらは当然、作成された Azure Search リソースでも利用できます。

+ [削除を防止するサブスクリプション レベルまたはリソース レベルのロック](../azure-resource-manager/resource-group-lock-resources.md)
+ [情報および管理操作に対するアクセスをコントロールするロールベースのアクセス制御 (RBAC)](../role-based-access-control/overview.md)

ロールベースのアクセス制御 (RBAC) はすべての Azure サービスでサポートされており、すべてのサービスで一貫してアクセスのレベルを設定できます。 たとえば、機微なデータ (管理者キーなど) の表示は所有者ロールと共同作成者ロールに制限されるのに対し、サービスの状態はすべてのロールのメンバーが表示できます。 RBAC には所有者、共同作成者、閲覧者のロールがあります。 既定では、すべてのサービス管理者が、所有者ロールのメンバーです。

## <a name="service-access-and-authentication"></a>サービス アクセスと認証

Azure Search は Azure プラットフォームのセキュリティ保護機能を継承しますが、独自のキーベースの認証も提供しています。 API キーは、ランダムに生成された数字と文字から成る文字列です。 キーの種類 (管理者またはクエリ) によって、アクセスのレベルが決まります。 有効なキーの送信は、要求が信頼されたエンティティのものであることの証明と見なされます。 検索サービスへのアクセスには 2 種類のキーが使用されます。

* 管理 (サービスに対するすべての読み取り/書き込み操作に有効)
* クエリ (インデックスに対するクエリなどの読み取り専用操作に有効)

管理キーは、サービスのプロビジョニング時に作成されます。 2 つの管理キーがあり、区別するために "*プライマリ*" と "*セカンダリ*" と呼ばれますが、これらは実際には交換可能です。 各サービスが 2 つの管理キーを持つため、サービスにアクセスしたままで一方のキーをロールオーバーできます。 どちらの管理キーも再生成できますが、管理キーの合計数は追加できません。 Search サービスごとに最大 2 個の管理キーがあります。

クエリ キーは必要に応じて作成され、Search を直接呼び出すクライアント アプリケーション向けに設計されています。 クエリ キーは最大 50 個まで作成できます。 アプリケーション コードでは、サービスへの読み取り専用アクセスを許可するために検索の URL とクエリ API キーを指定します。 また、アプリケーション コードでは、アプリケーションで使用されるインデックスも指定します。 エンドポイント、読み取り専用アクセスのための API キー、およびターゲット インデックスの組み合わせにより、クライアント アプリケーションからの接続のスコープとアクセス レベルが定義されます。

要求ごとに認証が必要です。この各要求は必須のキー、操作、およびオブジェクトで構成されています。 2 つのアクセス許可レベル (完全または読み取り専用) とコンテキスト (インデックスでのクエリ操作など) を組み合わせれば、サービス操作に対して全範囲のセキュリティを実現できます。 キーの詳細については、[API キーの作成と管理](search-security-api-keys.md)に関するページを参照してください。

## <a name="index-access"></a>インデックへのアクセス

Azure Search では、個別のインデックスはセキュリティ保護可能なオブジェクトでありません。 その代わり、インデックスへのアクセスは操作のコンテキストと共にサービス層 (読み取りまたは書き込みアクセス) で決定されます。

エンドユーザーのアクセスの場合、クエリ キーを使用して接続するようアプリケーションのクエリ要求を構成できます (これにより、すべての要求が読み取り専用になります)。また、アプリで使用される特定のインデックスを含めることができます。 クエリ要求では、インデックスの結合または複数のインデックスへの同時アクセスといった概念がないので、当然すべての要求のターゲットは単一のインデックスです。 そのため、クエリ要求の構造自体 (キーと単一のターゲット インデックス) がセキュリティ境界を定義します。

インデックスに対する管理者と開発者のアクセスは区別されていません。サービスによって管理されているオブジェクトの作成、削除、更新を行うには、どちらも書き込みアクセスが必要です。 サービスに対する管理者キーを持っている人はだれでも、そのサービスのインデックスの読み取り、変更、削除を行えます。 インデックスが誤って削除されたり、悪意によって削除されたりすることを防止するうえで、コード資産の社内ソース管理は、望ましくないインデックスの削除または変更を修復する対応策になります。 Azure Search は可用性を確保するためにクラスター内のフェールオーバーを備えていますが、インデックスの作成または読み込みに使用される専用コードを格納したり実行したりしません。

インデックス レベルでセキュリティ境界が必要なマルチテナントのソリューションでは、通常、そのソリューションに顧客がインデックス分離を処理するための中間層が含まれています。 マルチテナントのユース ケースの詳細については、「[マルチテナント SaaS アプリケーションと Azure Search の設計パターン](search-modeling-multitenant-saas-applications.md)」を参照してください。

## <a name="admin-access-from-client-apps"></a>クライアント アプリからの管理者アクセス

Azure Search 管理 REST API は、Azure リソース マネージャーの拡張機能であり、その依存関係を共有します。 そのため、Azure Search のサービス管理にとって、Active Directory は必須となります。 クライアント コードからのすべての管理要求は、Resource Manager に到達する前に、Azure Active Directory を使用して認証される必要があります。

インデックスの作成 (Azure Search サービス REST API) やドキュメントの検索 (Azure Search サービス REST API) など、Azure Search サービス エンドポイントに対するデータ要求では、要求ヘッダーで API キーが使用されます。

サービス管理操作と検索インデックスまたは検索ドキュメントに対するデータ操作をアプリケーション コードで処理する場合、2 つの認証アプローチ (Azure Search にネイティブなアクセス キーと Resource Manager に必要な Active Directory 認証方法) をコードに実装します。 

Azure Search での要求の構成について詳しくは、「[Azure Search Service REST (Azure Search サービス REST API)](https://docs.microsoft.com/rest/api/searchservice/)」を参照してください。 Resource Manager に関する認証要件の詳細については、「[サブスクリプションにアクセスするための Resource Manager 認証 API の使用](../azure-resource-manager/resource-manager-api-authentication.md)」を参照してください。

## <a name="user-access-to-index-content"></a>インデックス コンテンツへのユーザー アクセス

インデックスのコンテンツへのユーザーごとのアクセスは、クエリに対するセキュリティ フィルターを通じて実装され、特定のセキュリティ ID に関連付けられたドキュメントを返します。 事前定義されたロールとロールの割り当ては使用されず、ID に基づいてドキュメントとコンテンツの検索結果をトリミングするフィルターとして ID ベースのアクセス制御が実装されます。 次の表では、承認されていないコンテンツの検索結果をトリミングする 2 つのアプローチについて説明しています。

| アプローチ | [説明] |
|----------|-------------|
|[ID フィルターに基づいたセキュリティによるトリミング](search-security-trimming-for-azure-search.md)  | ユーザー ID アクセス制御を実装する基本的なワークフローについて記載しています。 また、インデックスへのセキュリティ ID の追加について取り上げているほか、そのフィールドに対してフィルター処理を行い、禁止されているコンテンツの結果をトリミングする方法について説明しています。 |
|[Azure Active Directory ID に基づいたセキュリティによるトリミング](search-security-trimming-for-azure-search-with-aad.md)  | この記事は前の記事を拡張したものであり、Azure クラウド プラットフォームの[無料サービス](https://azure.microsoft.com/free/)の 1 つである Azure Active Directory (AAD) から ID を取得する手順について説明しています。 |

## <a name="table-permissioned-operations"></a>表: 許可される操作

次の表では、Azure Search で許可される操作のほか、特定の操作へのアクセスを有効にするキーについてまとめています。

| 操作 | アクセス許可 |
|-----------|-------------------------|
| サービスの作成 | Azure サブスクリプション所有者|
| サービスのスケーリング | 管理者キー、リソースに対する RBAC 所有者または RBAC 共同作成者  |
| サービスの削除 | 管理者キー、リソースに対する RBAC 所有者または RBAC 共同作成者 |
| サービス上のオブジェクトの作成、変更、削除: <br>インデックスとコンポーネント部分 (アナライザーの定義、スコアリング プロファイル、CORS オプションなど)、インデクサー、データ ソース、シノニム、サジェスター。 | 管理者キー、リソースに対する RBAC 所有者または RBAC 共同作成者  |
| インデックスのクエリ | 管理者キーまたはクエリ キー (RBAC は適用不可) |
| クエリ システム情報 (オブジェクトの統計、カウントおよび一覧を返す操作など) | 管理者キー、リソースに対する RBAC (所有者、共同作成者、閲覧者) |
| 管理者キーの管理 | 管理者キー、リソースに対する RBAC 所有者または RBAC 共同作成者。 |
| クエリ キーの管理 |  管理者キー、リソースに対する RBAC 所有者または RBAC 共同作成者。  |


## <a name="see-also"></a>関連項目

+ [.NET の作業開始 (管理者キーを使用したインデックスの作成のデモンストレーション)](search-create-index-dotnet.md)
+ [REST の作業開始 (管理者キーを使用したインデックスの作成のデモンストレーション)](search-create-index-rest-api.md)
+ [Azure Search フィルターを使用した ID ベースのアクセス制御](search-security-trimming-for-azure-search.md)
+ [Azure Search フィルターを使用した Active Directory の ID ベースのアクセス制御](search-security-trimming-for-azure-search-with-aad.md)
+ [Azure Search のフィルター](search-filters.md)