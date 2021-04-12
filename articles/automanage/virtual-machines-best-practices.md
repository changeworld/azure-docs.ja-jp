---
title: Azure Automanage for virtual machines のベスト プラクティス
description: Azure Automanage for virtual machines で自動的にオンボードおよび構成されるサービスのベスト プラクティスについて説明します。
author: deanwe
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 09/04/2020
ms.author: deanwe
ms.openlocfilehash: 5cdc0080c0d8b06bd0b7e19566b54b4f105648e5
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102122104"
---
# <a name="azure-automanage-for-virtual-machines-best-practices"></a>Azure Automanage for virtual machines のベスト プラクティス


Automanage for virtual machines を使用すると、このような Azure サービスは自動的にオンボードされます。 これらはベスト プラクティスのホワイト ペーパーに不可欠なものです。[クラウド導入フレームワーク](/azure/cloud-adoption-framework/manage/azure-server-management)に関するページを参照してください。

これらサービスすべてについて、自動オンボード、自動構成、ドリフトの監視、ドリフトが検出された場合の修正が行われます。 このプロセスの詳細については、「[Azure Automanage for virtual machines](automanage-virtual-machines.md)」を参照してください。


## <a name="participating-services"></a>対象となるサービス

|サービス    |説明    |サポートされているプロファイル<sup>1</sup>    |サポートされている基本設定<sup>1</sup>    |
|-----------|---------------|----------------------|-------------------------|
|VM の分析情報の監視    |Azure Monitor for VMs では、実行中のプロセスや他のリソースへの依存関係など、仮想マシンのパフォーマンスと正常性が監視されます。 [詳細情報](../azure-monitor/vm/vminsights-overview.md)。    |Azure VM のベスト プラクティス - 運用    |いいえ    |
|バックアップ    |Azure Backup では、VM 上のデータが誤って破壊されることを防ぐために、独立して分離されたバックアップを提供しています。 [詳細情報](../backup/backup-azure-vms-introduction.md)。 料金は、保護対象の VM の数とサイズに基づいています。 [詳細情報](https://azure.microsoft.com/pricing/details/backup/)。    |Azure VM のベスト プラクティス - 運用    |Yes    |
|Azure Security Center    |Azure Security Center は統合されたインフラストラクチャ セキュリティ管理システムであり、データ センターのセキュリティ体制を強化し、クラウド内のハイブリッド ワークロード全体にわたる高度な脅威保護が提供されます。 [詳細情報](../security-center/security-center-introduction.md)。  VM が存在するサブスクリプションは、Automanage によって、Azure Security Center の Free レベルのオファリングに構成されます。 サブスクリプションが Azure Security Center に既にオンボードされている場合、自動管理ではサブスクリプションは再構成されません。    |Azure VM のベスト プラクティス - 運用、Azure VM のベスト プラクティス - Dev/Test    |No    |
|Microsoft Antimalware    |Azure に対する Microsoft マルウェア対策は、ウイルス、スパイウェアなどの悪意のあるソフトウェアの特定や駆除に役立つリアルタイムの保護です。 これにより、既知の悪意あるソフトウェアや望ましくないソフトウェアが Azure システム上にソフトウェア自体をインストールまたは実行しようとしたときに、アラートが生成されます。 [詳細情報](../security/fundamentals/antimalware.md)。 |Azure VM のベスト プラクティス - 運用、Azure VM のベスト プラクティス - Dev/Test    |Yes    |
|更新管理    |Azure Automation の Update Management を使用して、仮想マシンのオペレーティング システムの更新プログラムを管理することができます。 すべてのエージェント マシンで利用可能な更新プログラムの状態をすばやく評価し、サーバーに必要な更新プログラムをインストールするプロセスを管理できます。 [詳細情報](../automation/update-management/overview.md)。    |Azure VM のベスト プラクティス - 運用、Azure VM のベスト プラクティス - Dev/Test    |No    |
|変更履歴とインベントリ    |変更履歴とインベントリでは、変更履歴とインベントリの機能を組み合わせて、仮想マシンとサーバー インフラストラクチャの変更を追跡できます。 このサービスを利用すれば、環境内のサービス、デーモン、ソフトウェア、レジストリ、ファイル全体にわたって変更を追跡し、不要な変更を診断したりアラートを生成したりすることができます。 インベントリのサポートにより、ゲスト リソースでクエリを実行して、インストール済みのアプリケーションやその他の構成アイテムを可視化できます。  [詳細情報](../automation/change-tracking/overview.md)。    |Azure VM のベスト プラクティス - 運用、Azure VM のベスト プラクティス - Dev/Test    |No    |
|Azure ゲスト構成    | ゲスト構成ポリシーは、マシンのコンプライアンスに関するレポートを作成し、構成を監視するために使用されます。 ゲスト構成拡張機能を使用して、Automanage サービスによって [Windows セキュリティ ベースライン](/windows/security/threat-protection/windows-security-baselines)がインストールされます。 [詳細情報](../governance/policy/concepts/guest-configuration.md)。    |Azure VM のベスト プラクティス - 運用、Azure VM のベスト プラクティス - Dev/Test    |No    |
|Azure Automation アカウント    |Azure Automation は、インフラストラクチャとアプリケーションのライフサイクル全体にわたる管理をサポートします。 [詳細情報](../automation/automation-intro.md)。    |Azure VM のベスト プラクティス - 運用、Azure VM のベスト プラクティス - Dev/Test    |No    |
|Log Analytics ワークスペース    |Azure Monitor では、ログ データが Log Analytics ワークスペースに格納されます。Log Analytics ワークスペースは Azure リソースであり、データが収集、集計され、管理境界として機能するコンテナーです。 [詳細情報](../azure-monitor/logs/design-logs-deployment.md)。    |Azure VM のベスト プラクティス - 運用、Azure VM のベスト プラクティス - Dev/Test    |No    |


<sup>1</sup> Automanage を有効にしている場合は、構成プロファイルを使用できます。 [詳細情報](automanage-virtual-machines.md)。 構成プロファイルの既定の設定を調整し、ベスト プラクティスの制約内で独自の基本設定を指定することもできます。


## <a name="next-steps"></a>次の手順

Azure portal でAzure Automanage for virtual machines を有効にしてみてください。

> [!div class="nextstepaction"]
> [Azure portal で Azure Automanage for virtual machines を有効にする](quick-create-virtual-machines-portal.md)