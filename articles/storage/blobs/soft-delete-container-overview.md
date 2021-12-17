---
title: コンテナーの論理的な削除
titleSuffix: Azure Storage
description: コンテナーの論理的な削除では、お客様のデータを保護して、アプリケーションまたは別のストレージ アカウント ユーザーによってデータが誤って変更または削除されたときに簡単に復旧できるようにします。
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 07/06/2021
ms.author: tamram
ms.subservice: blobs
ms.custom: references_regions, devx-track-azurepowershell
ms.openlocfilehash: e72b2ab64e664523ad6e8cecc268dd77658df52b
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132720532"
---
# <a name="soft-delete-for-containers"></a>コンテナーの論理的な削除

コンテナーの論理的な削除を使用すると、削除されたデータが一定期間システム内に保持されるので、データが誤って削除されるのを防ぐことができます。 保持期間中は、論理的に削除されたコンテナーとその内容を、削除された時点のコンテナーの状態に復元することができます。 保持期間が終了すると、コンテナーとその内容は完全に削除されます。

## <a name="recommended-data-protection-configuration"></a>推奨されるデータ保護の構成

BLOB の論理的な削除は、BLOB データの包括的なデータ保護戦略の一部です。 BLOB データの最適な保護のためには、次のデータ保護機能をすべて有効にすることをお勧めします。

- コンテナーの論理的な削除。削除されたコンテナーを復元します。 コンテナーの論理的な削除を有効にする方法については、「[コンテナーの論理的な削除を有効にして管理する](soft-delete-container-enable.md)」をご覧ください。
- BLOB のバージョン管理。以前のバージョンの BLOB が自動的に維持されます。 BLOB のバージョン管理が有効になっている場合は、以前のバージョンの BLOB を復元し、データが誤って変更または削除された場合に復旧することができます。 BLOB のバージョン管理を有効にする方法については、「[BLOB のバージョン管理を有効にして管理する](versioning-enable.md)」をご覧ください。
- BLOB の論理的な削除。削除された BLOB、スナップショット、またはバージョンを復元します。 BLOB の論理的な削除も有効にする方法については、[BLOB の論理的な削除の有効化と管理](soft-delete-blob-enable.md)に関する記事をご覧ください。

データ保護に関する Microsoft の推奨事項の詳細については、「[データ保護の概要](data-protection-overview.md)」を参照してください。

## <a name="how-container-soft-delete-works"></a>コンテナーの論理的な削除のしくみ

コンテナーの論理的な削除を有効にすると、削除されたコンテナーの保持期間を 1 - 365 日の範囲で指定できます。 既定の保有期間は 7 日です。 保持期間中は、**コンテナーの復元** 操作を呼び出せば、削除されたコンテナーを復旧することができます。

コンテナーを復元すると、コンテナーの BLOB と、BLOB のバージョンとスナップショットも復元されます。 ただし、コンテナー自体が削除されている場合にのみ、コンテナーの論理的な削除を使用して BLOB を復元できます。 親コンテナーが削除されていないときに削除された BLOB を復元するには、BLOB の論理的な削除または BLOB のバージョン管理を使用する必要があります。

> [!WARNING]
> コンテナーの論理的な削除を使用すると、コンテナー全体、および削除時の内容のみを復元することができます。 コンテナーの論理的な削除を使用して、削除された BLOB をコンテナー内で復元することはできません。 BLOB の論理的な削除と BLOB のバージョン管理も有効にして、コンテナー内の個々の BLOB を保護することをお勧めします。
>
> コンテナーを復元するときは、元の名前に復元する必要があります。 新しいコンテナーを作成するときに元の名前を使用すると、論理的に削除されたコンテナーを復元できません。

次の図は、コンテナーの論理的な削除が有効になっている場合に、削除されたコンテナーを復元する方法を示しています。

:::image type="content" source="media/soft-delete-container-overview/container-soft-delete-diagram.png" alt-text="論理的に削除されたコンテナーを復元する方法を示す図":::

保持期間が経過すると、コンテナーは Azure Storage から完全に削除され、復旧できなくなります。 コンテナーが削除された時点で保持期間のクロックが開始します。 保持期間はいつでも変更できますが、更新された保持期間は、新しく削除されたコンテナーにのみ適用されることに注意してください。 以前に削除されたコンテナーは、そのコンテナーが削除された時点で有効になった保持期間に基づいて完全に削除されます。

コンテナーの論理的な削除を無効にしても、以前に論理的に削除されたコンテナーが完全に削除されることはありません。 論理的に削除されたコンテナーは、そのコンテナーが削除された時点で有効になった保持期間が経過すると完全に削除されます。

コンテナーの論理的な削除は、次の種類のストレージ アカウントで使用できます。

- 汎用 v2 および v1 ストレージ アカウント
- ブロック BLOB ストレージ アカウント
- BLOB ストレージ アカウント

階層型名前空間が Azure Data Lake Storage Gen2 で使用可能になっているストレージ アカウントもサポートされています。

バージョン 2019-12-12 以降の Azure Storage REST API では、コンテナーの論理的な削除がサポートされています。

> [!IMPORTANT]
> コンテナーの論理的な削除では、ストレージ アカウントの削除からは保護されませんが、そのアカウント内のコンテナーの削除からは保護されます。 ストレージ アカウントを削除から保護するには、ストレージ アカウント リソースに対してロックを構成します。 Azure Resource Manager リソースのロックの詳細については、「[リソースのロックによる予期せぬ変更の防止](../../azure-resource-manager/management/lock-resources.md)」を参照してください。

## <a name="feature-support"></a>機能サポート

次の表は、アカウントでのこの機能のサポートと、特定の機能を有効にした場合のサポートへの影響を示しています。

| ストレージ アカウントの種類 | Blob Storage (既定のサポート) | Data Lake Storage Gen2 <sup>1</sup> | NFS 3.0 <sup>1</sup> | SFTP <sup>1</sup> |
|--|--|--|--|--|
| Standard 汎用 v2 | ![はい](../media/icons/yes-icon.png) |![はい](../media/icons/yes-icon.png)              | ![いいえ](../media/icons/no-icon.png) | ![いいえ](../media/icons/no-icon.png) |
| Premium ブロック BLOB          | ![はい](../media/icons/yes-icon.png) |![はい](../media/icons/yes-icon.png)              | ![いいえ](../media/icons/no-icon.png) | ![いいえ](../media/icons/no-icon.png) |

<sup>1</sup> Data Lake Storage Gen2、ネットワーク ファイル システム (NFS) 3.0 プロトコル、セキュア ファイル転送プロトコル (SFTP) のサポートでは、すべて階層型名前空間が有効になっているストレージ アカウントが必要です。

## <a name="pricing-and-billing"></a>価格と課金

コンテナーの論理的な削除を有効にするための追加料金は発生しません。 論理的に削除されたコンテナー内のデータは、アクティブなデータと同じレートで課金されます。

## <a name="next-steps"></a>次の手順

- [コンテナーの論理的な削除の構成](soft-delete-container-enable.md)
- [BLOB の論理的な削除](soft-delete-blob-overview.md)
- [BLOB バージョン管理](versioning-overview.md)
