---
title: Azure Migrate での Hyper-V の評価のサポート
description: Azure Migrate を使用した Hyper-V の評価のサポートについて説明します。
ms.topic: conceptual
ms.date: 01/08/2020
ms.openlocfilehash: 20bdbb16d2f0610f6519424141b09190eae3cc42
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/15/2020
ms.locfileid: "76028788"
---
# <a name="support-matrix-for-hyper-v-assessment"></a>Hyper-V の評価のサポート マトリックス

この記事では、[Azure Migrate: Server Assessment](migrate-services-overview.md#azure-migrate-server-assessment-tool) を使用して Hyper-V VM を評価するためのサポート設定と制限事項についてまとめています。 Hyper-V VM を Azure に移行する方法については、[移行のサポート マトリックス](migrate-support-matrix-hyper-v-migration.md)に関するページを参照してください。

## <a name="overview"></a>概要

この記事に従って Azure への移行についてオンプレミスのマシンを評価するには、Azure Migrate: Server Assessment ツールを Azure Migrate プロジェクトに追加します。 [Azure Migrate アプライアンス](migrate-appliance.md)をデプロイします。 このアプライアンスは、オンプレミスのマシンを継続的に検出し、構成およびパフォーマンス データを Azure に送信します。 マシンの検出後は、検出されたマシンをグループにまとめ、グループに対して評価を実行します。


## <a name="limitations"></a>制限事項

**サポート** | **詳細**
--- | ---
**評価の上限**| 1 つの[プロジェクト](migrate-support-matrix.md#azure-migrate-projects)で最大 35,000 台の Hyper-V VM を検出および評価します。
**プロジェクトの制限** | 1 つの Azure サブスクリプションで複数のプロジェクトを作成できます。 評価の上限に達するまで、1 つのプロジェクトに VMware VM、Hyper-V VM、および物理サーバーを含めることができます。
**検出** | Azure Migrate アプライアンスでは、最大 5,000 台の Hyper-V VM を検出できます。<br/><br/> アプライアンスは、最大 300 台の Hyper-V ホストに接続できます。
**評価** | 1 つのグループに最大 35,000 個のマシンを追加できます。<br/><br/> 1 回の評価で最大 35,000 個の VM を評価できます。

評価の詳細については[こちら](concepts-assessment-calculation.md)をご覧ください。



## <a name="hyper-v-host-requirements"></a>Hyper-V ホストの要件

| **サポート**                | **詳細**               
| :-------------------       | :------------------- |
| **ホストのデプロイ**       | Hyper-V ホストは、スタンドアロンにすることも、クラスターにデプロイすることもできます。 |
| **アクセス許可**           | Hyper-V ホストに対する管理者のアクセス許可が必要です。 <br/> 管理者のアクセス許可を割り当てたくない場合には、代わりにローカルまたはドメインのユーザー アカウントを作成し、そのユーザーを Remote Management Users、Hyper-V Administrators、Performance Monitor Users のグループに追加します。 |
| **ホスト オペレーティング システム** | Windows Server 2019、Windows Server 2016、または Windows Server 2012 R2。<br/> Windows Server 2012 を実行している Hyper-V ホスト上にある VM を評価することはできません。 |
| **PowerShell リモート処理**   | 各ホストで有効にする必要があります。 |
| **Hyper-V レプリカ**       | Hyper-V レプリカを使用する (または、同じ VM 識別子を持つ複数の VM がある) 場合、Azure Migrate を使用して元の VM とレプリケートされた VM の両方を検出すると、Azure Migrate によって生成される評価が正確ではない可能性があります。 |


## <a name="hyper-v-vm-requirements"></a>Hyper-V VM の要件

| **サポート**                  | **詳細**               
| :----------------------------- | :------------------- |
| **オペレーティング システム** | Azure でサポートされているすべての [Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) および [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) オペレーティング システム。 |
| **統合サービス**       | オペレーティング システム情報をキャプチャするには、評価する VM で [Hyper-V 統合サービス](https://docs.microsoft.com/virtualization/hyper-v-on-windows/reference/integration-services)が実行されている必要があります。 |


## <a name="azure-migrate-appliance-requirements"></a>Azure Migrate アプライアンスの要件

Azure Migrate では、[Azure Migrate アプライアンス](migrate-appliance.md)を使用して検出と評価を行います。 Hyper-V 用のアプライアンスは、Hyper-V VM 上で実行され、Azure portal からダウンロードした圧縮された Hyper-V VHD を使用してデプロイされます。 

- Hyper-V の[アプライアンスの要件](migrate-appliance.md#appliance---hyper-v)を確認する。
- アプライアンスがアクセスする必要のある [URL](migrate-appliance.md#url-access) を確認する。

## <a name="port-access"></a>ポート アクセス

次の表は、評価のためのポート要件をまとめたものです。

**[デバイス]** | **[接続]**
--- | ---
**アプライアンス** | TCP ポート 3389 で、アプライアンスへのリモート デスクトップ接続を許可するための受信接続。<br/> ポート 44368 で、次の URL を使用してアプライアンス管理アプリにリモートでアクセスするためのインバウンド接続: ``` https://<appliance-ip-or-name>:44368 ```<br/> ポート 443、5671、5672 で検出とパフォーマンスのメタデータを Azure Migrate に送信するためのアウトバウンド接続。
**Hyper-V ホスト/クラスター** | Common Information Model (CIM) セッションを使用し、WinRM ポート 5985 (HTTP) と 5986 (HTTPS) で、Hyper-V VM の構成とパフォーマンスのメタデータをプルするための受信接続。



## <a name="next-steps"></a>次のステップ

[Hyper-V VM の評価を準備する](tutorial-prepare-hyper-v.md)
