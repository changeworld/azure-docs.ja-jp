---
title: Azure Stack 上の Azure Monitor でサポートされるメトリック | Microsoft Docs
description: Azure Stack 上の Azure Monitor でサポートされるメトリックについて説明します。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/06/2018
ms.author: mabrigg
ms.openlocfilehash: 7ebb9d683803a6c5a5a0fa25308b348eeea586c4
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2018
ms.locfileid: "53097898"
---
# <a name="supported-metrics-with-azure-monitor-on-azure-stack"></a>Azure Stack 上の Azure Monitor でサポートされるメトリック

*適用対象: Azure Stack 統合システム*

グローバル Azure と同様に、Azure Stack 上の Azure Monitor からメトリックを取得できます。 ポータルでメジャーを作成したり、REST API からそれらを取得したり、PowerShell または CLI を使用してそれらを照会したりできます。

次の表には、Azure Stack 上の Azure Monitor のメトリック パイプラインで利用できるメトリックの一覧を示しています。 これらのメトリックに対してクエリを実行してアクセスするには、**2018-01-01** api-version バージョンの API プロファイルを使用することが必要になります。 API プロファイルと Azure Stack の詳細については、「[Azure Stack での API バージョンのプロファイルの管理](azure-stack-version-profiles.md)」を参照してください。

## <a name="microsoftcomputevirtualmachines"></a>Microsoft.Compute/virtualMachines

| メトリック | メトリックの表示名 | 単位 | 集計の種類 | 説明 | ディメンション |
|----------------|---------------------|---------|------------------|-----------------------------------------------------------------------------------------------|---------------|
| Percentage CPU | CPU の割合 | Percent | 平均 | 仮想マシンで現在使用されている、割り当てられたコンピューティング ユニットの割合 | ディメンションなし |

## <a name="microsoftstoragestorageaccounts"></a>Microsoft.Storage/storageAccounts

| メトリック | メトリックの表示名 | 単位 | 集計の種類 | 説明 | ディメンション |
|----------------------|------------------------|--------------|------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------|
| UsedCapacity | Used capacity (使用済み容量) | Bytes | 平均 | アカウントの使用済み容量 | ディメンションなし |
| トランザクション | トランザクション | Count | 合計 | ストレージ サービスまたは指定された API 操作に対して行われた要求の数。 この数には、成功した要求と失敗した要求およびエラーが発生した要求が含まれます。 別の種類の応答の数には ResponseType ディメンションを使います。 | ResponseType、GeoType、ApiName |
| Ingress | イングレス | Bytes | 合計 | イングレス データの量 (バイト単位)。 この値には、外部クライアントから Azure Storage へのイングレスおよび Azure 内でのイングレスが含まれます。 | GeoType、ApiName |
| Egress | エグレス | Bytes | 合計 | エグレス データの量 (バイト単位)。 この値には、外部クライアントから Azure Storage へのエグレスおよび Azure 内でのエグレスが含まれます。 そのため、この値は課金対象のエグレスを反映しません。 | GeoType、ApiName |
| SuccessServerLatency | Success Server Latency (成功サーバー待機時間) | ミリ秒 | 平均 | 成功した要求の処理に Azure Storage が使った平均待機時間 (ミリ秒単位)。 この値には、AverageE2ELatency で指定されているネットワーク待機時間は含まれません。 | GeoType、ApiName |
| SuccessE2ELatency | Success E2E Latency (成功 E2E 待機時間) | ミリ秒 | 平均 | ストレージ サービスまたは指定された API 操作に対して行われた成功した要求の平均エンド ツー エンド待機時間 (ミリ秒単位)。 この値には、要求の読み取り、応答の送信、および応答の受信確認を受け取るために Azure Storage 内で必要な処理時間が含まれます。 | GeoType、ApiName |
| 可用性 | 可用性 | Percent | 平均 | ストレージ サービスまたは指定された API 操作の可用性の割合。 可用性は、TotalBillableRequests の値を取得し、それを該当する要求の数 (予期しないエラーが発生した要求を含む) で割ることによって、計算されます。 すべての予期しないエラーは、ストレージ サービスまたは指定された API 操作の可用性の低下をもたらします。 | GeoType、ApiName |

## <a name="microsoftstoragestorageaccountsblobservices"></a>Microsoft.Storage/storageAccounts/blobServices

| メトリック | メトリックの表示名 | 単位 | 集計の種類 | 説明 | ディメンション |
|----------------------|------------------------|--------------|------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------|
| BlobCapacity | Blob Capacity (BLOB 容量) | Bytes | 合計 | ストレージ アカウントの Blob service によって使われているストレージの量 (バイト単位)。 | BlobType |
| BlobCount | Blob Count (BLOB 数) | Count | 合計 | ストレージ アカウントの Blob service 内の BLOB の数。 | BlobType |
| ContainerCount | Blob Container Count (BLOB コンテナー数) | Count | 平均 | ストレージ アカウントの Blob service 内のコンテナーの数。 | ディメンションなし |
| トランザクション | トランザクション | Count | 合計 | ストレージ サービスまたは指定された API 操作に対して行われた要求の数。 この数には、成功した要求と失敗した要求およびエラーが発生した要求が含まれます。 別の種類の応答の数には ResponseType ディメンションを使います。 | ResponseType、GeoType、ApiName |
| Ingress | イングレス | Bytes | 合計 | イングレス データの量 (バイト単位)。 この値には、外部クライアントから Azure Storage へのイングレスおよび Azure 内でのイングレスが含まれます。 | GeoType、ApiName |
| Egress | エグレス | Bytes | 合計 | エグレス データの量 (バイト単位)。 この値には、外部クライアントから Azure Storage へのエグレスおよび Azure 内でのエグレスが含まれます。 そのため、この値は課金対象のエグレスを反映しません。 | GeoType、ApiName |
| SuccessServerLatency | Success Server Latency (成功サーバー待機時間) | ミリ秒 | 平均 | 成功した要求の処理に Azure Storage が使った平均待機時間 (ミリ秒単位)。 この値には、AverageE2ELatency で指定されているネットワーク待機時間は含まれません。 | GeoType、ApiName |
| SuccessE2ELatency | Success E2E Latency (成功 E2E 待機時間) | ミリ秒 | 平均 | ストレージ サービスまたは指定された API 操作に対して行われた成功した要求の平均エンド ツー エンド待機時間 (ミリ秒単位)。 この値には、要求の読み取り、応答の送信、および応答の受信確認を受け取るために Azure Storage 内で必要な処理時間が含まれます。 | GeoType、ApiName |
| 可用性 | 可用性 | Percent | 平均 | ストレージ サービスまたは指定された API 操作の可用性の割合。 可用性は、TotalBillableRequests の値を取得し、それを該当する要求の数 (予期しないエラーが発生した要求を含む) で割ることによって、計算されます。 すべての予期しないエラーは、ストレージ サービスまたは指定された API 操作の可用性の低下をもたらします。 | GeoType、ApiName |

## <a name="microsoftstoragestorageaccountstableservices"></a>Microsoft.Storage/storageAccounts/tableServices

| メトリック | メトリックの表示名 | 単位 | 集計の種類 | 説明 | ディメンション |
|----------------------|------------------------|--------------|------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------|
| TableCapacity | Table Capacity (テーブル容量) | Bytes | 平均 | ストレージ アカウントの Table service によって使われているストレージの量 (バイト単位)。 | ディメンションなし |
| TableCount | Table Count (テーブル数) | Count | 平均 | ストレージ アカウントの Table service 内のテーブルの数。 | ディメンションなし |
| TableEntityCount | Table Entity Count (テーブル エンティティ数) | Count | 平均 | ストレージ アカウントの Table service 内のテーブル エンティティの数。 | ディメンションなし |
| トランザクション | トランザクション | Count | 合計 | ストレージ サービスまたは指定された API 操作に対して行われた要求の数。 この数には、成功した要求と失敗した要求およびエラーが発生した要求が含まれます。 別の種類の応答の数には ResponseType ディメンションを使います。 | ResponseType、GeoType、ApiName |
| Ingress | イングレス | Bytes | 合計 | イングレス データの量 (バイト単位)。 この値には、外部クライアントから Azure Storage へのイングレスおよび Azure 内でのイングレスが含まれます。 | GeoType、ApiName |
| Egress | エグレス | Bytes | 合計 | エグレス データの量 (バイト単位)。 この値には、外部クライアントから Azure Storage へのエグレスおよび Azure 内でのエグレスが含まれます。 そのため、この値は課金対象のエグレスを反映しません。 | GeoType、ApiName |
| SuccessServerLatency | Success Server Latency (成功サーバー待機時間) | ミリ秒 | 平均 | 成功した要求の処理に Azure Storage が使った平均待機時間 (ミリ秒単位)。 この値には、AverageE2ELatency で指定されているネットワーク待機時間は含まれません。 | GeoType、ApiName |
| SuccessE2ELatency | Success E2E Latency (成功 E2E 待機時間) | ミリ秒 | 平均 | ストレージ サービスまたは指定された API 操作に対して行われた成功した要求の平均エンド ツー エンド待機時間 (ミリ秒単位)。 この値には、要求の読み取り、応答の送信、および応答の受信確認を受け取るために Azure Storage 内で必要な処理時間が含まれます。 | GeoType、ApiName |
| 可用性 | 可用性 | Percent | 平均 | ストレージ サービスまたは指定された API 操作の可用性の割合。 可用性は、TotalBillableRequests の値を取得し、それを該当する要求の数 (予期しないエラーが発生した要求を含む) で割ることによって、計算されます。 すべての予期しないエラーは、ストレージ サービスまたは指定された API 操作の可用性の低下をもたらします。 | GeoType、ApiName |

## <a name="microsoftstoragestorageaccountsqueueservices"></a>Microsoft.Storage/storageAccounts/queueServices

| メトリック | メトリックの表示名 | 単位 | 集計の種類 | 説明 | ディメンション |
|----------------------|------------------------|--------------|------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------|
| QueueCapacity | Queue Capacity (キュー容量) | Bytes | 平均 | ストレージ アカウントの Queue サービスによって使われているストレージの量 (バイト単位)。 | ディメンションなし |
| QueueCount | Queue Count (キュー数) | Count | 平均 | ストレージ アカウントの Queue サービス内のキューの数。 | ディメンションなし |
| QueueMessageCount | Queue Message Count (キュー メッセージ数) | Count | 平均 | ストレージ アカウントの Queue サービス内のキュー メッセージの概数。 | ディメンションなし |
| トランザクション | トランザクション | Count | 合計 | ストレージ サービスまたは指定された API 操作に対して行われた要求の数。 この数には、成功した要求と失敗した要求およびエラーが発生した要求が含まれます。 別の種類の応答の数には ResponseType ディメンションを使います。 | ResponseType、GeoType、ApiName |
| Ingress | イングレス | Bytes | 合計 | イングレス データの量 (バイト単位)。 この値には、外部クライアントから Azure Storage へのイングレスおよび Azure 内でのイングレスが含まれます。 | GeoType、ApiName |
| Egress | エグレス | Bytes | 合計 | エグレス データの量 (バイト単位)。 この値には、外部クライアントから Azure Storage へのエグレスおよび Azure 内でのエグレスが含まれます。 そのため、この値は課金対象のエグレスを反映しません。 | GeoType、ApiName |
| SuccessServerLatency | Success Server Latency (成功サーバー待機時間) | ミリ秒 | 平均 | 成功した要求の処理に Azure Storage が使った平均待機時間 (ミリ秒単位)。 この値には、AverageE2ELatency で指定されているネットワーク待機時間は含まれません。 | GeoType、ApiName |
| SuccessE2ELatency | Success E2E Latency (成功 E2E 待機時間) | ミリ秒 | 平均 | ストレージ サービスまたは指定された API 操作に対して行われた成功した要求の平均エンド ツー エンド待機時間 (ミリ秒単位)。 この値には、要求の読み取り、応答の送信、および応答の受信確認を受け取るために Azure Storage 内で必要な処理時間が含まれます。 | GeoType、ApiName |
| 可用性 | 可用性 | Percent | 平均 | ストレージ サービスまたは指定された API 操作の可用性の割合。 可用性は、TotalBillableRequests の値を取得し、それを該当する要求の数 (予期しないエラーが発生した要求を含む) で割ることによって、計算されます。 すべての予期しないエラーは、ストレージ サービスまたは指定された API 操作の可用性の低下をもたらします。 | GeoType、ApiName |

## <a name="next-steps"></a>次の手順

[Azure Stack 上の Azure Monitor](azure-stack-metrics-azure-data.md) について学習します。
