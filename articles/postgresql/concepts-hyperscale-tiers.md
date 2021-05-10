---
title: Basic レベル プレビュー - Hyperscale (Citus) - Azure Database for PostgreSQL
description: Azure Database for PostgreSQL - Hyperscale (Citus) の単一ノードの Basic レベル
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 04/07/2021
ms.openlocfilehash: c8cad5eb0983715a7cc7c18249243e93a2c498d7
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/07/2021
ms.locfileid: "107023899"
---
# <a name="basic-tier-preview"></a>Basic レベル (プレビュー)

> [!IMPORTANT]
> Hyperscale (Citus) Basic レベルは現在プレビュー段階です。  このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。
>
> その他の新機能については、[Hyperscale (Citus) のプレビュー機能](hyperscale-preview-features.md)に関するページで全一覧をご覧いただけます。

Azure Database for PostgreSQL - Hyperscale (Citus) の Basic レベルを使用すると、後でスケーリングできる小規模なサーバー グループを簡単に作成できます。 Standard レベルのサーバー グループには、コーディネーター ノードと少なくとも 2 つのワーカー ノードがありますが、Basic レベルの場合は、1 つのデータベース ノードですべてが実行されます。

使用するノードが少ないことを除けば、Basic レベルは Standard レベルの機能をすべて備えています。 Standard レベルと同様に、高可用性、読み取りレプリカ、単票形式のテーブル ストレージなどの機能をサポートしています。

## <a name="choosing-basic-vs-standard-tier"></a>Basic レベルと Standard レベルの選択

Basic レベルは、初期の開発、テスト、継続的インテグレーションに適した経済的で便利なデプロイ オプションとなります。 1 つのデータベース ノードが使用され、Standard レベルと同じ SQL API が用意されています。 Basic レベルでアプリケーションをテストし、後で [Standard レベルに移行](howto-hyperscale-scale-grow.md#add-worker-nodes)して、インターフェイスが同じままであることを確認できます。

Basic レベルは、(プレビューから一般提供に移行した後の) 運用環境の小規模なワークロードにも適しています。 サーバーの仮想コア数を増やすことで、Basic レベル "*内で*" 垂直方向にスケーリングすることができます。

より大きなスケールがすぐに必要な場合は、Standard レベルを使用してください。 許容される最小限のサーバー グループには、1 つのコーディネーター ノードと 2 つのワーカーが含まれます。 [初期サイズ設定](howto-hyperscale-scale-initial.md)方法に関するページで説明されているように、ユースケースに基づいてより多くのノードの使用を選択できます。

## <a name="next-steps"></a>次のステップ

* [Basic レベルをプロビジョニングする](quickstart-create-hyperscale-basic-tier.md)方法を確認します
* 準備ができたら、Basic レベルから Standard レベルへの[移行方法](howto-hyperscale-scale-grow.md#add-worker-nodes)に関するページを参照してください。
* [単票形式のストレージ](concepts-hyperscale-columnar.md)オプションは、Basic レベルと Standard レベルの両方で使用できます
