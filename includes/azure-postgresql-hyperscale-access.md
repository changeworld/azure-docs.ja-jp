---
title: インクルード ファイル
description: インクルード ファイル
author: jonels-msft
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: include
ms.date: 10/15/2021
ms.author: jonels
ms.custom: include file
ms.openlocfilehash: 4e0840dc3aa24f744eaac74b44b2d7972ff40900
ms.sourcegitcommit: 37cc33d25f2daea40b6158a8a56b08641bca0a43
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/15/2021
ms.locfileid: "130072190"
---
Azure Database for PostgreSQL - Hyperscale (Citus) は、3 つのネットワーク オプションをサポートします。

* アクセス権なし
    * これは、パブリックまたはプライベート アクセスが有効になっていない場合に、新しく作成されるサーバー グループの既定値です。 コンピューターなし - Azure の内部または外部で、データベース ノードに接続できます。
* パブリック アクセス
    * コーディネーター ノードには、パブリック IP アドレスが割り当てられます。
    * コーディネーターノードへのアクセスは、ファイアウォールによって保護されています。
    * 必要に応じて、すべてのワーカー ノードへのアクセスを有効にすることができます。 この場合、パブリック IP アドレスはワーカー ノードに割り当てられ、同じファイアウォールによって保護されます。
* プライベート アクセス (プレビュー)
    * サーバー グループのノードには、プライベート IP アドレスのみが割り当てられます。
    * 各ノードには、選択した仮想ネットワーク内のホストが Hyperscale (Citus) ノードにアクセスできるように、プライベート エンドポイントが必要です。
    * ネットワーク セキュリティ グループなどの Azure 仮想ネットワークのセキュリティ機能は、アクセス制御に使用できます。

Hyperscale (Citus) サーバー グループを作成する場合は、パブリックまたはプライベート アクセスを有効にするか、既定の [アクセスなし] を選択することができます。 サーバー グループを作成したら、パブリック アクセスとプライベート アクセスの切り替えを選択するか、両方とも一度にアクティブ化することができます。
