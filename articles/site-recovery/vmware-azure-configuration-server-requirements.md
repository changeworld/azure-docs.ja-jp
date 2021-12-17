---
title: VMware のディザスター リカバリー用の構成サーバーの Azure Site Recovery での要件。
description: この記事では、Azure に VMware のディザスター リカバリー用に構成サーバーを Azure Site Recovery を使用してデプロイするときのサポートと要件について説明します。
ms.service: site-recovery
ms.topic: article
ms.date: 08/19/2021
ms.openlocfilehash: 71861ec8f3b1e10f0052fe46705b726f06e717dd
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/19/2021
ms.locfileid: "122444304"
---
# <a name="configuration-server-requirements-for-vmware-disaster-recovery-to-azure"></a>Azure に VMware をディザスター リカバリーするための構成サーバーの要件

>[!NOTE]
> この記事の情報は、Azure Site Recovery クラシック リリースに適用されます。 プレビューで VM をレプリケートする場合、Azure Site Recovery レプリケーション アプライアンスを作成して使用する必要があります。 レプリケーション アプライアンスの要件とデプロイ方法の詳細については、[この記事](deploy-vmware-azure-replication-appliance-preview.md)を参照してください。

Azure への VMware 仮想マシンと物理サーバーのディザスター リカバリーに [Azure Site Recovery](site-recovery-overview.md) を使うときは、オンプレミスの構成サーバーを展開します。

- 構成サーバーは、オンプレミスの VMware と Azure の間の通信を調整します。 データのレプリケーションも管理します。
- 構成サーバーのコンポーネントとプロセスの[詳細をご確認ください](vmware-azure-architecture.md)。

## <a name="configuration-server-deployment"></a>構成サーバーのデプロイ

Azure への VMware VM のディザスター リカバリーのためには、VMware VM として構成サーバーをデプロイします。

- Site Recovery に用意されている OVA テンプレートを Azure portal からダウンロードして vCenter Server にインポートし、構成サーバー VM を設定します。
- OVA テンプレートを使用して構成サーバーをデプロイすると、VM はこの記事で説明されている要件に自動的に準拠します。
- OVA テンプレートを使用して構成サーバーを設定することを強くお勧めします。 ただし、VMware VM のディザスター リカバリーを設定するときに、OVA テンプレートを使用できない場合は、[これらの指示](physical-azure-set-up-source.md)を使用して構成サーバーをデプロイできます。
- Azure へのオンプレミス物理マシンのディザスター リカバリー用に構成サーバーをデプロイする場合は、[こちらの記事](physical-azure-set-up-source.md)の説明に従ってください。

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]

## <a name="next-steps"></a>次のステップ
Azure への [VMware VM](vmware-azure-tutorial.md) のディザスター リカバリーを設定する。
