---
title: 不変 BLOB データに対する法的な保持
titleSuffix: Azure Storage
description: 訴訟ホールドでは、BLOB データが明示的にクリアされるまで、書き込み 1 回、読み取り複数回 (WORM) 形式で BLOB データが格納されます。 データを WORM 状態に保つ必要がある期間が不明な場合は、訴訟ホールドを使用します。
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 07/22/2021
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 39108c5c5fd823e313ce616fbb4f6d6135e7d81e
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128645761"
---
# <a name="legal-holds-for-immutable-blob-data"></a>不変 BLOB データに対する法的な保持

訴訟ホールドは、法的調査の目的または一般的な保護ポリシーのために適用できる一時的な変更防止ポリシーです。 訴訟ホールドでは、BLOB データが明示的にクリアされるまで、書き込み 1 回、読み取り複数回 (WORM) 形式で BLOB データが格納されます。 訴訟ホールドを設定すると、BLOB の作成と読み取りは可能ですが、変更や削除はできません。 データを WORM 状態に保つ必要がある期間が不明な場合は、訴訟ホールドを使用します。

Blob Storage の不変ポリシーの詳細については、「[不変ストレージを使用してビジネスに不可欠な BLOB データを保存する](immutable-storage-overview.md)」を参照してください。

## <a name="legal-hold-scope"></a>訴訟ホールドのスコープ

訴訟ホールド ポリシーは、次のいずれかのスコープで構成できます。

- バージョン レベルのポリシー (プレビュー): 機密データをきめ細かく管理するために、個々の BLOB バージョン レベルで訴訟ホールドを構成できます。
- コンテナー レベルのポリシー: コンテナー レベルで構成されている訴訟ホールドは、そのコンテナー内のすべての BLOB に適用されます。 個々のオブジェクトを、独自の不変ポリシーを使用して構成することはできません。

### <a name="version-level-policy-scope-preview"></a>バージョンレベルのポリシーのスコープ (プレビュー)

BLOB のバージョンに対して訴訟ホールドを構成するには、まず、親コンテナーでバージョンレベルの不変性を有効にする必要があります。 バージョン レベルの変更をいったん有効にすると、それをその後無効にすることはできません。 詳細については、「[コンテナーでバージョンレベルの不変性のサポートを有効にする](immutable-policy-configure-version-scope.md#enable-support-for-version-level-immutability-on-a-container)」を参照してください。

コンテナーに対してバージョンレベルの不変性を有効にすると、コンテナー レベルで訴訟ホールドを設定できなくなります。 訴訟ホールドは、個々の BLOB バージョンに適用する必要があります。 BLOB の現在のバージョンまたは以前のバージョンに対して、訴訟ホールドを構成できます。

バージョンレベルの訴訟ホールド ポリシーでは、ストレージ アカウントで BLOB のバージョン管理が有効になっている必要があります。 BLOB のバージョン管理を有効にする方法については、「[BLOB のバージョン管理を有効にして管理する](versioning-enable.md)」をご覧ください。 バージョン管理を有効にすると、課金に影響する場合があることに注意してください。 詳細については、「[BLOB のバージョン管理](versioning-overview.md#pricing-and-billing)」の「**価格と課金**」セクションを参照してください。

バージョン レベルの訴訟ホールドを有効にする方法の詳細については、「[訴訟ホールドを構成またはクリアする](immutable-policy-configure-version-scope.md#configure-or-clear-a-legal-hold)」を参照してください。

### <a name="container-level-scope"></a>コンテナー レベルのスコープ

コンテナーの訴訟ホールドを構成する場合、そのホールドはコンテナー内のすべてのオブジェクトに適用されます。 訴訟ホールドがクリアされた場合、コンテナーに対して有効な時間ベースの保持ポリシーがない限り、クライアントはコンテナー内のオブジェクトの書き込みと削除を再度実行できます。

訴訟ホールドをコンテナーに適用すると、既存のすべての BLOB が 30 秒以内に不変 WORM 状態に移行します。 そのポリシーで保護されたコンテナーにアップロードされるすべての新しい BLOB も不変状態に移行します。 すべての BLOB が不変状態になると、不変コンテナー内の上書きまたは削除操作は許可されなくなります。 階層型名前空間があるアカウントの場合、BLOB の名前を変更したり、別のディレクトリに移動したりすることはできません。

コンテナー レベルのスコープで訴訟ホールドを構成する方法については、「[訴訟ホールドを構成またはクリアする](immutable-policy-configure-container-scope.md#configure-or-clear-a-legal-hold)」を参照してください。

#### <a name="legal-hold-tags"></a>訴訟ホールドのタグ

コンテナー レベルの訴訟ホールドは、識別子文字列として機能する 1 つ以上のユーザー定義英数字タグに関連付けられている必要があります。 たとえば、タグにはケース ID またはイベント名を含めることができます。

#### <a name="audit-logging"></a>監査ログ

有効な訴訟ホールドを持つ各コンテナーは、ポリシー監査ログを提供します。 ログにはユーザー ID、コマンドの種類、タイム スタンプ、訴訟ホールド タグが含まれます。 この監査ログは、SEC 17a-4(f) 規制ガイドラインに従い、ポリシーの有効期間の間、保持されます。

すべての管理サービス アクティビティのより包括的なログは、[Azure アクティビティ ログ](../../azure-monitor/essentials/platform-logs-overview.md)に表示されます。 [Azure リソース ログ](../../azure-monitor/essentials/platform-logs-overview.md)には、データ操作に関する情報が保持されます。 規制や他の目的で必要になる可能性のあるログは、ユーザーが永続的に保存する必要があります。

#### <a name="limits"></a>制限

コンテナーレベルの訴訟ホールドには、次の制限が適用されます。

- ストレージ アカウントで、訴訟ホールドが設定されたコンテナーの最大数は 10,000 です。
- コンテナーで、訴訟ホールド タグの最大数は 10 です。
- 訴訟ホールド タグの最短の長さは英数字 3 文字です。 最大長は英数字 23 文字です。
- コンテナーで、ポリシーの有効期間中に保持される訴訟ホールド ポリシー監査ログの最大数は 10 です。

## <a name="next-steps"></a>次のステップ

- [データ保護の概要](data-protection-overview.md)
- [不変ストレージを使用してビジネスに不可欠な BLOB データを保存する](immutable-storage-overview.md)
- [不変 BLOB データに対する時間ベースの保持ポリシー](immutable-time-based-retention-policy-overview.md)
- [BLOB バージョンに対する不変性ポリシーを構成する (プレビュー)](immutable-policy-configure-version-scope.md)
- [コンテナーの不変性ポリシーを構成する](immutable-policy-configure-container-scope.md)
