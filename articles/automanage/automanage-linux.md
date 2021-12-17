---
title: Azure Automanage for Linux
description: Linux マシン用に自動的にオンボードされて構成されるサービスに対する、Azure Automanage for virtual machines のベスト プラクティスについて学習します。
author: deanwe
ms.service: virtual-machines
ms.subservice: automanage
ms.collection: linux
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 02/22/2021
ms.author: deanwe
ms.openlocfilehash: 8fe7d56236281c10075a3b0526dd7014e36b0652
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131435550"
---
# <a name="azure-automanage-for-machines-best-practices---linux"></a>Azure Automanage for Machines Best Practices - Linux

Linux VM で Automanage Machine Best Practices プロファイルを使用する場合、これらの Azure サービスは自動的にオンボードされます。 それらはベスト プラクティスのホワイト ペーパーに不可欠なものであり、[クラウド導入フレームワーク](/azure/cloud-adoption-framework/manage/azure-server-management)を参照してください。

これらのすべてのサービスについては、Microsoft が自動オンボード、自動構成、ドリフトの監視、ドリフトが検出された場合の修復を行います。 詳細については、[Azure Automanage for virtual machines](automanage-virtual-machines.md) に関する記事を参照してください。

## <a name="supported-linux-distributions-and-versions"></a>サポートされている Linux ディストリビューションとバージョン

Automanage によって、次の Linux ディストリビューションとバージョンがサポートされています。

- CentOS 7.3+、8
- RHEL 7.4+、8
- Ubuntu 16.04 および 18.04
- SLES 12 (SP3 から SP5 のみ)

## <a name="participating-services"></a>対象となるサービス

>[!NOTE]
> 現時点では、Linux マシンでは Microsoft Antimalware はサポートされていません。

|サービス    |説明    |サポートされる構成プロファイル<sup>1</sup>|
|-----------|---------------|----------------------|
|[Machines Insights Monitoring](../azure-monitor/vm/vminsights-overview.md)    |Azure Monitor for machines では、実行中のプロセスや他のリソースへの依存関係など、仮想マシンのパフォーマンスと正常性が監視されます。 [詳細情報](../azure-monitor/vm/vminsights-overview.md)。    |Production    |
|[Backup](../backup/backup-overview.md)   |Azure Backup では、VM 上のデータが誤って破壊されることを防ぐために、独立して分離されたバックアップを提供しています。 [詳細情報](../backup/backup-azure-vms-introduction.md)。 料金は、保護対象の VM の数とサイズに基づいています。 [詳細情報](https://azure.microsoft.com/pricing/details/backup/)。    |Production    |
|[Azure Security Center](../security-center/security-center-introduction.md)    |Azure Security Center は統合されたインフラストラクチャ セキュリティ管理システムであり、データ センターのセキュリティ体制を強化し、クラウド内のハイブリッド ワークロード全体にわたる高度な脅威保護が提供されます。 [詳細情報](../security-center/security-center-introduction.md)。  VM が存在するサブスクリプションは、Automanage によって、Azure Security Center の Free レベルのオファリングに構成されます。 ご利用のサブスクリプションが Azure Security Center に既にオンボードされている場合、Automanage によって再構成されることはありません。    |運用、Dev/Test    |
|[更新管理](../automation/update-management/overview.md)    |Azure Automation の Update Management を使用して、マシンのオペレーティング システムの更新プログラムを管理することができます。 すべてのエージェント マシンで利用可能な更新プログラムの状態をすばやく評価し、サーバーに必要な更新プログラムをインストールするプロセスを管理できます。 [詳細情報](../automation/update-management/overview.md)。    |運用、Dev/Test    |
|[変更履歴とインベントリ](../automation/change-tracking/overview.md) |変更履歴とインベントリでは、変更履歴とインベントリの機能を組み合わせて、仮想マシンとサーバー インフラストラクチャの変更を追跡できます。 このサービスを利用すれば、環境内のサービス、デーモン、ソフトウェア、レジストリ、ファイル全体にわたって変更を追跡し、不要な変更を診断したりアラートを生成したりすることができます。 インベントリのサポートにより、ゲスト リソースでクエリを実行して、インストール済みのアプリケーションやその他の構成アイテムを可視化できます。  [詳細情報](../automation/change-tracking/overview.md)。    |運用、Dev/Test    |
|[ゲスト構成](../governance/policy/concepts/guest-configuration.md)  | ゲスト構成は、マシンのコンプライアンスに関するレポートを作成し、構成を監視するために使用されます。 ゲスト構成拡張機能を使用して、Automanage サービスによって Azure Linux ベースラインがインストールされます。 Linux マシンの場合は、ゲスト構成サービスによって、ベースラインが監査専用モードでインストールされます。 ご利用の VM がベースラインに準拠していない場所を確認することはできますが、非準拠が自動的に修復されることはありません。 [詳細情報](../governance/policy/concepts/guest-configuration.md)。    |運用、Dev/Test    |
|[ブート診断](../virtual-machines/boot-diagnostics.md)  | ブート診断は、VM ブート エラーの診断を可能にする、Azure の仮想マシン (VM) のデバッグ機能です。 ブート診断を使用すると、ユーザーは、シリアル ログ情報とスクリーンショットを収集して、起動中の VM の状態を確認できます。 これは、マネージド ディスクを使用しているマシンに対してのみ有効になります。 |運用、Dev/Test    |
|[Azure Automation アカウント](../automation/automation-create-standalone-account.md)    |Azure Automation は、インフラストラクチャとアプリケーションのライフサイクル全体にわたる管理をサポートします。 [詳細情報](../automation/automation-intro.md)。    |運用、Dev/Test    |
|[Log Analytics ワークスペース](../azure-monitor/logs/log-analytics-overview.md) |Azure Monitor では、ログ データが Log Analytics ワークスペースに格納されます。Log Analytics ワークスペースは Azure リソースであり、データが収集、集計され、管理境界として機能するコンテナーです。 [詳細情報](../azure-monitor/logs/design-logs-deployment.md)。    |運用、Dev/Test    |


<sup>1</sup> Automanage を有効にしている場合は、構成プロファイルの選択肢を使用できます。 [詳細情報](automanage-virtual-machines.md#configuration-profile)。 必要な一連の Azure サービスと設定を含む独自のカスタム プロファイルを作成することもできます。


## <a name="next-steps"></a>次の手順

Azure portal で Automanage for machines を有効にしてみてください。

> [!div class="nextstepaction"]
> [Azure portal で Azure Automanage for machines を有効にする](quick-create-virtual-machines-portal.md)