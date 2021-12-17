---
title: Windows Server 用 Azure Automanage
description: Azure Automanage for virtual machines で、Windows Server マシン用に自動的にオンボードおよび構成されるサービスのベスト プラクティスについて説明します。
author: deanwe
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 02/22/2021
ms.author: deanwe
ms.openlocfilehash: 72ef8193215d3dc35f576a96a005bde8bf40e49f
ms.sourcegitcommit: 901ea2c2e12c5ed009f642ae8021e27d64d6741e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/12/2021
ms.locfileid: "132371944"
---
# <a name="azure-automanage-for-machines-best-practices---windows-server"></a>Azure Automanage for Machines のベスト プラクティス - Windows Server

Windows Server VM で Automanage Machine Best Practices を使用する場合、これらの Azure サービスは自動的にオンボードされます。 それらはベスト プラクティスのホワイト ペーパーに不可欠なものであり、[クラウド導入フレームワーク](/azure/cloud-adoption-framework/manage/azure-server-management)を参照してください。

これらのすべてのサービスについては、Microsoft が自動オンボード、自動構成、ドリフトの監視、ドリフトが検出された場合の修復を行います。 詳細については、[Azure Automanage for virtual machines](automanage-virtual-machines.md) に移動します。

## <a name="supported-windows-server-versions"></a>サポートされている Windows Server のバージョン

Automanage でサポートされている Windows Server のバージョンは、次のとおりです。

- Windows Server 2012/R2
- Windows Server 2016
- Windows Server 2019
- Windows Server 2022
- Windows Server 2022 Azure Edition

## <a name="participating-services"></a>対象となるサービス

|サービス    |説明    |構成プロファイル<sup>1</sup>    |
|-----------|---------------|----------------------|
|[Machines Insights Monitoring](../azure-monitor/vm/vminsights-overview.md)    |Azure Monitor for Machines では、実行中のプロセスや他のリソースへの依存関係など、仮想マシンのパフォーマンスと正常性が監視されます。    |Production    |
|[Backup](../backup/backup-overview.md)    |Azure Backup では、マシン上のデータが誤って破壊されることを防ぐために、独立して分離されたバックアップを提供しています。 料金は、保護対象の VM の数とサイズに基づいています。    |Production    |
|[Microsoft Defender for Cloud](../defender-for-cloud/defender-for-cloud-introduction.md)    |Microsoft Defender for Cloud は統合されたインフラストラクチャ セキュリティ管理システムであり、データ センターのセキュリティ体制を強化し、クラウド内のハイブリッド ワークロード全体に高度な脅威保護を提供します。  VM が存在するサブスクリプションは、Automanage によって、Microsoft Defender for Cloud の Free レベルのオファリングに合わせて構成されます。 ご利用のサブスクリプションが Microsoft Defender for Cloud に既にオンボードされている場合、Automanage によって再構成されることはありません。    |運用、Dev/Test    |
|[Microsoft Antimalware](../security/fundamentals/antimalware.md)    |Azure に対する Microsoft マルウェア対策は、ウイルス、スパイウェアなどの悪意のあるソフトウェアの特定や駆除に役立つリアルタイムの保護です。 これにより、既知の悪意あるソフトウェアや望ましくないソフトウェアが Azure システム上にソフトウェア自体をインストールまたは実行しようとしたときに、アラートが生成されます。 **注:** Microsoft Antimalware を使用するには、他のマルウェア対策ソフトウェアがインストールされていないことが必要です。そうでないと、動作しない可能性があります。 |運用、Dev/Test    |
|[更新管理](../automation/update-management/overview.md)    |Azure Automation の Update Management を使用して、マシンのオペレーティング システムの更新プログラムを管理することができます。 すべてのエージェント マシンで利用可能な更新プログラムの状態をすばやく評価し、サーバーに必要な更新プログラムをインストールするプロセスを管理できます。    |運用、Dev/Test    |
|[変更履歴とインベントリ](../automation/change-tracking/overview.md) |変更履歴とインベントリでは、変更履歴とインベントリの機能を組み合わせて、仮想マシンとサーバー インフラストラクチャの変更を追跡できます。 このサービスを利用すれば、環境内のサービス、デーモン、ソフトウェア、レジストリ、ファイル全体にわたって変更を追跡し、不要な変更を診断したりアラートを生成したりすることができます。 インベントリのサポートにより、ゲスト リソースでクエリを実行して、インストール済みのアプリケーションやその他の構成アイテムを可視化できます。    |運用、Dev/Test    |
|[ゲスト構成](../governance/policy/concepts/guest-configuration.md) | ゲスト構成ポリシーは、マシンのコンプライアンスに関するレポートを作成し、構成を監視するために使用されます。 ゲスト構成拡張機能を使用して、Automanage サービスによって [Windows セキュリティ ベースライン](/windows/security/threat-protection/windows-security-baselines)がインストールされます。 Windows マシンの場合、準拠していない場合はゲスト構成サービスによってベースライン設定が自動的に再適用されます。    |運用、Dev/Test    |
|[ブート診断](../virtual-machines/boot-diagnostics.md)    | ブート診断は、VM ブート エラーの診断を可能にする、Azure の仮想マシン (VM) のデバッグ機能です。 ブート診断を使用すると、ユーザーは、シリアル ログ情報とスクリーンショットを収集して、起動中の VM の状態を確認できます。 これは、マネージド ディスクを使用しているマシンに対してのみ有効になります。 |運用、Dev/Test    |
|[Windows Admin Center](/windows-server/manage/windows-admin-center/azure/manage-vm)    | Azure portal の Windows Admin Center (プレビュー) を使用して、Azure VM 内の Windows Server オペレーティング システムを管理します。 これは、Windows Server 2016 以上を使用するマシンでのみサポートされます。 Automanage は、プライベート IP アドレスを介して Windows Admin Center を構成します。 パブリック IP アドレス経由で Windows Admin Center に接続する場合は、ポート 6516 の受信ポート ルールを開いてください。 既定では、Automanage は Dev/Test プロファイルの Windows Admin Center をオンボードします。 運用環境と Dev/Test 環境で Windows Admin Center を有効または無効にするには、基本設定を使用します。 |運用、Dev/Test    |
|[Azure Automation アカウント](../automation/automation-create-standalone-account.md)    |Azure Automation は、インフラストラクチャとアプリケーションのライフサイクル全体にわたる管理をサポートします。    |運用、Dev/Test    |
|[Log Analytics ワークスペース](../azure-monitor/logs/log-analytics-overview.md) |Azure Monitor では、ログ データが Log Analytics ワークスペースに格納されます。Log Analytics ワークスペースは Azure リソースであり、データが収集、集計され、管理境界として機能するコンテナーです。    |運用、Dev/Test    |


<sup>1</sup> Automanage を有効にしている場合は、[構成プロファイル](automanage-virtual-machines.md#configuration-profile)の選択肢を使用できます。 必要な一連の Azure サービスと設定を使用して、独自のカスタム プロファイルを作成することもできます。


## <a name="next-steps"></a>次の手順

Azure portal で Automanage for machines を有効にしてみてください。

> [!div class="nextstepaction"]
> [Azure portal で Azure Automanage for machines を有効にする](quick-create-virtual-machines-portal.md)
