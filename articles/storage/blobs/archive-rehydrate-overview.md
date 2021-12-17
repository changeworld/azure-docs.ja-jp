---
title: アーカイブ層からの BLOB のリハイドレート
description: BLOB はアーカイブ アクセス層に含まれていますが、オフラインであると見なされ、読み取りや変更ができません。 アーカイブされた BLOB のデータの読み取りまたは変更を行うには、まず、ホットまたはクール層のいずれかのオンライン層に BLOB をリハイドレートする必要があります。
services: storage
author: tamram
ms.author: tamram
ms.date: 10/25/2021
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.reviewer: fryu
ms.openlocfilehash: 170d6e8cdd8115eab48d7e0714fe189305488f67
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131013576"
---
# <a name="blob-rehydration-from-the-archive-tier"></a>アーカイブ層からの BLOB のリハイドレート

BLOB はアーカイブ アクセス層に含まれていますが、オフラインであると見なされ、読み取りや変更ができません。 アーカイブされた BLOB のデータの読み取りまたは変更を行うには、まず、ホットまたはクール層のいずれかのオンライン層に BLOB をリハイドレートする必要があります。 アーカイブ層に格納されている BLOB をリハイドレートするには、次の 2 つのオプションがあります。

- [アーカイブ済み BLOB をオンライン層にコピーする](#copy-an-archived-blob-to-an-online-tier): [BLOB のコピー](/rest/api/storageservices/copy-blob)操作でホットまたはクール層の新しい BLOB にコピーすることで、アーカイブされた BLOB をリハイドレートできます。 Microsoft ではほとんどのシナリオでこのオプションを推奨します。

- [BLOB のアクセス層をオンライン層に変更する](#change-a-blobs-access-tier-to-an-online-tier): [BLOB 層の設定](/rest/api/storageservices/set-blob-tier)操作を使用してその層を変更することにより、アーカイブされた BLOB をホットまたはクール層にリハイドレートすることができます。

アーカイブ層からの BLOB のリハイドレートは、完了までに数時間かかることがあります。 Microsoft では、最適なパフォーマンスを得るために、大きな BLOB をリハイドレートするよう推奨しています。 複数の小さな BLOB を同時にリハイドレートすると、余分に時間がかかる場合があります。 ストレージ アカウントあたり最大 10 GiB を 1 時間ごとにリハイドレートできます。

アーカイブ層からオンライン層に BLOB をリハイドレートし、イベントをイベント ハンドラーに送信するときに、イベントを発生させるように [Azure Event Grid](../../event-grid/overview.md) を構成できます。 詳細については、「[BLOB のリハイドレート イベントの処理](#handle-an-event-on-blob-rehydration)」を参照してください。

Azure Storage のアクセス層の詳細については、「[BLOB データのホット、クールおよびアーカイブ アクセス層](access-tiers-overview.md)」を参照してください。

## <a name="rehydration-priority"></a>リハイドレートの優先度

BLOB をリハイドレートするときは、[BLOB 層の設定](/rest/api/storageservices/set-blob-tier)または [BLOB のコピー](/rest/api/storageservices/copy-blob)操作でオプションの *x-ms-rehydrate-priority* ヘッダーを使用して、リハイドレート操作の優先度を設定できます。 リハイドレート優先順位のオプションは次のとおりです。

- **Standard の優先度**:このリハイドレート要求は、受信した順序で処理され、最大で 15 時間かかることがあります。
- **高優先度**: リハイドレート要求は標準の優先度の要求よりも優先され、サイズが 10 GB 未満のオブジェクトでは 1 時間以内に完了することがあります。

リハイドレートの進行中にその優先順位を確認するには、[Get Blob Properties](/rest/api/storageservices/get-blob-properties) を呼び出して、戻り値の `x-ms-rehydrate-priority` ヘッダー値を参照します。 リハイドレートの優先度プロパティは *標準* または *高* を返します。

標準優先度は、アーカイブでの既定のリハイドレート オプションです。 高優先度のリハイドレートは高速ですが、標準優先度のリハイドレートよりもコストがかかります。 BLOB のサイズと現在の需要によっては、高優先度の処理にかかる時間が 1 時間を超える場合もあります。 Microsoft では、緊急データ復元の状況で使用するために、高優先度のリハイドレートの予約を推奨します。

標準の優先度のリハイドレート操作は保留中ですが、BLOB のリハイドレートの優先度設定を "*高*" に更新して、BLOB をより迅速にリハイドレートすることができます。 たとえば、大量の BLOB を一括でリハイドレートしている場合は、初期操作のすべての BLOB に対して "*標準*" の優先度を指定してから、より迅速にオンラインにする必要がある個々の BLOB に対して、1 時間あたり 10 GiB の上限に達するまで優先度を "*高く*" することができます。

保留中の操作では、リハイドレートの優先度設定を "*高*" から "*標準*" に下げることはできません。 リハイドレートの優先度設定を更新すると、課金に影響する場合があることに注意してください。

リハイドレートの優先度設定を設定および更新する方法については、「[アーカイブ済み BLOB をオンライン層にリハイドレートする](archive-rehydrate-to-online-tier.md)」を参照してください。

標準優先度と高優先度のリハイドレート要求の価格の違いの詳細については、「[Azure Blob Storage の価格](https://azure.microsoft.com/pricing/details/storage/blobs/)」を参照してください。

## <a name="copy-an-archived-blob-to-an-online-tier"></a>アーカイブ済み BLOB をオンライン層にコピーする

アーカイブ層からオンライン層に BLOB を移動するための最初のオプションは、アーカイブされた BLOB をホットまたはクール層の新しいコピー先 BLOB にコピーすることです。 BLOB をコピーするには、[BLOB のコピー](/rest/api/storageservices/copy-blob)操作を使用します。 アーカイブ済み BLOB をオンライン層の新しい BLOB にコピーする場合、ソース BLOB はアーカイブ層で変更されずに残ります。

アーカイブされた BLOB は、別の名前の新しい BLOB または別のコンテナーにコピーする必要があります。 同じ BLOB にコピーして、ソース BLOB を上書きすることはできません。

Microsoft では、次の理由により、BLOB をアーカイブ層からオンライン層に移動する必要があるほとんどのシナリオでコピー操作を実行することをお勧めします。

- コピー操作では、必要な 180 日が経過する前にアーカイブ層から BLOB の層を変更した場合に評価される早期削除料金が回避されます。 詳しくは、「[アーカイブ アクセス層](access-tiers-overview.md#archive-access-tier)」をご覧ください。
- ストレージ アカウントにライフサイクル管理ポリシーが有効になっている場合、[BLOB 層の設定](/rest/api/storageservices/set-blob-tier)で BLOB をリハイドレートすると、最終更新時刻がポリシーに設定されているしきい値を超えているため、リハイドレート後にライフサイクル ポリシーによって BLOB がアーカイブ層に戻されます。 コピー操作では、ソース BLOB がアーカイブ層に残り、別の名前と新しい最終更新時刻による新しい BLOB が作成されます。そのため、ライフサイクル ポリシーによって、リハイドレートされた BLOB がアーカイブ層に戻るリスクはありません。

選択したリハイドレート優先度に応じて、アーカイブ層から BLOB をコピーするのに数時間かかることがあります。 BLOB コピー操作では、バックグラウンドでアーカイブ ソース BLOB が読み取られ、選択された移動先の層に新しいオンライン BLOB が作成されます。 リハイドレート操作が完了する前に親コンテナー内の BLOB を一覧表示すると、新しい BLOB が表示される場合がありますが、その層はアーカイブに設定されます。アーカイブ層のソース BLOB からの読み取り操作が完了し、BLOB の内容がオンライン層の新しいコピー先 BLOB に書き込まれるまで、データは使用できません。 新しい BLOB は独立したコピーであるため、変更または削除しても、アーカイブ層のソース BLOB には影響しません。

BLOB をオンライン層にコピーしてリハイドレートする方法については、「[コピー操作で BLOB をリハイドレートする](archive-rehydrate-to-online-tier.md#rehydrate-a-blob-with-a-copy-operation)」を参照してください。

> [!IMPORTANT]
> コピー先でコピーが正常に完了するまでは、コピー元 BLOB を削除しないでください。 コピー元 BLOB が削除された場合、コピー先 BLOB はコピーを完了できず、空になります。 コピー操作の完了で発生するイベントを処理して、ソース BLOB を削除しても安全な場合を確認できます。 詳細については、「[BLOB のリハイドレート イベントの処理](#handle-an-event-on-blob-rehydration)」を参照してください。

アーカイブ済み BLOB をオンラインの宛先層にコピーする方法は、同じストレージ アカウント内でのみサポートされています。 アーカイブされた BLOB を、アーカイブ層にもあるコピー先 BLOB にコピーすることはできません。

次の表は、ソース BLOB とコピー先 BLOB の層に応じた BLOB コピー操作の動作を示しています。

|  | **ホット層のコピー元** | **クール層のコピー元** | **アーカイブ層のコピー元** |
|--|--|--|--|
| **ホット層のコピー先** | サポートされています | サポートされています | 同じアカウント内でサポートされます。 BLOB のリハイドレートが必要です。 |
| **クール層のコピー先** | サポートされています | サポートされています | 同じアカウント内でサポートされます。 BLOB のリハイドレートが必要です。 |
| **アーカイブ層のコピー先** | サポートされています | サポートされています | サポートされていない |

## <a name="change-a-blobs-access-tier-to-an-online-tier"></a>BLOB のアクセス層をオンライン層に変更する

アーカイブ層からオンライン層に BLOB をリハイドレートする 2 つ目のオプションは、[BLOB 層の設定](/rest/api/storageservices/set-blob-tier)を呼び出すことにより、BLOB の層を変更することです。 この操作により、アーカイブされた BLOB の層をホットまたはクールに変更できます。

[Set Blob Tier](/rest/api/storageservices/set-blob-tier) 要求が開始された後は、キャンセルできません。 リハイドレート操作中、BLOB のアクセス層の設定は、リハイドレート プロセスが完了するまでアーカイブ済みとして引き続き表示されます。 リハイドレート操作が完了すると、BLOB のアクセス層プロパティが更新され、新しい層が反映されます。

階層をオンライン層に変更して BLOB をリハイドレートする方法については、「[その層を変更して BLOB をリハイドレートする](archive-rehydrate-to-online-tier.md#rehydrate-a-blob-by-changing-its-tier)」を参照してください。

> [!CAUTION]
> BLOB の層を変更しても、最後に変更された時刻には影響しません。 ストレージ アカウントに [ライフサイクル管理](./lifecycle-management-overview.md)ポリシーが有効になっている場合、**BLOB 層の設定** で BLOB をリハイドレートすると、最終更新時刻がポリシーに設定されているしきい値を超えているため、リハイドレート後にライフサイクル ポリシーによって BLOB がアーカイブ層に戻されます。
>
> このシナリオを回避するには、「[アーカイブ済み BLOB をオンライン層にコピーする](#copy-an-archived-blob-to-an-online-tier)」セクションの説明に従って、アーカイブ済み BLOB を代わりにコピーしてリハイドレートします。 コピー操作を実行すると、最終更新時刻を更新された BLOB の新しいインスタンスが作成されるため、ライフサイクル管理ポリシーがトリガされることはありません。

## <a name="check-the-status-of-a-blob-rehydration-operation"></a>BLOB リハイドレート操作の状態を確認する

BLOB のリハイドレート操作中に、[Get Blob Properties](/rest/api/storageservices/get-blob-properties) 操作を呼び出して、その状態を確認できます。 リハイドレート操作の状態を確認する方法については、「[リハイドレート操作の状態を確認する](archive-rehydrate-to-online-tier.md#check-the-status-of-a-rehydration-operation)」を参照してください。

## <a name="handle-an-event-on-blob-rehydration"></a>BLOB リハイドレートでイベントを処理する

アーカイブされた BLOB のリハイドレートには最大 15 時間かかる場合があります。また、リハイドレートが完了したかどうかを判断するために **Get Blob Properties** を繰り返しポーリングする処理は非効率的です。 [Azure Event Grid](../../event-grid/overview.md) を使用して、リハイドレート完了時に発生するイベントをキャプチャすると、パフォーマンスが向上し、コストが最適化されます。

Azure Event Grid は、BLOB のリハイドレートにどの操作が使われたかによって、BLOB のリハイドレート時に以下の 2 つのイベントのうち 1 つを発生させます。

- **Microsoft.Storage.BlobCreated** イベント ファイルは BLOB が作成されたときに発生します。 BLOB のリハイドレートのコンテキストでは、このイベントは、[BLOB のコピー](/rest/api/storageservices/copy-blob)操作によってホットまたはクール層のいずれかに新しいコピー先 BLOB が作成され、BLOB のデータがアーカイブ層から完全にリハイドレートされるときに発生します。
- **Microsoft.Storage.BlobTierChanged** イベントは、BLOB の層が変更されたときに発生します。 BLOB のリハイドレートのコンテキストでは、このイベントは、[BLOB 層の設定](/rest/api/storageservices/set-blob-tier) 操作によってアーカイブ済みの BLOB の層がホットまたはクール層に正常に変更されたときに発生します。

リハイドレート時にイベントをキャプチャして Azure Function のイベント ハンドラーに送信する方法については、「[BLOB リハイドレート イベントに応答して Azure Function を実行する](archive-rehydrate-handle-event.md)」を参照してください。

Blob Storage でのイベント処理の詳細については、「[Azure Blob Storage のイベントに反応する](storage-blob-event-overview.md)」、「[Event Grid のソースとしてのAzure Blob Storage](../../event-grid/event-schema-blob-storage.md)」を参照してください。

## <a name="pricing-and-billing"></a>価格と課金

[Set Blob Tier](/rest/api/storageservices/set-blob-tier) を使用したリハイドレートは、データ読み取りトランザクションとデータ取得サイズで課金されます。 高優先度を使用すると、標準優先度と比較して、操作とデータ取得のコストが高くなります。 High 優先度のリハイドレートは、請求書に別の行の項目として表示されます。 数ギガバイトのアーカイブ BLOB を返す高優先度の要求で 5 時間以上かかった場合、高優先度の取得料金は課金されません。 ただし、Standard の取得料金は適用されます。

[BLOB のコピー](/rest/api/storageservices/copy-blob)を使用してアーカイブ済み BLOB をオンライン層にコピーする場合は、データ読み取りトランザクションとデータ取得サイズに対して課金されます。 オンライン層での宛先 BLOB の作成は、データ書き込みトランザクションに対して課金されます。 ソース BLOB はアーカイブ層で変更されていないため、オンライン BLOB にコピーする場合、早期削除料金は適用されません。 優先順位の高い取得料金は、選択した場合に適用されます。

アーカイブ層の BLOB は、少なくとも 180 日保持する必要があります。 180 日の期間が経過する前にアーカイブ済み BLOB のレベルを削除または変更すると、早期削除料金が発生します。 詳しくは、「[アーカイブ アクセス層](access-tiers-overview.md#archive-access-tier)」をご覧ください。

ブロック BLOB とデータ ハイドレートの価格の詳細については、[Azure Storage の価格](https://azure.microsoft.com/pricing/details/storage/blobs/)に関するページを参照してください。 送信データ転送の価格の詳細については、[データ転送料金の詳細](https://azure.microsoft.com/pricing/details/data-transfers/)に関するページを参照してください。

## <a name="see-also"></a>関連項目

- [BLOB データのホット、クール、およびアーカイブ アクセス層](access-tiers-overview.md)。
- [アーカイブ済み BLOB をオンライン層にリハイドレートする](archive-rehydrate-to-online-tier.md)
- [BLOB リハイドレート イベントに応答して Azure 関数を実行する](archive-rehydrate-handle-event.md)
- [Blob Storage のイベント処理](storage-blob-event-overview.md)