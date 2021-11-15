---
title: CLI を使用して間接モードの Azure Arc 対応 Managed Instance をアップグレードする
description: CLI を使用して間接モードの Azure Arc 対応 Managed Instance をアップグレードする
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: grrlgeek
ms.author: jeschult
ms.reviewer: mikeray
ms.date: 11/03/2021
ms.topic: how-to
ms.openlocfilehash: daff60736185f448183d24207099670884402795
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2021
ms.locfileid: "131565662"
---
# <a name="upgrade-an-indirect-mode-azure-arc-enabled-managed-instance-using-the-cli"></a>CLI を使用して間接モードの Azure Arc 対応 Managed Instance をアップグレードする

## <a name="prerequisites"></a>前提条件

### <a name="install-tools"></a>ツールをインストールする

この記事のタスクに進む前に、次をインストールする必要があります。

- [Azure CLI (az)](/cli/azure/install-azure-cli)
- [Azure CLI 用の `arcdata` 拡張機能](install-arcdata-extension.md)

## <a name="limitations"></a>制限事項

Managed Instance をアップグレードする前に、Azure Arc データ コントローラーを新しいバージョンにアップグレードする必要があります。

現時点では、一度にアップグレードできる Managed Instance は 1 つのみです。

## <a name="upgrade-the-managed-instance"></a>Managed Instance のアップグレード

最初にドライランを実行できます。 これにより、バージョン スキーマが検証され、アップグレードするインスタンスが一覧表示されます。

````cli
az sql mi-arc upgrade --name <instance name> --k8s-namespace <namespace> --dry-run --use-k8s
````

次のように出力されます。

```output
Preparing to upgrade sql sqlmi-1 in namespace arc to data controller version.
****Dry Run****1 instance(s) would be upgraded by this commandsqlmi-1 would be upgraded to 20211024.1.
```

### <a name="general-purpose"></a>General Purpose

Managed Instance をアップグレードするには、次のコマンドを使用します。

````cli
az sql mi-arc upgrade --name <instance name> --desired-version <version> --k8s-namespace <namespace> --use-k8s
````

例:

````cli
az sql mi-arc upgrade --name instance1 --target v1.0.0.20211028 --k8s-namespace arc1 --use-k8s
````

## <a name="monitor"></a>モニター

Kubectl または CLI を使用して、アップグレードの進行状況を監視できます。

### <a name="kubectl"></a>kubectl

```console
kubectl describe sqlmi --namespace <namespace>
```

### <a name="cli"></a>CLI

```cli
az sql mi-arc show --name <instance name> --k8s-namespace <namespace> --use-k8s
```

### <a name="output"></a>出力

コマンドの出力に、リソース情報が表示されます。 状態にアップグレード情報が示されます。

アップグレード中は、```State``` に ```Updating``` が表示されます。```Running Version``` は現在のバージョンになります。

```output
Status:
  Log Search Dashboard:  https://30.88.222.48:5601/app/kibana#/discover?_a=(query:(language:kuery,query:'custom_resource_name:sqlmi-1'))
  Metrics Dashboard:     https://30.88.221.32:3000/d/40q72HnGk/sql-managed-instance-metrics?var-hostname=sqlmi-1-0
  Observed Generation:   2
  Primary Endpoint:      30.76.129.38,1433
  Ready Replicas:        1/1
  Running Version:       v1.0.0_2021-07-30
  State:                 Updating
```

アップグレードが完了すると、```State``` に ```Ready``` が表示されます。```Running Version``` は新しいバージョンになります。

```output
Status:
  Log Search Dashboard:  https://30.88.222.48:5601/app/kibana#/discover?_a=(query:(language:kuery,query:'custom_resource_name:sqlmi-1'))
  Metrics Dashboard:     https://30.88.221.32:3000/d/40q72HnGk/sql-managed-instance-metrics?var-hostname=sqlmi-1-0
  Observed Generation:   2
  Primary Endpoint:      30.76.129.38,1433
  Ready Replicas:        1/1
  Running Version:       20211024.1
  State:                 Ready
```

## <a name="troubleshoot-upgrade-problems"></a>アップグレードに関する問題のトラブルシューティング

アップグレードで問題が発生した場合は、「[トラブルシューティング ガイド](troubleshoot-guide.md)」を参照してください。
