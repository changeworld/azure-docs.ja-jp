---
title: Azure Arc 対応サーバー用 Azure Automanage
description: Azure Arc 対応サーバー用 Azure Automanage の詳細
ms.service: virtual-machines
ms.subservice: automanage
ms.collection: linux
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 06/24/2021
ms.openlocfilehash: 42800401334da2f348cee6796e773c1ed0c0f8ff
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131451162"
---
# <a name="azure-automanage-for-machines-best-practices---azure-arc-enabled-servers"></a>Azure Automanage for Machines のベスト プラクティス - Azure Arc 対応サーバー

Azure Arc 対応サーバー VM で Automanage Machine Best Practices を使用する場合、これらの Azure サービスは自動的にオンボードされます。 それらはベスト プラクティスのホワイト ペーパーに不可欠なものであり、[クラウド導入フレームワーク](/azure/cloud-adoption-framework/manage/azure-server-management)を参照してください。

これらのすべてのサービスについては、Microsoft が自動オンボード、自動構成、ドリフトの監視、ドリフトが検出された場合の修復を行います。 詳細については、[Azure Automanage for virtual machines](automanage-virtual-machines.md) に移動します。

## <a name="supported-operating-systems"></a>サポートされるオペレーティング システム

Automanage では、Azure Arc 対応サーバーで次のオペレーティング システムがサポートされます

- Windows Server 2012/R2
- Windows Server 2016
- Windows Server 2019
- CentOS 7.3+、8
- RHEL 7.4+、8
- Ubuntu 16.04 および 18.04
- SLES 12 (SP3 から SP5 のみ)

## <a name="participating-services"></a>対象となるサービス

|サービス    |説明    |構成プロファイル<sup>1</sup>    |
|-----------|---------------|----------------------|
|[Machines Insights Monitoring](../azure-monitor/vm/vminsights-overview.md)    |Azure Monitor for machines では、実行中のプロセスや他のリソースへの依存関係など、仮想マシンのパフォーマンスと正常性が監視されます。    |Production    |
|[更新管理](../automation/update-management/overview.md)    |Azure Automation の Update Management を使用して、マシンのオペレーティング システムの更新プログラムを管理することができます。 すべてのエージェント マシンで利用可能な更新プログラムの状態をすばやく評価し、サーバーに必要な更新プログラムをインストールするプロセスを管理できます。    |運用、Dev/Test    |
|[変更履歴とインベントリ](../automation/change-tracking/overview.md) |変更履歴とインベントリでは、変更履歴とインベントリの機能を組み合わせて、仮想マシンとサーバー インフラストラクチャの変更を追跡できます。 このサービスを利用すれば、環境内のサービス、デーモン、ソフトウェア、レジストリ、ファイル全体にわたって変更を追跡し、不要な変更を診断したりアラートを生成したりすることができます。 インベントリのサポートにより、ゲスト リソースでクエリを実行して、インストール済みのアプリケーションやその他の構成アイテムを可視化できます。    |運用、Dev/Test    |
|[Azure ゲスト構成](../governance/policy/concepts/guest-configuration.md)  | ゲスト構成ポリシーは、マシンのコンプライアンスに関するレポートを作成し、構成を監視するために使用されます。 ゲスト構成拡張機能を使用して、Automanage サービスによって Azure Linux ベースラインがインストールされます。 Linux マシンの場合は、ゲスト構成サービスによって、ベースラインが監査専用モードでインストールされます。 ご利用の VM がベースラインに準拠していない場所を確認することはできますが、非準拠が自動的に修復されることはありません。    |運用、Dev/Test    |
|[Azure Automation アカウント](../automation/automation-create-standalone-account.md)    |Azure Automation は、インフラストラクチャとアプリケーションのライフサイクル全体にわたる管理をサポートします。    |運用、Dev/Test    |
|[Log Analytics ワークスペース](../azure-monitor/logs/log-analytics-overview.md) |Azure Monitor では、ログ データが Log Analytics ワークスペースに格納されます。Log Analytics ワークスペースは Azure リソースであり、データが収集、集計され、管理境界として機能するコンテナーです。    |運用、Dev/Test    |


<sup>1</sup> Automanage を有効にしている場合は、[構成プロファイル](automanage-virtual-machines.md#configuration-profile)の選択肢を使用できます。 必要な一連の Azure サービスと設定を使って、独自のカスタム プロファイルを作成することもできます。


## <a name="next-steps"></a>次の手順

Azure portal で Automanage for machines を有効にしてみてください。

> [!div class="nextstepaction"]
> [Azure portal で Azure Automanage for machines を有効にする](quick-create-virtual-machines-portal.md)
