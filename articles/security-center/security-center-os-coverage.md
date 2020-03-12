---
title: Azure Security Center でサポートされているプラットフォーム | Microsoft Docs
description: このドキュメントでは、Azure Security Center でサポートされるプラットフォームの一覧を示します。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 70c076ef-3ad4-4000-a0c1-0ac0c9796ff1
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/29/2019
ms.author: memildin
ms.openlocfilehash: e13149ba802f0f8b9a565e0aabd86ae05167f18b
ms.sourcegitcommit: 5192c04feaa3d1bd564efe957f200b7b1a93a381
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/02/2020
ms.locfileid: "78208821"
---
# <a name="supported-platforms"></a>サポートされているプラットフォーム 

## 仮想マシン/サーバー <a name="vm-server"></a>

Security Center は、さまざまな種類のハイブリッド環境で仮想マシン/サーバーをサポートします。

* Azure のみ
* Azure とオンプレミス
* Azure とその他のクラウド
* Azure、その他のクラウド、およびオンプレミス

Azure サブスクリプションでアクティブ化された Azure 環境では、Azure Security Center によって、サブスクリプション内にデプロイされている IaaS リソースが自動的に検出されます。

> [!NOTE]
> すべてのセキュリティ機能を利用するには、Azure Security Center が使用する [Log Analytics エージェント](../azure-monitor/platform/agents-overview.md#log-analytics-agent)をインストールし、[Azure Security Center にデータを送信するように適切に構成する](security-center-enable-data-collection.md#manual-agent)必要があります。

次のセクションでは、Azure Security Center が使用する [Log Analytics エージェント](../azure-monitor/platform/agents-overview.md#log-analytics-agent)を実行できる、サポート対象のサーバー オペレーティング システムの一覧を示します。

### Windows Server オペレーティング システム <a name="os-windows"></a>

|OS|Azure Security Center でサポートされます|Microsoft Defender ATP との統合のサポート|
|:---|:-:|:-:|
|Windows Server 2019|✔|X|
|Windows Server 2016|✔|✔|
|Windows Server 2012 R2|✔|✔|
|Windows Server 2008 R2|✔|✔|

上記の Windows オペレーティング システムでサポートされている機能の詳細については、「[仮想マシン/サーバーでサポートされる機能](security-center-services.md#vm-server-features)」をご覧ください。

### Windows オペレーティング システム<a name="os-windows (non-server)"></a>

Azure Security Center は Azure サービスと統合し、Windows ベースの仮想マシンの監視と保護が行われます。

### Linux オペレーティング システム <a name="os-linux"></a>

64 ビット

* CentOS 6 および 7
* Amazon Linux 2017.09
* Oracle Linux 6 および Oracle Linux 7
* Red Hat Enterprise Linux Server 6 および 7
* Debian GNU/Linux 8 および 9
* Ubuntu Linux 14.04 LTS、16.04 LTS、および 18.04 LTS
* SUSE Linux Enterprise Server 12

32 ビット
* CentOS 6
* Oracle Linux 6
* Red Hat Enterprise Linux Server 6
* Debian GNU/Linux 8 および 9
* Ubuntu Linux 14.04 LTS および 16.04 LTS

> [!NOTE]
> サポートされている Linux オペレーティング システムの一覧は常に変更されているため、このトピックが最後に公開されてから変更があった場合は、サポートされているバージョンの最新の一覧を表示するために、[ここ](https://github.com/microsoft/OMS-Agent-for-Linux#supported-linux-operating-systems)をクリックしてください。

上記の Linux オペレーティングシステムでサポートされている機能の詳細については、「[仮想マシン/サーバーでサポートされる機能](security-center-services.md#vm-server-features)」をご覧ください。

### マネージド仮想マシン サービス <a name="virtual-machine"></a>

仮想マシンは、Azure Kubernetes (AKS)、Azure Databricks など、いくつかの Azure マネージド サービスの一部として、顧客サブスクリプションでも作成されます。 これらの仮想マシンも Azure Security Center によって検出され、Log Analytics エージェントは、上記のサポートされている [Windows/Linux オペレーティング システム](#os-windows)に従ってインストールおよび構成できます。

### Cloud Services <a name="cloud-services"></a>

クラウド サービスで実行する仮想マシン もサポートされます。 監視されるのは、運用スロットで実行するクラウド サービスの Web ロールと worker ロールだけです。 Cloud Services の詳細については、「[Azure Cloud Services の概要](../cloud-services/cloud-services-choose-me.md)」をご覧ください。

Azure Stack に存在する Virtual Machines の保護もサポートされています。 Security Center と Azure Stack との統合の詳細については、[Azure Stack 仮想マシンの Security Center へのオンボード](https://docs.microsoft.com/azure/security-center/quick-onboard-azure-stack)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

- [Security Center によるデータの収集方法と Log Analytics エージェント](security-center-enable-data-collection.md)について確認します。
- [Security Center でデータを管理および保護する](security-center-data-security.md)方法を確認します。
- [Azure Security Center を導入するための設計上の考慮事項を計画し、理解する](security-center-planning-and-operations-guide.md)方法について説明しています。
- [さまざまなクラウド環境で使用できる機能](security-center-services.md)について確認します。
- [Azure Security Center での Windows マシンおよび Linux マシンの脅威の防止](threat-protection.md#windows-machines)について詳しく確認します。
