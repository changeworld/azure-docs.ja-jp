---
title: コンテナーに対する Azure Monitor (プレビュー) エージェントをアップグレードする方法 | Microsoft Docs
description: この記事では、コンテナーに対する Azure Monitor で使用される Log Analytics エージェントをアップグレードする方法について説明します。
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/13/2018
ms.author: magoedte
ms.openlocfilehash: fb45002d284bc28dfb7093f69cfac7aae0681e8d
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/14/2018
ms.locfileid: "51628510"
---
# <a name="how-to-upgrade-the-azure-monitor-for-containers-preview-agent"></a>コンテナーに対する Azure Monitor (プレビュー) エージェントをアップグレードする方法
コンテナーに対する Azure Monitor では、コンテナー化されたバージョンの Linux 用 Log Analytics エージェント が使用されます。 エージェントの新しいバージョンがリリースされた場合でも、Azure Kubernetes Service (AKS) でホストされているマネージド Kubernetes クラスター上のエージェントが自動的にアップグレードされることはありません。

この記事では、エージェントをアップグレードするプロセスについて説明します。

## <a name="upgrading-agent-on-monitored-kubernetes-cluster"></a>監視対象の Kubernetes クラスター上のエージェントのアップグレード
エージェントをアップグレードするプロセスは、2 つの単純な手順で構成されます。 最初の手順は、コンテナーに対する Azure Monitor の監視を Azure CLI を使用して無効にすることです。  [監視の無効化](monitoring-container-insights-optout.md?toc=%2fazure%2fmonitoring%2ftoc.json#azure-cli)に関する記事の手順に従ってください。 Azure CLI を使用して、ソリューションとワークスペースに格納されている対応するデータに影響を与えることなく、クラスター内のノードからエージェントを削除できます。 

>[!NOTE]
>このメンテナンス アクティビティを実行している間、クラスター内のノードによる収集されたデータの転送は行われず、エージェントを削除して新しいバージョンをインストールするまでの間、パフォーマンス ビューにデータは表示されません。 
>

新しいバージョンのエージェントをインストールするには、Azure CLI を使用する[監視の利用開始](monitoring-container-insights-onboard.md?toc=%2fazure%2fmonitoring%2ftoc.json#enable-monitoring-using-azure-cli)に関する記事の手順に従って、このプロセスを完了します。  

監視を再び有効にした後、クラスターの更新された正常性メトリックが表示されるまで、約 15 分かかる場合があります。 

## <a name="next-steps"></a>次の手順
エージェントのアップグレード中に問題が発生した場合は、[トラブルシューティング ガイド](monitoring-container-insights-troubleshoot.md)を参照してください。