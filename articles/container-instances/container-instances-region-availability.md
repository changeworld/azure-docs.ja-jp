---
title: "Azure Container Instances リージョンとリソースの可用性"
description: "Azure リージョンがどのコンテナー インスタンスのデプロイと、インスタンスの CPU およびメモリ制限をサポートしているかを検出します。"
services: container-instances
author: mmacy
manager: timlt
ms.service: container-instances
ms.topic: overview
ms.date: 12/15/2017
ms.author: marsma
ms.openlocfilehash: ec7f469c47924f4ae22d6509996ca9cf498fc9ad
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/16/2017
---
# <a name="region-availability-for-azure-container-instances"></a>Azure Container Instances のリージョンの可用性

プレビュー時は、指定された CPU とメモリ制限を備えた以下のリージョンで、Azure Container Instances を利用できます。

| 場所 | OS | CPU | メモリ (GB) |
| -------- | -- | :---: | :-----------: |
| 西ヨーロッパ、米国西部、米国東部 | Linux | 4 | 14 |
| 西ヨーロッパ、米国西部、米国東部 | Windows | 4 | 14 |

## <a name="resource-availability"></a>リソースの可用性

これらのリソース制限内で作成されたコンテナー インスタンスは、デプロイ リージョン内での利用の対象になります。 リージョンでの負荷が高い場合、インスタンスのデプロイ時に失敗することがあります。

このようなデプロイの失敗を減らすために、より低い CPU とメモリの設定でインスタンスをデプロイしてみるか、デプロイを後で試してみてください。

## <a name="next-steps"></a>次の手順

コンテナー インスタンスのデプロイに関するトラブルシューティングの詳細については、「[Azure Container Instances でのデプロイに関する問題をトラブルシューティングする](container-instances-troubleshooting.md)」をご覧ください。