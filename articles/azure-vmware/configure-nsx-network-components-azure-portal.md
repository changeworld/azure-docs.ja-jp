---
title: Azure VMware Solution を使用して NSX ネットワーク コンポーネントを構成する
description: Azure VMware Solution を使用して、NSX-T ネットワーク セグメントを構成する方法について説明します。
ms.topic: reference
ms.date: 09/13/2021
ms.openlocfilehash: 190ec1e93cb9fef795e47693f80e5e9296354b83
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128699706"
---
# <a name="configure-nsx-network-components-using-azure-vmware-solution"></a>Azure VMware Solution を使用して NSX ネットワーク コンポーネントを構成する

Azure VMware Solution プライベート クラウドには、既定では NSX-T が付属しています。 プライベート クラウドには、**アクティブ/アクティブ** モードで NSX-T Tier-0 ゲートウェイが、アクティブ/スタンバイ モードで既定の NSX-T Tier-1 ゲートウェイが事前プロビジョニングされています。  これらのゲートウェイを使用すると、セグメント (論理スイッチ) に接続し、East-West および North-South 接続を提供できます。 

Azure VMware Solution のデプロイ後、必要な NSX-T オブジェクトを Azure portal から構成できます。  これには、VMware 管理者が毎日必要とする NSX-T 操作のうち、NSX-T Manager に慣れていないユーザーを対象とするものを簡略化したビューが表示されます。  

Azure VMware Solution コンソールで NSX-T コンポーネントを構成するための 4 つのオプションがあります。

- **[セグメント]** - NSX-T Manager と vCenter に表示されるセグメントを作成します。 詳細については、「[Azure portal を使用して NSX-T セグメントを追加する](tutorial-nsx-t-network-segment.md#use-azure-portal-to-add-an-nsx-t-segment)」を参照してください。

- **[DHCP]** - DHPC を使用する予定の場合は、DHCP サーバーまたは DHCP リレーを作成します。  詳細については、「[Azure portal を使用して DHCP サーバーまたはリレーを作成する](configure-dhcp-azure-vmware-solution.md#use-the-azure-portal-to-create-a-dhcp-server-or-relay)」を参照してください。

- **[ポート ミラーリング]** - ポート ミラーリングを作成して、ネットワークの問題のトラブルシューティングに役立てます。 詳細については、「[Azure portal でポート ミラーリングを構成する](configure-port-mirroring-azure-vmware-solution.md)」を参照してください。

- **[DNS]** - 指定された DNS サーバーに解決のために DNS 要求を送信する DNS フォワーダーを作成します。  詳細については、「[Azure portal で DNS フォワーダーを構成する](configure-dns-azure-vmware-solution.md)」を参照してください。

>[!IMPORTANT]
>引き続き、NSX-T Manager コンソールにアクセスして、前述の詳細設定とその他の NSX-T 機能を使用できます。 

