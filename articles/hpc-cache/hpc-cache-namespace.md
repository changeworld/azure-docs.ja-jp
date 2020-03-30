---
title: Azure HPC キャッシュを作成する
description: Azure HPC Cache インスタンスを作成する方法
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: rohogue
ms.openlocfilehash: aaa939051a1aeafdb0650119772fc7214506aa8d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2020
ms.locfileid: "73582188"
---
# <a name="plan-the-aggregated-namespace"></a>集約された名前空間を計画する

Azure HPC Cache を使用すると、バックエンド ストレージ システムの細部が見えないようにする仮想名前空間を通じて、クライアントはさまざまなストレージ システムにアクセスすることができます。

ストレージ ターゲットを追加するときに、クライアント側のファイル パスを設定します。 クライアント コンピューターでは、このファイル パスをマウントし、ストレージ システムを直接マウントするのではなく、キャッシュに対してファイル読み取り要求を行うことができます。

この仮想ファイル システムは Azure HPC Cache によって管理されるため、クライアント側のパスを変更することなく、ストレージ ターゲットを変更できます。 たとえば、クライアント側の手順を変更することなく、ハードウェア ストレージ システムをクラウド ストレージに置き換えることができます。

## <a name="aggregated-namespace-example"></a>集約された名前空間の例

集約された名前空間を設計するにあたっては、クライアント マシンから必要な情報に手間なくアクセスできるように、また管理者やワークフロー エンジニアがパスを容易に識別できるようにしてください。

たとえば、Azure BLOB の格納データの処理に Azure HPC Cache インスタンスが使用されるシステムを考えてみます。 その分析には、オンプレミス データセンターに格納されたテンプレート ファイルが必要です。

テンプレート データはデータセンターに格納され、このジョブに必要な情報は次のサブディレクトリに格納されています。

    /goldline/templates/acme2017/sku798
    /goldline/templates/acme2017/sku980 

データセンターのストレージ システムは、次のエクスポートを公開します。

    /
    /goldline
    /goldline/templates

分析の対象となるデータは、[CLFSLoad ユーティリティ](hpc-cache-ingest.md#pre-load-data-in-blob-storage-with-clfsload)を使用して、"sourcecollection" という名前の Azure Blob Storage コンテナーにコピーされています。

キャッシュ経由で簡単にアクセスできるようにするために、次の仮想名前空間パスを使用してストレージ ターゲットを作成することを検討します。

| バックエンド ストレージ システム <br/> (NFS ファイル パスまたは BLOB コンテナー) | 仮想名前空間パス |
|-----------------------------------------|------------------------|
| /goldline/templates/acme2017/sku798     | /templates/sku798      |
| /goldline/templates/acme2017/sku980     | /templates/sku980      |
| sourcecollection                        | /source/               |

それぞれが一意のエクスポート パスを参照している限り、1 つの NFS ストレージ ターゲットに複数の仮想名前空間パスを使用できます。

NFS ソース パスは同じエクスポートのサブディレクトリであるため、同じストレージ ターゲットから複数の名前空間パスを定義する必要があります。

| ストレージ ターゲットのホスト名  | NFS エクスポートのパス      | サブディレクトリのパス | 名前空間のパス    |
|--------------------------|----------------------|-------------------|-------------------|
| *<IP アドレスまたはホスト名>* | /goldline/templates  | acme2017/sku798   | /templates/sku798 |
| *<IP アドレスまたはホスト名>* | /goldline/templates  | acme2017/sku980   | /templates/sku980 |

クライアント アプリケーションでキャッシュをマウントして、集約された名前空間のファイル パス (``/source``、``/templates/sku798``、``/templates/sku980``) に容易にアクセスできます。

## <a name="next-steps"></a>次のステップ

仮想ファイル システムの設定方法が決まったら、[ストレージ ターゲットを作成](hpc-cache-add-storage.md)して、バックエンド ストレージをクライアント側の仮想ファイル パスにマップします。
