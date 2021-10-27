---
title: ストレージ アカウントの概要
titleSuffix: Azure Storage
description: Azure Storage のさまざまな種類のストレージ アカウントについて説明します。 アカウントの名前付け、パフォーマンス レベル、アクセス層、冗長性、暗号化、エンドポイントなどについて確認します。
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 05/14/2021
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: a05935f547815ffba419e2e4302c5197d1907bbf
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/15/2021
ms.locfileid: "130045277"
---
# <a name="storage-account-overview"></a>ストレージ アカウントの概要

Azure ストレージ アカウントには、すべての Azure Storage データ オブジェクト (BLOB、ファイル共有、キュー、テーブル、およびディスク) が含まれます。 このストレージ アカウントでは、世界中のどこからでも HTTP または HTTPS 経由でアクセスできる Azure Storage データ用の一意の名前空間が提供されます。 ストレージ アカウント内のデータは、持続性があり、高可用性で、セキュリティ保護されており、非常にスケーラブルです。

Azure ストレージ アカウントの作成方法については、「[ストレージ アカウントの作成](storage-account-create.md)」を参照してください。

## <a name="types-of-storage-accounts"></a>ストレージ アカウントの種類

Azure Storage では、数種類のストレージ アカウントが提供されています。 各種類は異なる機能をサポートし、独自の価格モデルがあります。 アプリケーションに最適な種類を判断するために、ストレージ アカウントを作成する前にこれらの違いを検討してください。

次の表では、Microsoft が推奨するストレージアカウントの種類を、ほとんどのシナリオについて説明しています。 いずれも [Azure Resource Manager](../../azure-resource-manager/management/overview.md) デプロイ モデルが使用されています。

| ストレージ アカウントの種類 | サポートされているストレージ サービス | 冗長オプション | 使用 |
|--|--|--|--|
| Standard 汎用 v2 | Blob Storage (Data Lake Storage<sup>1</sup> を含む)、Queue Storage、Table Storage、Azure Files  | LRS、GRS、RA-GRS<br /><br />ZRS、GZRS、RA-GZRS<sup>2</sup> | BLOB、ファイル共有、キュー、テーブル用の Standard タイプのストレージ アカウント。 Azure Storage を使用するほとんどのシナリオにお勧めします。 Azure Files の NFS ファイル共有のサポートが必要な場合は、Premium ファイル共有タイプのアカウントを使用してください。 |
| Premium ブロック BLOB<sup>3</sup> | Blob Storage (Data Lake Storage<sup>1</sup> を含む) | LRS<br /><br />ZRS<sup>2</sup> | ブロック BLOB と追加 BLOB 用の Premium タイプのストレージ アカウント。 トランザクション レートが高く、比較的小さなオブジェクトが使用されるシナリオ、またはストレージ待ち時間が一貫して短いことが要求されるシナリオに推奨されます。 [ワークロードの例に関する詳細情報](../blobs/storage-blob-block-blob-premium.md)を参照してください。 |
| Premium ファイル共有<sup>3</sup> | Azure Files | LRS<br /><br />ZRS<sup>2</sup> | ファイル共有専用の Premium タイプのストレージ アカウント。 エンタープライズまたはハイ パフォーマンス スケール アプリケーションにお勧めします。 SMB ファイル共有と NFS ファイル共有の両方をサポートするストレージアカウントが必要な場合は、このタイプのアカウントを使用します。 |
| Premium ページ BLOB<sup>3</sup> | ページ BLOB のみ | LRS | ページ BLOB に特化した Premium Storage アカウントの種類。 [ページ BLOB とサンプル ユース ケースの詳細情報](../blobs/storage-blob-pageblob-overview.md)を参照してください。 |

<sup>1</sup> Data Lake Storage は、Azure Blob Storage を基にした、ビッグ データ分析専用の機能のセットです。 詳細については、[Data Lake Storage Gen2 の概要](../blobs/data-lake-storage-introduction.md)および [Data Lake Storage Gen2 で使用するストレージ アカウントの作成](../blobs/create-data-lake-storage-account.md)に関するページを参照してください。

<sup>2</sup> ゾーン冗長ストレージ (ZRS) と geo ゾーン冗長ストレージ (GZRS、RA-GZRS) は、特定のリージョンの Standard 汎用 v2、Premium ブロック BLOB、Premium ファイル共有の各アカウントでのみ使用できます。 詳細については、「[Azure Storage の冗長性](storage-redundancy.md)」を参照してください。

<sup>3</sup> Premium パフォーマンス ストレージ アカウントでは、ソリッド ステート ドライブ (SSD) を使用することで低遅延と高スループットを実現しています。

レガシ ストレージ アカウントもサポートされます。 詳細については、「[レガシ ストレージ アカウントの種類](#legacy-storage-account-types)」を参照してください。

## <a name="storage-account-endpoints"></a>ストレージ アカウント エンドポイント

ストレージ アカウントは、データ用の一意の名前空間を Azure 内に用意します。 Azure Storage 内に格納されるすべてのオブジェクトには、一意のアカウント名を含むアドレスが割り当てられます。 アカウント名と Azure Storage サービス エンドポイントの組み合わせによって、ストレージ アカウント用のエンドポイントが形成されます。

ストレージ アカウントに名前を付けるときは、次の規則に注意してください。

- ストレージ アカウント名の長さは 3 から 24 文字である必要があり、数字と小文字のみを使用できます。
- ストレージ アカウント名は Azure 内で一意である必要があります。 複数のストレージ アカウントが同じ名前を持つことはできません。

次の表は、各 Azure Storage サービスのエンドポイントの形式を一覧にしたものです。

| ストレージ サービス | エンドポイント |
|--|--|
| BLOB ストレージ | `https://<storage-account>.blob.core.windows.net` |
| Data Lake Storage Gen2 | `https://<storage-account>.dfs.core.windows.net` |
| Azure Files | `https://<storage-account>.file.core.windows.net` |
| ストレージ | `https://<storage-account>.queue.core.windows.net` |
| Table ストレージ | `https://<storage-account>.table.core.windows.net` |

ストレージアカウント内のオブジェクトの場所をエンドポイントに追加して、ストレージアカウント内のオブジェクトにアクセスするためのURLを構築します。 たとえば、BLOB の URL は次のようになります。

`http://*mystorageaccount*.blob.core.windows.net/*mycontainer*/*myblob*`

BLOB 用のカスタム ドメインを使用するようにストレージ アカウントを構成することもできます。 詳細については、「[Azure Storage アカウントのカスタム ドメイン名の構成](../blobs/storage-custom-domain-name.md)」をご覧ください。

## <a name="migrate-a-storage-account"></a>ストレージ アカウントを移行する

次の表は、ストレージ アカウントの移動、アップグレード、または移行方法に関するガイダンスをまとめたものです。

| 移行シナリオ | 説明 |
|--|--|
| ストレージ アカウントを別のサブスクリプションに移動する | Azure Resource Manager には、リソースを別のサブスクリプションに移動するためのオプションが用意されています。 詳細については、「[新しいリソース グループまたはサブスクリプションへのリソースの移動](../../azure-resource-manager/management/move-resource-group-and-subscription.md)」を参照してください。 |
| ストレージ アカウントを別のリソース グループに移動する | Azure Resource Manager には、リソースを別のリソース グループに移動するためのオプションが用意されています。 詳細については、「[新しいリソース グループまたはサブスクリプションへのリソースの移動](../../azure-resource-manager/management/move-resource-group-and-subscription.md)」を参照してください。 |
| ストレージ アカウントを別のリージョンに移動する | ストレージ アカウントを移動するには、別のリージョンにストレージ アカウントのコピーを作成します。 その後、AzCopy または選択した他のツールを使用して、そのアカウントにデータを移動します。 詳細については、「[Azure ストレージ アカウントを別のリージョンに移動する](storage-account-move.md)」をご覧ください。 |
| 汎用 v2 ストレージ アカウントにアップグレードする | 汎用 v1 ストレージ アカウントまたは BLOB ストレージ アカウントは、汎用 v2 ストレージ アカウントにアップグレードすることができます。 この操作は元に戻すことができません。 詳細については、「[汎用 v2 ストレージ アカウントにアップグレードする](storage-account-upgrade.md)」を参照してください。 |
| クラシック ストレージ アカウントを Azure Resource Manager に移行する | Azure Resource Manager デプロイ モデルは、機能、スケーラビリティ、およびセキュリティに関して、クラシック デプロイ モデルよりも優れています。 クラシック ストレージ アカウントを Azure Resource Manager に移行する方法の詳細については、「[プラットフォームでサポートされているクラシックから Azure Resource Manager への IaaS リソースの移行](../../virtual-machines/migration-classic-resource-manager-overview.md#migration-of-storage-accounts)」の「ストレージ アカウントの移行」セクションを参照してください。 |

## <a name="transfer-data-into-a-storage-account"></a>ストレージ アカウントへのデータの転送

Microsoft では、オンプレミス ストレージ デバイスまたはサード パーティのクラウド ストレージ プロバイダーからデータをインポートするためのサービスとユーティリティを提供しています。 どのソリューションを使用するかは、転送するデータ量によって決まります。 詳細については、「[Azure Storage の移行の概要](storage-migration-overview.md)」を参照してください。

## <a name="storage-account-encryption"></a>ストレージ アカウントの暗号化

ストレージ アカウント内のすべてのデータは、サービス側で自動的に暗号化されます。 暗号化およびキー管理の詳細については、「[保存データに対する Azure Storage 暗号化](storage-service-encryption.md)」を参照してください。

## <a name="storage-account-billing"></a>ストレージ アカウントの課金

Azure Storage では、ストレージ アカウントの使用に基づいて請求されます。 ストレージ アカウント内のすべてのオブジェクトは、グループとしてまとめて課金されます。 ストレージ コストは次の要素に基づいて計算されます。

- **リージョン** とは、自分のアカウントがベースとしている地理的領域を指します。
- **アカウントの種類** とは、使用するストレージ アカウントの種類を指します。
- **アクセス層** とは、汎用 v2 または BLOB ストレージ アカウント用に指定したデータの使用状況パターンを指します。
- **容量** とは、データの保存に使用しているストレージ アカウントの割り当て量を指します。
- **冗長性** は、1 回に保持するデータのコピー数とデータの場所を決定します。
- **トランザクション** とは、Azure Storage に対するすべての読み取り操作と書き込み操作を指します。
- **データ転送** とは、Azure リージョンから転送されるデータを指します。 他のリージョンで動作しているアプリケーションがストレージ アカウント内のデータにアクセスした場合は、エグレス データ転送として課金されます  リソース グループを使用してデータとサービスを同じリージョンにグループ化してエグレス料金を制限する方法については、「[Azure リソース グループとは](/azure/cloud-adoption-framework/govern/resource-consistency/resource-access-management#what-is-an-azure-resource-group)」を参照してください。

アカウントの種類、ストレージ容量、レプリケーション、およびトランザクションに基づく詳細な価格情報については、[Azure Storage の価格に関するページ](https://azure.microsoft.com/pricing/details/storage/)を参照してください。 データ エグレスの価格の詳細については、[データ転送の価格の詳細](https://azure.microsoft.com/pricing/details/data-transfers/)に関するページを参照してください。 コストの見積もりには、[Azure Storage の料金計算ツール](https://azure.microsoft.com/pricing/calculator/?scenario=data-management)をご利用ください。

[!INCLUDE [cost-management-horizontal](../../../includes/cost-management-horizontal.md)]

## <a name="legacy-storage-account-types"></a>レガシ ストレージ アカウントの種類

次の表では、レガシ ストレージ アカウントの種類について説明します。 これらの種類のアカウントは、Microsoft によって推奨されていませんが、特定のシナリオで使用される場合があります。

| レガシ ストレージ アカウントの種類 | サポートされているストレージ サービス | 冗長オプション | デプロイメント モデル | 使用 |
|--|--|--|--|--|
| Standard 汎用 v1 | Blob Storage、Queue Storage、Table Storage、Azure Files | LRS、GRS、RA-GRS | Resource Manager、クラシック | 汎用 v1 アカウントでは、最新の機能が利用できない場合があるほか、GB 単価もやや高いことがあります。 使用が検討されるシナリオは次のとおりです。<br /><ul><li>アプリケーションで、Azure [クラシック デプロイ モデル](../../azure-portal/supportability/classic-deployment-model-quota-increase-requests.md)が必要である。</li><li>アプリケーションは、トランザクション集中型であるか、かなり geo レプリケーション帯域幅を使用しますが、大容量は必要ありません。 この場合は、汎用 v1 が最も経済的な選択肢になる可能性があります。</li><li>Azure Storage REST API の 2014-02-14 より前のバージョンか、クライアント ライブラリの 4.x より前のバージョンを使用していて、アプリケーションをアップグレードできない。</li></ul> |
| Standard Blob Storage | Blob Storage (ブロック BLOB および追加 BLOB のみ) | LRS、GRS、RA-GRS | リソース マネージャー | 可能であれば、Standard 汎用の v2 アカウントの使用をお勧めします。 |

## <a name="next-steps"></a>次のステップ

- [ストレージ アカウントの作成](storage-account-create.md)
- [汎用 v2 ストレージ アカウントにアップグレードする](storage-account-upgrade.md)
- [削除されたストレージ アカウントを復旧します](storage-account-recover.md)
