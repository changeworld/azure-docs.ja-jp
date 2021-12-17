---
title: Purview アカウントのアーキテクチャとベスト プラクティス
description: この記事では、Azure Purview アカウントのアーキテクチャの例を提供し、ベスト プラクティスについて説明します。
author: zeinam
ms.author: zeinam
ms.service: purview
ms.subservice: purview-data-map
ms.topic: conceptual
ms.date: 10/12/2021
ms.openlocfilehash: 5e6faa2eb9556207aae7583044755bc9d01b0502
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132721843"
---
# <a name="azure-purview-accounts-architectures-and-best-practices"></a>Azure Purview アカウントのアーキテクチャとベスト プラクティス  

Azure Purview は、統合データ ガバナンス ソリューションです。 クラウド環境とオンプレミス環境の両方にまたがるデータ資産全体のデータ ガバナンスを一元管理するために、Azure Purview アカウントをデプロイします。 Azure Purview を一元化されたデータ ガバナンス ソリューションとして使用するには、Azure サブスクリプション内に 1 つ以上の Purview アカウントをデプロイする必要があります。 Purview インスタンスの数は、最小限に維持することをお勧めします。ただし、ビジネスのセキュリティやコンプライアンス要件を満足するために、より多くの Purview インスタンスが必要になる場合もあります。

## <a name="intended-audience"></a>対象ユーザー

- データ アーキテクチャ チーム
- データ ガバナンスおよび管理チーム
- データ セキュリティ チーム

## <a name="single-purview-account"></a>1 つの Purview アカウント

組織全体に最小数の Purview アカウントをデプロイすることを検討してください。 このアプローチにより、プラットフォームの価値が、そのプラットフォーム内に存在するデータの関数として指数関数的に増加する "ネットワーク効果" が最大限に活用されます。 

1 つの Purview アカウント内に組織のデータ管理構造をレイアウトするには、[Azure Purview コレクションの階層](./concept-best-practices-collections.md)を使用します。 このシナリオでは、Azure サブスクリプション内に 1 つの Purview アカウントがデプロイされます。 Azure Purview 内では、1 つ以上の Azure サブスクリプションのデータ ソースを登録してスキャンできます。 また、オンプレミス環境またはマルチクラウド環境のデータ ソースを登録してスキャンすることもできます。

:::image type="content" source="media/concept-best-practices/accounts-single-account.png" alt-text="1 つの Azure Purview アカウントを示すスクリーンショット。"lightbox="media/concept-best-practices/accounts-single-account.png":::

## <a name="multiple-purview-accounts"></a>複数の Purview アカウント

組織によっては、複数の Azure Purview アカウントの設定が必要になる場合があります。 Azure Purview アカウントのアーキテクチャを定義するときのいくつかの例として、次のシナリオを確認してください。  

### <a name="testing-new-features"></a>新しい機能のテスト 

分離された環境でスキャンの構成または分類をテストするときは、Purview アカウントの新しいインスタンスを作成することをお勧めします。 一部のシナリオでは、用語集などのプラットフォームのいくつかの領域に "バージョン管理" 機能がありますが、予測される機能を自由にテストするための Purview の "破棄可能な" インスタンスを使用した後、その機能を運用インスタンスにロールアウトするように計画する方が簡単です。  

さらに、ロールバックを実行できない場合は、テスト用の Purview アカウントを使用することを検討してください。 たとえば、用語集の用語属性は現在、Purview アカウントに追加された後は Purview インスタンスから削除できません。 この場合は、まず、テスト用の Purview アカウントを使用することをお勧めします。
 
### <a name="isolating-productionand-non-production-environments"></a>運用環境と非運用環境の分離 

環境ごとにデータの個別のインスタンスがある場合は特に、開発、テスト、運用の各環境に Purview アカウントの個別のインスタンスをデプロイすることを検討してください。  

このシナリオでは、運用および非運用データ ソースを対応する各 Purview インスタンス内で登録してスキャンできます。

必要な場合は、1 つのデータ ソースを複数の Purview インスタンスで登録することもできます。

:::image type="content" source="media/concept-best-practices/accounts-multiple-accounts.png" alt-text="環境に基づいた複数の Azure Purview アカウントを示すスクリーンショット。"lightbox="media/concept-best-practices/accounts-multiple-accounts.png":::

### <a name="fulfilling-compliance-requirements"></a>コンプライアンス要件の実現  

Azure Purview でデータ ソースをスキャンすると、そのメタデータに関連した情報が取り込まれ、Purview アカウントがデプロイされている Azure リージョンの Azure Purview Data Map 内に格納されます。 メタデータを特定の地理的な場所に格納することさえも含む特定の規制およびコンプライアンス要件がある場合は、Azure Purview の個別のインスタンスをデプロイすることを検討してください。  

組織が複数の地域にデータを保有しており、メタデータを実際のデータと同じリージョンに保持する必要がある場合は、複数の Purview インスタンス (地域ごとに 1 つ) をデプロイする必要があります。 この場合は、各リージョンのデータ ソースを、そのデータ ソースのリージョンまたは地域に対応する Purview アカウントで登録してスキャンする必要があります。

:::image type="content" source="media/concept-best-practices/accounts-multiple-regions.png" alt-text="コンプライアンス要件に基づいた複数の Azure Purview アカウントを示すスクリーンショット。"lightbox="media/concept-best-practices/accounts-multiple-regions.png":::

### <a name="having-data-sources-distributed-across-multiple-tenants"></a>複数のテナントにまたがるデータ ソースの分散  

現在、Purview ではマルチテナントをサポートしていません。 Azure データ ソースが、異なる Azure Active Directory テナントにある複数の Azure サブスクリプションにまたがって分散されている場合は、各テナントで個別の Azure Purview アカウントをデプロイすることをお勧めします。 

VM ベースのデータ ソースと Power BI テナントには例外が適用されます。テナント間の Power BI を 1 つの Purview アカウントでスキャンして登録する方法の詳細については、[テナント間の Power BI の登録とスキャン](./register-scan-power-bi-tenant.md)に関するページを参照してください。 

:::image type="content" source="media/concept-best-practices/accounts-multiple-tenants.png" alt-text="マルチテナントの要件に基づいた複数の Azure Purview アカウントを示すスクリーンショット。"lightbox="media/concept-best-practices/accounts-multiple-tenants.png"::: 

### <a name="billing-model"></a>課金モデル 

予算編成モデルを定義し、組織の Azure Purview アーキテクチャを設計する場合は、[Azure Purview の価格モデル](https://azure.microsoft.com/pricing/details/azure-purview)を確認してください。 Purview アカウントがデプロイされているサブスクリプション内の 1 つの Purview アカウントに対して 1 つの課金が生成されます。 このモデルはまた、Purview Data Map 内のメタデータのスキャンや分類などの他の Purview のコストにも適用されます。

一部の組織には、多くの場合、個別に運営される多数の事業単位 (BU) があり、場合によっては、これらの事業単位では課金さえ互いに共有されません。 このような場合、組織は BU ごとに Purview インスタンスを作成することになります。 このモデルは理想的とは言えませんが、特に、事業単位は Azure の課金を進んで共有しようとしない場合が多いため、必要になることがあります。 

チャージバックおよびショーバック モデルでのクラウド コンピューティングのコスト モデルの詳細については、[クラウド会計の概要](/azure/cloud-adoption-framework/strategy/cloud-accounting)に関するページを参照してください。  

## <a name="additional-considerations-and-recommendations"></a>その他の考慮事項と推奨事項 

- 管理オーバーヘッドの軽減のために、Purview アカウントの数を少ない状態に維持してください。 複数の Purview アカウントの作成を予定している場合は、Purview アカウントにまたがる追加のスキャン、アクセス制御モデル、資格情報、ランタイムの作成と管理が必要になることがあります。 さらに、Purview アカウントごとに分類や用語集の用語の管理が必要になることもあります。

- 予算と財務上の要件を確認してください。 可能な場合は、Azure サービスを使用するときにチャージバックまたはショーバック モデルを使用し、Azure Purview のコストを組織全体にわたって分割して Purview アカウントの数を最小限に維持してください。 

- 組織のビジネス ユーザー、データ管理およびガバナンス チームのために Azure Purview Data Map 内のメタデータ アクセス制御を定義するには、[Azure Purview コレクション](concept-best-practices-collections.md)を使用します。 詳細については、「[Azure Purview でのアクセスの制御](./catalog-permissions.md)」を参照してください。

- 新しい Purview アカウントをデプロイする前に、[Azure Purview の制限](./how-to-manage-quotas.md#azure-purview-limits)を確認してください。 現在、リージョンあたり、テナントあたりの Purview アカウント (すべてのサブスクリプションを結合) の既定の制限は 3 です。 Azure Purview の追加のインスタンスをデプロイする前に、Microsoft サポートに連絡してサブスクリプションまたはテナントでこの制限を増やすことが必要になる可能性があります。  

- 使用している環境で新しい Purview アカウントをデプロイする前に、[Azure Purview の前提条件](./create-catalog-portal.md#prerequisites)を確認してください。
  
## <a name="next-steps"></a>次のステップ
-  [Purview アカウントを作成する](./create-catalog-portal.md)
