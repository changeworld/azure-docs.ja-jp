---
title: Azure ディスク プール (プレビュー) の概要
description: Azure ディスク プール (プレビュー) について説明します。
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 07/23/2021
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: ea06b3c4fdf9be626df99f4a06f4190ce86df4b3
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/03/2021
ms.locfileid: "123433280"
---
# <a name="azure-disk-pools-preview"></a>Azure ディスク プール (プレビュー)

Azure リソースである Azure ディスク プール (プレビュー) を使用すると、アプリケーションやワークロードで 1 つのエンドポイントからマネージド ディスクのグループにアクセスできます。 ディスク プールでは、Internet Small Computer Systems Interface (iSCSI) ターゲットを公開し、iSCSI によるこのプール内のディスクへのデータ アクセスを有効にできます。 各ディスク プールには 1 つの iSCSI ターゲットを設定でき、各ディスクを iSCSI LUN として公開できます。 ディスク プールの下のディスクを、データストアとしての Azure VMware Solution ホストに接続できます。 これにより、Azure VMware Solution ホストから独立してストレージをスケーリングできます。 データストアが構成されたら、そこにボリュームを作成し、VMware インスタンスにアタッチすることができます。

## <a name="how-it-works"></a>しくみ

ディスク プールをデプロイするときに、管理対象リソース グループが自動的に作成されます。 この管理対象リソース グループには、ディスク プールの操作に必要なすべての Azure リソースが含まれます。 これらのリソース グループの名前付け規則は、MSP_<リソース グループ名>_<ディスク プール名>\_<リージョン名> です。

マネージド ディスクをディスク プールに追加すると、そのディスクはマネージド iSCSI コントローラーにアタッチされます。 複数のマネージド ディスクをストレージ ターゲットとしてディスク プールに追加でき、各ストレージ ターゲットは、ディスク プールの iSCSI ターゲットで iSCSI LUN として提示されます。 ディスク プールでは、Azure VMware Solution のネイティブ サポートが提供されます。 Azure VMware Solution クラスターからディスク プールに接続することができ、そこにはその環境内のすべての Azure VMware Solution ホストが含まれます。 次の図は、Azure VMware Solution で可能なディスク プールの使用方法を示したものです。

:::image type="content" source="media/disks-pools/disk-pool-diagram.png" alt-text="ディスク プールのしくみを示す図。各 Ultra Disk には iSCSI を使用して各 iSCSI コントローラーからアクセスでき、Azure VMware Solution ホストは iSCSI を使用して iSCSI コントローラーにアクセスできます。":::

## <a name="restrictions"></a>制限

プレビューのディスク プールには、次の制限があります。

- ディスク プールに追加できるのは、Premium SSD または Ultra Disk のみです。
- [ゾーン冗長ストレージ (ZRS)](disks-redundancy.md#zone-redundant-storage-for-managed-disks) を使用するディスクは現在サポートされていません。 

### <a name="regional-availability"></a>リージョン別の提供状況

ディスク プールは現在、次のリージョンで利用できます。

- オーストラリア東部
- カナダ中部
- 米国中部
- 米国東部
- 米国西部 2
- 東日本
- 北ヨーロッパ
- 西ヨーロッパ
- 東南アジア
- 英国南部


## <a name="billing"></a>課金

ディスク プールをデプロイすると、2 つの主な領域で課金コストが発生します。

- ディスク プールに追加されたディスク
- ディスク プールに付属する管理対象リソース グループにデプロイされた Azure リソース。 これらのリソースは次のとおりです。
    - 仮想マシン
    - マネージド ディスク。
    - ネットワーク インターフェイス 1 つ。
    - 診断ログとメトリック用の 1 つのストレージ アカウント。
        
この管理対象リソース グループ内のリソースと、実際のデータ ストレージである個々のディスクに対して請求されます。 たとえば、1 つの P30 ディスクが追加されたディスク プールがある場合、P30 ディスクと、管理対象リソース グループにデプロイされたすべてのリソースに対して請求されます。 これらのリソースと自分のディスク以外のものに、ディスク プールに関する追加のサービス料金はかかりません。 管理対象リソース グループの詳細については、「[しくみ](#how-it-works)」セクションを参照してください。

自分のディスク プールのコストを評価するための、VM とディスクのリージョン別の価格については、[Azure 料金計算ツール](https://azure.microsoft.com/pricing/calculator/)を参照してください。 ディスク プールによって消費される Azure リソースは、Azure Reservation (お持ちの場合) で考慮でされる場合があります。


## <a name="next-steps"></a>次のステップ

[ディスク プール計画ガイド](disks-pools-planning.md)に関する記事を参照してください。
