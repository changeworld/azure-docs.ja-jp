---
title: Azure レンダリング - 参照アーキテクチャ
description: クラウドにバーストすることで Azure Batch およびその他の Azure サービスを使用してオンプレミス レンダー ファームを拡張するためのアーキテクチャ
services: batch
author: davefellows
manager: jeconnoc
ms.author: danlep
ms.date: 08/13/2018
ms.topic: conceptual
ms.openlocfilehash: 0fe101ee6eb88094034b90c4d39f06ba509c9512
ms.sourcegitcommit: 0fcd6e1d03e1df505cf6cb9e6069dc674e1de0be
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/14/2018
ms.locfileid: "40099686"
---
# <a name="reference-architectures-for-azure-rendering"></a>Azure レンダリング向け参照アーキテクチャ

この記事では、オンプレミス レンダー ファームを Azure に拡張、つまり "バースト" するためのアーキテクチャ概要の図を示します。 例は、Azure コンピューティング、ネットワーク、およびストレージ サービスで使用できるさまざまなオプションを示しています。

## <a name="hybrid-with-nfs-or-cfs"></a>NFS または CFS を持つハイブリッド

次の図は、以下の Azure サービスを含むハイブリッド シナリオを示しています。

* **コンピューティング** - Azure Batch プールまたは仮想マシン スケール セット。

* **ネットワーク** - オンプレミス: Azure ExpressRoute または VPN。 Azure: Azure VNet。

* **ストレージ** - 入力ファイルと出力ファイル:Azure VM を使用した NFS または CFS。Azure File Sync または RSync を介してオンプレミス ストレージと同期されています。

  ![クラウド バースティング - NFS または CFS を持つハイブリッド](./media/batch-rendering-architectures/hybrid-nfs-cfs.png)

## <a name="hybrid-with-blobfuse"></a>Blobfuse を持つハイブリッド

次の図は、以下の Azure サービスを含むハイブリッド シナリオを示しています。

* **コンピューティング** - Azure Batch プールまたは仮想マシン スケール セット。

* **ネットワーク** - オンプレミス: Azure ExpressRoute または VPN。 Azure: Azure VNet。

* **ストレージ** - 入力ファイルと出力ファイル: Blob Storage。Azure Blobfuse を介してコンピューティング リソースにマウントされています。

  ![クラウド バースティング - Blobfuse を持つハイブリッド](./media/batch-rendering-architectures/hybrid-blob-fuse.png)

## <a name="hybrid-compute-and-storage"></a>ハイブリッド コンピューティングとストレージ

次の図は、コンピューティングとストレージの両方を対象とした完全結合されたハイブリッド シナリオを示しており、次の Azure サービスが含まれています。

* **コンピューティング** - Azure Batch プールまたは仮想マシン スケール セット。

* **ネットワーク** - オンプレミス: Azure ExpressRoute または VPN。 Azure: Azure VNet。

* **ストレージ** - クロスプレミス: Avere vFXT。 (省略可能) Azure Data Box を介した Blob Storage へのオンプレミス ファイルのアーカイブ。

  ![クラウド バースティング - ハイブリッド コンピューティングとストレージ](./media/batch-rendering-architectures/hybrid-compute-storage.png)


## <a name="next-steps"></a>次の手順

* Azure Batch での[レンダー マネージャー](batch-rendering-render-managers.md)の使用について確認します。

* [Azure でのレンダリング](batch-rendering-service.md)用オプションを確認します。