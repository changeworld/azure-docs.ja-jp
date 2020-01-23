---
title: Azure Migrate での物理サーバーの評価のサポート
description: Azure Migrate での物理サーバーの評価のサポートについて説明します。
ms.topic: conceptual
ms.date: 01/08/2020
ms.openlocfilehash: 32080605217cde78bd648ca6192f73d1025dea4c
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/15/2020
ms.locfileid: "76028763"
---
# <a name="support-matrix-for-physical-server-assessment"></a>物理サーバーの評価のサポート マトリックス 

[Azure Migrate サービス](migrate-overview.md)を使用すると、コンピューターを評価したり、Microsoft Azure クラウドに移行したりできます。 この記事では、オンプレミスの物理サーバーを評価して移行する際にサポートされる設定と制限について概要を説明します。


## <a name="overview"></a>概要

この記事を使用して オンプレミス マシンの Azure への移行を評価するには、Azure Migrate: Server Assessment ツールを Azure Migrate プロジェクトに追加します。 [Azure Migrate アプライアンス](migrate-appliance.md)をデプロイします。 アプライアンスは、オンプレミス マシンを継続的に検出し、構成データとパフォーマンス データを Azure に送信します。 マシンの検出後、検出されたマシンをグループにまとめ、グループの評価を実行します

## <a name="limitations"></a>制限事項

**サポート** | **詳細**
--- | ---
**評価の上限**| 1 つの[プロジェクト](migrate-support-matrix.md#azure-migrate-projects)で最大 35,000 個の物理サーバーを検出して評価します。
**プロジェクトの制限** | 1 つの Azure サブスクリプションで複数のプロジェクトを作成できます。 評価の上限に達するまで、1 つのプロジェクトに VMware VM、Hyper-V VM、および物理サーバーを含めることができます。
**検出** | Azure Migrate アプライアンスでは、最大 250 台の物理サーバーを検出できます。
**評価** | 1 つのグループに最大 35,000 個のマシンを追加できます。<br/><br/> 1 回の評価で最大 35,000 個のマシンを評価できます。

評価の詳細については[こちら](concepts-assessment-calculation.md)をご覧ください。




## <a name="physical-server-requirements"></a>物理サーバーの要件

| **サポート**                | **詳細**               
| :-------------------       | :------------------- |
| **物理サーバーの展開**       | 物理サーバーは、スタンドアロンにすることも、クラスターにデプロイすることもできます。 |
| **アクセス許可**           | **Windows:** 検出に含めるすべての Windows サーバー上にローカル ユーザー アカウントを設定します。 ユーザー アカウントは、これらのグループ (リモート デスクトップ ユーザー、パフォーマンス モニター ユーザー、パフォーマンス ログ ユーザー) に追加する必要があります。 <br/> **Linux:** 検出する Linux サーバーのルート アカウントが必要です。 |
| **オペレーティング システム** | 次を除くすべての [Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) および [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) オペレーティング システムがサポートされています。<br/> Windows Server 2003 <br/> SUSE Linux|


## <a name="azure-migrate-appliance-requirements"></a>Azure Migrate アプライアンスの要件

Azure Migrate では、[Azure Migrate アプライアンス](migrate-appliance.md)を使用して検出と評価を行います。 物理サーバーのアプライアンスは、VM または物理マシン上で実行できます。 これは、Azure portal からダウンロードした PowerShell スクリプトを使用して設定します。

- 物理サーバーの[アプライアンスの要件](migrate-appliance.md#appliance---physical)を確認します。
- アプライアンスがアクセスする必要のある [URL](migrate-appliance.md#url-access) を確認します。

## <a name="port-access"></a>ポート アクセス

次の表は、評価のためのポート要件をまとめたものです。

**[デバイス]** | **[接続]**
--- | ---
**アプライアンス** | TCP ポート 3389 で、アプライアンスへのリモート デスクトップ接続を許可するための受信接続。<br/> ポート 44368 で、次の URL を使用してアプライアンス管理アプリにリモートでアクセスするためのインバウンド接続: ``` https://<appliance-ip-or-name>:44368 ```<br/> ポート 443、5671、5672 で検出とパフォーマンスのメタデータを Azure Migrate に送信するためのアウトバウンド接続。
**物理サーバー** | **Windows:** ポート 443、WinRM ポート 5985 (HTTP) および 5986 (HTTPS) で Windows サーバーから構成とパフォーマンスのメタデータをプルするための受信接続。 <br/> **Linux:** ポート 22 (UDP) で Linux サーバーから構成とパフォーマンスのメタデータをプルするための受信接続。 |


## <a name="next-steps"></a>次のステップ

[物理サーバーの評価を準備します](tutorial-prepare-physical.md)。
