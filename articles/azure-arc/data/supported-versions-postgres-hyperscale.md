---
title: Azure Arc 対応 PostgreSQL Hyperscale でサポートされている Postgres のバージョン
description: Azure Arc 対応 PostgreSQL Hyperscale でサポートされている Postgres のバージョン
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 11/03/2021
ms.topic: how-to
ms.openlocfilehash: 180cb275b2b4a08dc9463eef4432006c5c0ee714
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132290611"
---
# <a name="supported-versions-of-postgres-with-azure-arc-enabled-postgresql-hyperscale"></a>Azure Arc 対応 PostgreSQL Hyperscale でサポートされている Postgres のバージョン
サポートされるバージョンの一覧は、Azure PaaS の Postgres マネージド サービスとの一致を確保するため、時間の経過と共に進化していきます。 現在、サポートされているメジャー バージョンは次のとおりです。
- Postgres 12 (既定)
- Postgres 11


[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="how-to-chose-between-versions"></a>バージョンを選択する方法
アプリケーションが設計されているバージョンと、各バージョンの機能を確認することをお勧めします。 詳細については、公式の Postgres サイトの各バージョンに関するページを参照してください。
- [Postgres 12 (既定)](https://www.postgresql.org/docs/12/index.html)
- [Postgres 11](https://www.postgresql.org/docs/11/index.html)

## <a name="how-to-create-a-particular-version-in-azure-arc-enabled-postgresql-hyperscale"></a>Azure Arc 対応 PostgreSQL Hyperscale で特定のバージョンを作成する方法
作成時に、 _--engine-version_ パラメーターを渡すことによって、作成するバージョンを指定できます。 バージョン情報が指定されていない場合、既定では Postgres バージョン 12 のサーバー グループが作成されます。

なお、サポートされているバージョンに関係なく、Kubernetes クラスター内の Postgres Custom Resource Definition (CRD) は 1 つしかありません。
たとえば、次のコマンドを実行します。
```console
kubectl get crds
```

次のような出力が返されます。
```console
NAME                                                             CREATED AT
dags.sql.arcdata.microsoft.com                                   2021-10-12T23:53:40Z
datacontrollers.arcdata.microsoft.com                            2021-10-13T01:00:27Z
exporttasks.tasks.arcdata.microsoft.com                          2021-10-12T23:53:39Z
healthstates.azmon.container.insights                            2021-10-12T19:04:44Z
monitors.arcdata.microsoft.com                                   2021-10-13T01:00:26Z
postgresqls.arcdata.microsoft.com                                2021-10-12T23:53:37Z
sqlmanagedinstancerestoretasks.tasks.sql.arcdata.microsoft.com   2021-10-12T23:53:38Z
sqlmanagedinstances.sql.arcdata.microsoft.com                    2021-10-12T23:53:37Z
```

この例では、この出力は Postgres に関連する 2 種類の CRD、postgresqls.arcdata.microsoft.com と shortname postgresqls があることを示しています。 この CRD は、Postgres インスタンスでも Postgres サーバー グループでもありません。 CRD の存在は、そのサーバー グループが作成されたものであるかどうかを示すものではありません。 CRD は、Kubernetes クラスターにどのような種類のリソースを作成できるかを示すものです。

## <a name="how-can-i-be-notified-when-other-versions-are-available"></a>他のバージョンが利用可能になったときに通知を受け取る方法
この記事に戻ってきてください。 必要に応じて更新されます。


## <a name="next-steps"></a>次のステップ:
- [Azure Arc 対応 PostgreSQL Hyperscale の作成について確認する](create-postgresql-hyperscale-server-group.md)
- [Arc データ コントローラーに作成された Azure Arc 対応 PostgreSQL Hyperscale サーバー グループの一覧を取得する方法について確認する](list-server-groups-postgres-hyperscale.md)
