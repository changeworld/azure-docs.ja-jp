---
title: BLOB データの不変ストレージの概要
titleSuffix: Azure Storage
description: Azure Storage では、BLOB ストレージの WORM (Write Once Read Many) がサポートされています。これにより、消去および変更できない状態でデータを保存できます。 時間ベースのアイテム保持ポリシーにより、BLOB データは指定された間隔で、WORM 状態で格納され、訴訟ホールドが明示的にクリアされるまで有効なままになります。
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 08/31/2021
ms.author: tamram
ms.subservice: blobs
ms.custom: references_regions
ms.openlocfilehash: 7796ab6b07b83be3311355d629b7822fa082f9f6
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132724123"
---
# <a name="store-business-critical-blob-data-with-immutable-storage"></a>不変ストレージを使用してビジネスに不可欠な BLOB データを保存する

Azure Blob Storage の不変ストレージを使用すると、ユーザーはビジネスに不可欠なデータを WORM (Write Once, Read Many) 状態で保存できます。 WORM の状態では、ユーザーが指定した期間、データを変更および削除できません。 BLOB データに不変ポリシーを構成することにより、上書きや削除からデータを保護することができます。

Azure Blob Storage の不変ストレージでは、次の 2 種類の不変ポリシーがサポートされています。

- **時間ベースのアイテム保持ポリシー**: 時間ベースのアイテム保持ポリシーを使用すると、ユーザーは指定した間隔でデータを格納するポリシーを設定できます。 時間ベースのアイテム保持ポリシーを設定すると、オブジェクトの作成と読み取りは可能ですが、変更または削除はできません。 保持期間の期限が切れた後はオブジェクトを削除できますが、上書きはできません。 時間ベースのアイテム保持ポリシーの詳細については、「[不変 BLOB データに対する時間ベースのアイテム保持ポリシー](immutable-time-based-retention-policy-overview.md)」を参照してください。

- **訴訟ホールドポリシー**: 訴訟ホールドでは、訴訟ホールドが明示的にクリアされるまで、不変データを格納します。 訴訟ホールドを設定すると、オブジェクトの作成と読み取りは可能ですが、変更または削除はできません。 訴訟ホールド ポリシーの詳細については、「[不変 BLOB データの訴訟ホールド](immutable-legal-hold-overview.md)」を参照してください。

次の図は、時間ベースの保持ポリシーと訴訟ホールドが有効になっている間、どのように書き込みと削除操作が防止されるかを示しています。

:::image type="content" source="media/immutable-storage-overview/worm-diagram.png" alt-text="保持ポリシーと訴訟ホールドが書き込みと削除操作を防止する方法を示す図":::

## <a name="about-immutable-storage-for-blobs"></a>BLOB の不変ストレージについて

不変ストレージを使用すると、医療機関、金融機関、および関連する業界 (&mdash;特に、ブローカー ディーラー組織&mdash;) は、データを安全に保存できるようになります。 不変ストレージは、重要なデータを変更や削除から保護するためのどのシナリオでも利用できます。

一般的な用途は次のとおりです。

- **法令遵守**:Azure Blob Storage の不変ストレージは、組織が SEC 17a-4(f)、CFTC 1.31(d)、FINRA などの規制に準拠するのに役立ちます。

- **セキュリティ保護されたドキュメント リテンション**: BLOB の不変ストレージにより、どのユーザーも、データを変更したり、削除したりできなくなります。アカウント管理特権を持つユーザーですらです。

- **訴訟ホールド**: BLOB の不変ストレージを使用すると、ユーザーは訴訟やビジネス用途に不可欠な機密情報を、ホールドが削除されるまでの必要な期間中、改ざん防止状態で保存できます。 この機能は、法的なユース ケースのみに限定されず、イベント トリガーや会社のポリシーに基づいたデータの保護が必要な、イベント ベースのホールドまたはエンタープライズ ロックとして考えることもできます。

## <a name="regulatory-compliance"></a>規制に対するコンプライアンス

Microsoft は、記録管理と情報ガバナンスに特化した大手の独立系評価会社である Cohasset Associates を保有して、金融サービス業界に固有の要件に対して、BLOB の不変ストレージとそのコンプライアンスを評価しました。 Cohasset では、不変ストレージが、BLOB を WORM 状態に維持するために使用する場合、CFTC Rule 1.31(c)-(d)、FINRA Rule 4511、および SEC Rule 17a-4(f) の関連ストレージ要件を満たしていることを実証しました。 この一連のルールは金融機関のレコード保有期間に対する最も規範的なガイダンスを世界中に示すものであるため、Microsoft ではこれらのルールを対象としました。

Cohasset レポートは [Microsoft Service Trust Center](https://aka.ms/AzureWormStorage) で入手できます。 [Azure トラスト センター](https://www.microsoft.com/trustcenter/compliance/compliance-overview)では、コンプライアンス関連の Microsoft の認定資格に関する詳しい情報を確認できます。 WORM 不変性コンプライアンスに関する Microsoft からの構成証明の文書を要求するには、[Azure サポート](https://azure.microsoft.com/support/options/)にお問い合わせください。

## <a name="immutability-policy-scope"></a>不変ポリシーのスコープ

不変ポリシーは、BLOB バージョン (プレビュー) またはコンテナーにスコープを設定できます。 オブジェクトが不変ポリシーの下でどのように動作するかは、ポリシーのスコープによって異なります。 不変ポリシーの種類ごとのポリシー スコープの詳細については、次のセクションを参照してください。

- [時間ベースのアイテム保持ポリシーのスコープ](immutable-time-based-retention-policy-overview.md#time-based-retention-policy-scope)
- [訴訟ホールドのスコープ](immutable-legal-hold-overview.md#legal-hold-scope)

スコープに応じて、時間ベースのアイテム保持ポリシーとリソース (コンテナーまたは BLOB バージョン) の訴訟ホールドの両方を構成できます。 次の表は、各リソース スコープでサポートされている不変ポリシーをまとめたものです。

| Scope | コンテナーでは、バージョン レベルの不変性ポリシーがサポートされます | コンテナーでは、バージョン レベルの不変性ポリシーはサポートされません |
|--|--|--|
| コンテナー | 1 つの既定のバージョン レベルの不変ポリシーをサポートします。 既定のポリシーは、コンテナーの構成後に作成された新しいバージョンに適用されます。<br /><br /> 訴訟ホールドはサポートされていません。 | 1 つのコンテナー レベルの不変ポリシーと 1 つの訴訟ホールドをサポートします。 BLOB バージョンのポリシーは、コンテナーで指定される既定のポリシーをオーバーライドできます。 |
| BLOB バージョン | 1 つのバージョン レベルの不変ポリシーと 1 つの訴訟ホールドをサポートします。 | 該当なし |

### <a name="about-the-preview"></a>プレビューについて

バージョン レベルの不変ポリシーは、次のリージョンで使用できます。

- カナダ中部
- カナダ東部
- フランス中部
- フランス南部

> [!IMPORTANT]
> バージョン レベルの不変ポリシーは、現在、**プレビュー** の段階です。 ベータ版、プレビュー版、または一般提供としてまだリリースされていない Azure の機能に適用される法律条項については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。

## <a name="summary-of-immutability-scenarios"></a>不変性のシナリオの概要

不変ポリシーによって提供される保護は、不変ポリシーのスコープ、そして時間ベースのアイテム保持ポリシーの場合は、ロックまたはロック解除状態か、アクティブか期限切れかによって異なります。

### <a name="scenarios-with-version-level-scope"></a>バージョン レベルのスコープを使用するシナリオ

次の表に、バージョン レベルの不変ポリシーによって提供される保護の概要を示します。

| シナリオ | 禁止されている操作 | BLOB 保護 | コンテナー保護 | アカウント保護 |
|--|--|--|--|--|
| BLOB のバージョンが *アクティブ* なアイテム保持ポリシーによって保護されている、または訴訟ホールドが有効である | [Delete Blob](/rest/api/storageservices/delete-blob)、[Set Blob Metadata](/rest/api/storageservices/set-blob-metadata)、[Put Page](/rest/api/storageservices/put-page)、[Append Block](/rest/api/storageservices/append-block)<sup>1</sup> | BLOB のバージョンを削除することはできません。 ユーザー メタデータを書き込めません。 <br /><br /> [Put Blob](/rest/api/storageservices/put-blob)、[Put Block List](/rest/api/storageservices/put-block-list)、[Copy Blob](/rest/api/storageservices/copy-blob) で、BLOB を上書きすると、新しいバージョンが作成されます。<sup>2</sup> | ポリシーがロックまたはロック解除されているかどうかに関係なく、コンテナーに少なくとも 1 つの BLOB が存在する場合、コンテナーの削除は失敗します。 | バージョン レベルの不変ストレージが有効になっているコンテナーが少なくとも 1 つある場合、ストレージ アカウントの削除は失敗します。 |
| BLOB のバージョンが *期限切れ* のアイテム保持ポリシーによって保護されている、または訴訟ホールドが有効である | [Set Blob Metadata](/rest/api/storageservices/set-blob-metadata)、[Put Page](/rest/api/storageservices/put-page)、[Append Block](/rest/api/storageservices/append-block)<sup>1</sup> | BLOB のバージョンを削除することができます。 ユーザー メタデータを書き込めません。 <br /><br /> [Put Blob](/rest/api/storageservices/put-blob)、[Put Block List](/rest/api/storageservices/put-block-list)、[Copy Blob](/rest/api/storageservices/copy-blob) で、BLOB を上書きすると、新しいバージョンが作成されます<sup>2</sup>。 | ポリシーがロックまたはロック解除されているかどうかに関係なく、コンテナーに少なくとも 1 つの BLOB が存在する場合、コンテナーの削除は失敗します。 | 時間ベースのアイテム保持ポリシーを持つ BLOB バージョンを含むコンテナーが少なくとも 1 つある場合、ストレージ アカウントの削除は失敗します。<br /><br />ロック解除されたポリシーでは、削除保護は提供されません。 |

<sup>1</sup> [Append Block](/rest/api/storageservices/append-block) 操作は、**allowProtectedAppendWrites** プロパティが有効になっている時間ベースのアイテム保持ポリシーに対してのみ許可されます。 詳細については、「[保護された追加 BLOB の書き込みを許可する](immutable-time-based-retention-policy-overview.md#allow-protected-append-blobs-writes)」を参照してください。
<sup>2</sup> BLOB バージョンは常にコンテンツを変更できません。 ストレージ アカウントでバージョン管理が有効になっている場合、ブロック BLOB に対する書き込み操作で新しいバージョンが作成されます。ただし、[Put Block](/rest/api/storageservices/put-block) 操作は除きます。

### <a name="scenarios-with-container-level-scope"></a>コンテナー レベルのスコープを使用するシナリオ

次の表に、コンテナー レベルの不変ポリシーによって提供される保護の概要を示します。

| シナリオ | 禁止されている操作 | BLOB 保護 | コンテナー保護 | アカウント保護 |
|--|--|--|--|--|
| コンテナーは、コンテナー スコープを持つ *アクティブ* な時間ベースのアイテム保持ポリシーよって保護されている、または訴訟ホールドが有効、またはその両方です。 | [Delete Blob](/rest/api/storageservices/delete-blob)、[Put Blob](/rest/api/storageservices/put-blob)<sup>1</sup>、[Set Blob Metadata](/rest/api/storageservices/set-blob-metadata)、[Put Page](/rest/api/storageservices/put-page)、[Set Blob Properties](/rest/api/storageservices/set-blob-properties)、[Snapshot Blob](/rest/api/storageservices/snapshot-blob)、[Incremental Copy Blob](/rest/api/storageservices/incremental-copy-blob)、[Append Block](/rest/api/storageservices/append-block)<sup>2</sup> | コンテナー内のすべての BLOB は、コンテンツとユーザー メタデータを変更できません | コンテナー レベルのポリシーが有効な場合、コンテナーの削除は失敗します。 | 少なくとも 1 つの BLOB が存在するコンテナーがある場合、ストレージ アカウントの削除は失敗します。 |
| コンテナーは、コンテナー スコープを持つ *期限切れ* の時間ベースのアイテム保持ポリシーよって保護されている、かつ訴訟ホールドが有効ではありません | [Put Blob](/rest/api/storageservices/put-blob)<sup>1</sup>、[Set Blob Metadata](/rest/api/storageservices/set-blob-metadata)、[Put Page](/rest/api/storageservices/put-page)、[Set Blob Properties](/rest/api/storageservices/set-blob-properties)、[Snapshot Blob](/rest/api/storageservices/snapshot-blob)、[Incremental Copy Blob](/rest/api/storageservices/incremental-copy-blob)、[Append Block](/rest/api/storageservices/append-block)<sup>2</sup> | 削除操作は許可されます。 上書き操作は許可されません。 | ポリシーがロックまたはロック解除されているかどうかに関係なく、コンテナーに少なくとも 1 つの BLOB が存在する場合、コンテナーの削除は失敗します。 | 時間ベースのアイテム保持ポリシーを持つコンテナーが少なくとも 1 つある場合、ストレージ アカウントの削除は失敗します。<br /><br />ロック解除されたポリシーでは、削除保護は提供されません。 |

<sup>1</sup> Azure Storage により、[Put Blob](/rest/api/storageservices/put-blob) 操作で新しい BLOB を作成することが許可されます。 不変コンテナーの既存の BLOB パスでの以降の上書き操作は、許可されません。

<sup>2</sup> [Append Block](/rest/api/storageservices/append-block) 操作は、**allowProtectedAppendWrites** プロパティが有効になっている時間ベースのアイテム保持ポリシーに対してのみ許可されます。 詳細については、「[保護された追加 BLOB の書き込みを許可する](immutable-time-based-retention-policy-overview.md#allow-protected-append-blobs-writes)」を参照してください。

> [!NOTE]
> 一部のワークロード ([SQL Backup to URL](/sql/relational-databases/backup-restore/sql-server-backup-to-url) など) では、BLOB が作成され、それに追加されます。 コンテナーにアクティブな時間ベースのアイテム保持ポリシーまたは訴訟ホールドがある場合は、このパターンに失敗します。

## <a name="supported-account-configurations"></a>サポートされているアカウント構成

不変ポリシーは、新規および既存のストレージ アカウントに対してサポートされています。 次の表は、各種類のポリシーでサポートされているストレージ アカウントの種類を示しています。

| 不変ポリシーの種類 | ポリシーのスコープ | サポートされるストレージ アカウントの種類 | 階層型名前空間のサポート (プレビュー) |
|--|--|--|--|
| 時間ベースのアイテム保持ポリシー | バージョン レベルのスコープ (プレビュー) | 汎用 v2<br />Premium ブロック BLOB | いいえ |
| 時間ベースのアイテム保持ポリシー | コンテナー レベルのスコープ | 汎用 v2<br />Premium ブロック BLOB<br />汎用 v1 (レガシ)<sup>1</sup><br> BLOB ストレージ (レガシ) | はい |
| 訴訟ホールド | バージョン レベルのスコープ (プレビュー) | 汎用 v2<br />Premium ブロック BLOB | いいえ |
| 訴訟ホールド | コンテナー レベルのスコープ | 汎用 v2<br />Premium ブロック BLOB<br />汎用 v1 (レガシ)<sup>1</sup><br> BLOB ストレージ (レガシ) | はい |

<sup>1</sup> Microsoft は、汎用 v1 アカウントを汎用 v2 にアップグレードすることをお勧めします。これにより、より多くの機能を利用することができます。 既存の汎用 v1 ストレージ アカウントのアップグレードについては、[ストレージ アカウントのアップグレード](../common/storage-account-upgrade.md)に関する記事を参照してください。

### <a name="access-tiers"></a>アクセス層

すべての BLOB アクセス層は、不変ストレージをサポートしています。 BLOB のアクセス層を変更するには、Set Blob Tier 操作を使用します。 詳細については、[BLOB データのホット、クール、アーカイブ アクセス層](access-tiers-overview.md)に関するページを参照してください。

### <a name="redundancy-configurations"></a>冗長構成

すべての冗長構成は、不変ストレージをサポートします。 geo 冗長構成では、お客様が管理するフェールオーバーはサポートされていません。 冗長構成の詳細については、「[Azure Storage の冗長性](../common/storage-redundancy.md)」を参照してください。

### <a name="hierarchical-namespace-support"></a>階層型名前空間のサポート

階層型名前空間を持つアカウントに対する不変ストレージのサポートはプレビュー段階です。 プレビューに登録するには、「[Azure Data Lake Storage のプレビュー機能](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR2EUNXd_ZNJCq_eDwZGaF5VUOUc3NTNQSUdOTjgzVUlVT1pDTzU4WlRKRy4u)」を参照してください。

BLOB が不変の状態で、アカウントの階層型名前空間が有効になっている場合、BLOB の名前変更や移動はできないことに注意してください。 名前とディレクトリ構造は必須のコンテナー レベル データを提供し、不変ポリシーの設定後、変更できません。

> [!IMPORTANT]
> 階層型名前空間機能が有効になっているアカウント内の Azure Blob ストレージの不変ストレージは、現在プレビュー段階です。 ベータ版、プレビュー版、または一般提供としてまだリリースされていない Azure の機能に適用される法律条項については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。

## <a name="recommended-blob-types"></a>推奨される BLOB の種類

主にブロック BLOB と追加 BLOB に対して不変ポリシーを構成することをお勧めします。 ディスクへの書き込みがブロックされるため、アクティブな仮想マシンの VHD ディスクを格納するページ BLOB の不変ポリシーを構成することは推奨されません。 時間ベースのポリシーをロックする前に、ドキュメントを十分に確認し、シナリオをテストすることをお勧めします。

## <a name="immutable-storage-with-blob-soft-delete"></a>BLOB の論理的な削除と不変ストレージ

ストレージ アカウントに対して BLOB の論理的な削除が構成されている場合、訴訟ホールドまたは時間ベースのアイテム保持ポリシーが有効になっているかどうかに関係なく、アカウント内のすべての BLOB に適用されます。 不変ポリシーが適用される前に、保護を強化するために論理的な削除を有効にすることをお勧めします。

BLOB の論理的な削除を有効にした後で不変ポリシーを構成した場合、論理的に削除されたすべての BLOB は、論理的な削除のアイテム保持ポリシーの有効期限が切れた時点で完全に削除されます。 論理的に削除された BLOB は、論理的な削除の保持期間中であれば復元できます。 論理的な削除がまだ行われていない BLOB またはバージョンは、不変ポリシーによって保護されており、時間ベースのアイテム保持ポリシーが期限切れになるか、または訴訟ホールドが削除されるまで、論理的な削除はできません。

## <a name="use-blob-inventory-to-track-immutability-policies"></a>BLOB インベントリを使用して不変ポリシーを追跡する

Azure Storage BLOB インベントリは、ストレージ アカウント内のコンテナーの概要と、その中にある BLOB、スナップショット、および BLOB のバージョンを提供します。 BLOB インベントリ レポートを使用すると、リソースに不変ポリシーが構成されているかどうかなど、BLOB とコンテナーの属性を把握できます。

BLOB インベントリを有効にすると、Azure Storage によって 1 日単位でインベントリ レポートが生成されます。 このレポートには、ビジネス要件とコンプライアンス要件に関するデータの概要が示されます。

BLOB インベントリの詳細については、「[Azure Storage BLOB インベントリ (プレビュー)](blob-inventory.md)」を参照してください。

## <a name="pricing"></a>価格

不変ストレージを使用する場合、追加の容量料金は発生しません。 不変データは、変更可能データと同じ方法で価格が設定されます。 Azure Blob Storage の価格設定の詳細については、[Azure Storage の価格に関するページ](https://azure.microsoft.com/pricing/details/storage/blobs/)を参照してください。

BLOB バージョンに対する時間ベースのアイテム保持ポリシーまたは訴訟ホールドを作成、変更、または削除すると、書き込みトランザクション料金が発生します。

請求書の支払いに失敗し、アカウントに有効な時間ベースのアイテム保持ポリシーが有効になっている場合、通常のデータ保持ポリシーは、Microsoft との契約の条項および条件に従って適用されます。 全般的な情報については、「[Microsoft でのデータ管理](https://www.microsoft.com/trust-center/privacy/data-management)」を参照してください。

## <a name="feature-support"></a>機能サポート

次の表は、アカウントでのこの機能のサポートと、特定の機能を有効にした場合のサポートへの影響を示しています。

| ストレージ アカウントの種類 | Blob Storage (既定のサポート) | Data Lake Storage Gen2 <sup>1</sup> | NFS 3.0 <sup>1</sup> | SFTP <sup>1</sup> |
|--|--|--|--|--|
| Standard 汎用 v2 | ![はい](../media/icons/yes-icon.png) |![はい](../media/icons/yes-icon.png)  <sup>2</sup>              | ![はい](../media/icons/yes-icon.png)  <sup>2</sup> | ![はい](../media/icons/yes-icon.png)  <sup>2</sup>
| Premium ブロック BLOB          | ![はい](../media/icons/yes-icon.png) |![はい](../media/icons/yes-icon.png)  <sup>2</sup> | ![はい](../media/icons/yes-icon.png)  <sup>2</sup> | ![はい](../media/icons/yes-icon.png)  <sup>2</sup>

<sup>1</sup> Data Lake Storage Gen2、ネットワーク ファイル システム (NFS) 3.0 プロトコル、セキュア ファイル転送プロトコル (SFTP) のサポートでは、すべて階層型名前空間が有効になっているストレージ アカウントが必要です。

<sup>2</sup>    機能はプレビュー レベルでサポートされています。

## <a name="next-steps"></a>次のステップ

- [データ保護の概要](data-protection-overview.md)
- [不変 BLOB データに対する時間ベースの保持ポリシー](immutable-time-based-retention-policy-overview.md)
- [不変 BLOB データに対する法的な保持](immutable-legal-hold-overview.md)
- [BLOB バージョンに対する不変性ポリシーを構成する (プレビュー)](immutable-policy-configure-version-scope.md)
- [コンテナーの不変性ポリシーを構成する](immutable-policy-configure-container-scope.md)
