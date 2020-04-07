---
title: Azure Site Recovery を使用してサーバーと VM を Azure に移行する
description: Azure Site Recovery サービスを使用して、オンプレミスおよび Azure IaaS の VM を Azure に移行する方法について説明します。
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: raynew
ms.openlocfilehash: a7107eae5c798deb78d4d35eccdf4adcf5273335
ms.sourcegitcommit: 0553a8b2f255184d544ab231b231f45caf7bbbb0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2020
ms.locfileid: "80388901"
---
# <a name="about-migration"></a>移行について

この記事では、[Azure Site Recovery](site-recovery-overview.md) サービスがマシンの移行にどのように役立つかを簡単に説明します。 

> [!TIP]
> VM とサーバーを Azure に移行するには、Azure Site Recovery サービスではなく、Azure Migrate サービスを使用しなければならなくなりました。 [詳細については、こちらを参照してください](../migrate/migrate-services-overview.md)。


Site Recovery を使用すると次の移行に対応できます。

- **オンプレミスから Azure への移行**: オンプレミスの Hyper-V VM、VMware VM、物理サーバーを Azure に移行します。 移行後、オンプレミスのマシンで実行されているワークロードが Azure VM で実行されるようになります。 
- **Azure 内での移行**: Azure リージョン間で Azure VM を移行します。 
- **AWS の移行**: AWS Windows インスタンスを Azure IaaS VM に移行します。 

> [!NOTE]
> これで、Azure Migrate サービスを使用してオンプレミスから Azure に移行できます。 [詳細については、こちらを参照してください](../migrate/migrate-overview.md)。

## <a name="what-do-we-mean-by-migration"></a>移行が意味すること

オンプレミス VM および Azure VM のディザスター リカバリーのために Site Recovery を使用する他に、Site Recovery サービスを使用してそれらの VM を移行できます。 どのような違いがあるでしょうか。

- ディザスター リカバリーでは、Azure にマシンを定期的にレプリケートします。 その後障害が発生したときは、マシンをプライマリ サイトからセカンダリ Azure サイトにフェールオーバーし、そこからこれらのマシンにアクセスします。 プライマリ サイトが再度使用可能になると、Azure からフェールバックします。
- 移行では、オンプレミス マシンを Azure へ、または Azure VM をセカンダリ リージョンにレプリケートします。 その後、VM をプライマリ サイトからセカンダリへフェールオーバーして、移行プロセスが完了します。 フェールバックは必要ありません。  


## <a name="migration-scenarios"></a>移行シナリオ

**シナリオ** | **詳細**
--- | ---
**オンプレミスから Azure への移行** | オンプレミスの VMware VM、Hyper-V VM、物理 サーバーを Azure に移行できます。 これを行うには、完全なディザスター リカバリーの場合とほぼ同じ手順を実行を完了します。 ただ、Azure からオンプレミス サイトにマシンをフェールバックしないだけです。
**Azure リージョンの間で移行** | Azure VM を 1 つの Azure リージョンから別のリージョンに移行できます。 移行が完了した後で、移行先のセカンダリ リージョンに現在ある Azure VM のディザスター リカバリーを構成できます。
**AWS を Azure に移行する** | AWS インスタンスを Azure VM に移行できます。 Site Recovery は、移行を目的として AWS インスタンスを物理サーバーのように扱います。 

## <a name="next-steps"></a>次のステップ

- [オンプレミスのマシンを Azure に移行する](migrate-tutorial-on-premises-azure.md)
- [VM を 1 つの Azure リージョンから別のリージョンに移行する](azure-to-azure-tutorial-migrate.md)
- [AWS を Azure に移行する](migrate-tutorial-aws-azure.md)
