---
title: Microsoft Defender for Cloud でサポートされているプラットフォーム | Microsoft Docs
description: このドキュメントでは、Microsoft Defender for Cloud でサポートされているプラットフォームの一覧を示します。
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: overview
ms.date: 03/31/2020
ms.author: memildin
ms.openlocfilehash: 4cf49562119503e7b0c54cb0691cbb520c6a0f9e
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131428351"
---
# <a name="supported-platforms"></a>サポートされているプラットフォーム 

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

このページでは、Microsoft Defender for Cloud でサポートされているプラットフォームと環境を示します。

## <a name="combinations-of-environments"></a>環境の組み合わせ <a name="vm-server"></a>

Microsoft Defender for Cloud では、さまざまな種類のハイブリッド環境の仮想マシンとサーバーがサポートされます。

* Azure のみ
* Azure とオンプレミス
* Azure とその他のクラウド
* Azure、その他のクラウド、およびオンプレミス

Azure サブスクリプションでアクティブ化された Azure 環境では、Microsoft Defender for Cloud によって、サブスクリプション内にデプロイされている IaaS リソースが自動的に検出されます。

## <a name="supported-operating-systems"></a>サポートされるオペレーティング システム

Defender for Cloud は [Log Analytics エージェント](../azure-monitor/agents/agents-overview.md#log-analytics-agent)に依存します。 次のページの説明に従って、このエージェントでサポートされているオペレーティング システムのいずれかがマシンで実行されていることを確認してください。

* [Windows 用の Log Analytics エージェントでサポートされているオペレーティング システム ](../azure-monitor/agents/agents-overview.md#supported-operating-systems)
* [Linux 用の Log Analytics エージェントでサポートされているオペレーティング システム](../azure-monitor/agents/agents-overview.md#supported-operating-systems)

また、Log Analytics エージェントが [Defender for Cloud にデータを送信するように適切に構成されている](enable-data-collection.md#manual-agent)ことを確認してください

Windows および Linux で使用できる特定の Defender for Cloud 機能の詳細については、「[マシンを対象とする機能](supported-machines-endpoint-solutions-clouds.md)」を参照してください。

> [!NOTE]
> **Microsoft Defender for servers** はサーバーを保護するように設計されていますが、そのほとんどは Windows 10 マシンを対象としてサポートされています。 現在サポートされていない機能の 1 つは、[Defender for Cloud の統合 EDR ソリューション: Microsoft Defender for Endpoint](integration-defender-for-endpoint.md) です。

## <a name="managed-virtual-machine-services"></a>マネージド仮想マシン サービス <a name="virtual-machine"></a>

仮想マシンは、Azure Kubernetes (AKS)、Azure Databricks など、いくつかの Azure マネージド サービスの一部として、顧客サブスクリプションでも作成されます。 Defender for Cloud ではこれらの仮想マシンも検出され、サポートされている OS が使用可能な場合は、Log Analytics エージェントをインストールして構成できます。

## <a name="cloud-services"></a>Cloud Services <a name="cloud-services"></a>

クラウド サービスで実行する仮想マシン もサポートされます。 監視されるのは、運用スロットで実行するクラウド サービスの Web ロールと worker ロールだけです。 Cloud Services の詳細については、「[Azure Cloud Services の概要](../cloud-services/cloud-services-choose-me.md)」をご覧ください。

Azure Stack Hub に存在する VM の保護もサポートされています。 Defender for Cloud と Azure Stack Hub との統合の詳細については、[Azure Stack Hub 仮想マシンの Defender for Cloud へのオンボード](quickstart-onboard-machines.md?pivots=azure-portal#onboard-your-azure-stack-hub-vms)に関するページを参照してください。 

## <a name="next-steps"></a>次の手順

- [Defender for Cloud による Log Analytics エージェントを使用したデータの収集](enable-data-collection.md)方法について学習します。
- [Defender for Cloud でデータを管理および保護する方法](data-security.md)について学習します。
- [Microsoft Defender for Cloud を導入するための計画と設計上の考慮事項](security-center-planning-and-operations-guide.md)について学習します。