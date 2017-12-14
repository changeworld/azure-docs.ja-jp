---
title: "Azure Container Instances リージョンとリソースの可用性"
description: "Azure リージョンがどのコンテナー インスタンスのデプロイと、インスタンスの CPU およびメモリ制限をサポートしているかを検出します。"
services: container-instances
author: mmacy
manager: timlt
ms.service: container-instances
ms.topic: overview
ms.date: 08/31/2017
ms.author: marsma
ms.openlocfilehash: ace4eb6b284f2c1b2caeb54c1d686e68cacb1725
ms.sourcegitcommit: a48e503fce6d51c7915dd23b4de14a91dd0337d8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/05/2017
---
# <a name="region-availability-for-azure-container-instances"></a>Azure Container Instances のリージョンの可用性

プレビュー時は、指定された CPU とメモリ制限を備えた以下のリージョンで、Azure Container Instances を利用できます。

| 場所 | OS | CPU | メモリ (GB) |
| -------- | -- | :---: | :-----------: |
| 西ヨーロッパ、米国西部、米国東部 | Linux | 2 | 7 |
| 西ヨーロッパ、米国西部、米国東部 | Windows | 2 | 3.5 |

## <a name="resource-availability"></a>リソースの可用性

これらのリソース制限内で作成されたコンテナー インスタンスは、デプロイ リージョン内での利用の対象になります。 リージョンでの負荷が高い場合、インスタンスのデプロイ時に失敗することがあります。

このようなデプロイの失敗を減らすために、より低い CPU とメモリの設定でインスタンスをデプロイしてみるか、デプロイを後で試してみてください。

## <a name="next-steps"></a>次のステップ

コンテナー インスタンスのデプロイに関するトラブルシューティングの詳細については、「[Azure Container Instances でのデプロイに関する問題をトラブルシューティングする](container-instances-troubleshooting.md)」をご覧ください。