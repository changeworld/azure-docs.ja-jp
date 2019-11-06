---
title: Azure Site Recovery を使用して VMware を Azure にディザスター リカバリーするための構成サーバーの要件 | Microsoft Docs
description: この記事では、Azure Site Recovery による Azure への VMware のディザスター リカバリー用に構成サーバーをデプロイするときのサポートと要件について説明します
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
services: site-recovery
ms.topic: article
ms.date: 09/09/2019
ms.author: raynew
ms.openlocfilehash: d83b99ea540d6232f4c0786d3a743f97332e1c9f
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/23/2019
ms.locfileid: "72792337"
---
# <a name="configuration-server-requirements-for-vmware-disaster-recovery-to-azure"></a>Azure に VMware をディザスター リカバリーするための構成サーバーの要件

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

## <a name="next-steps"></a>次の手順
Azure への [VMware VM](vmware-azure-tutorial.md) のディザスター リカバリーを設定する。
