---
title: Azure Network Function Manager の前提条件と要件
description: Network Function Manager の要件と前提条件について説明します。
author: prmitt
ms.service: network-function-manager
ms.topic: article
ms.date: 11/02/2021
ms.author: prmitt
ms.custom: ignite-fall-2021
ms.openlocfilehash: 8843c92943071a96c6e0e9340823a85d01b1c218
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131092275"
---
# <a name="network-function-manager-prerequisites-and-requirements"></a>Network Function Manager の前提条件と要件

このアーティクルは、Azure Network Function Manager を構成し使用するために必要な前提条件と要件を理解するのに役立ちます。

## <a name="azure-stack-edge-pro-with-gpu-installed-and-activated"></a><a name="edge-pro"></a>GPU がインストールおよびアクティブ化された Azure Stack Edge Pro

次の前提条件を満たしていることを確認します。

* Azure Stack Edge Pro デバイスで Azure Network Function Manager サービスが有効になっていることを確認します。
* ネットワーク機能をデプロイする前に、Azure Stack Edge Pro がインストールされ、アクティブ化されていることを確認します。
* Azure Stack Edge リソースは、Network Function Manager のリソースでサポートされているリージョンにデプロイする必要があります。 詳細については、[[利用可能なリージョン]](overview.md#regions) をご覧ください。
* Azure Stack Edge Pro の[クイックスタート](../databox-online/azure-stack-edge-gpu-quickstart.md)と[チュートリアル](../databox-online/azure-stack-edge-gpu-deploy-checklist.md)のすべての手順を実行してください。
* Azure 管理ポータルの Azure Stack Edge リソースのプロパティ セクションにあるデバイスの **状態** が **オンライン** であることを確認してください。

   :::image type="content" source="./media/overview/properties.png" alt-text="プロパティのスクリーンショット。" lightbox="./media/overview/properties.png":::

## <a name="partner-prerequisites"></a><a name="partner-prereq"></a>パートナーの前提条件

お客様は、1 つまたは複数の Network Function Manager [パートナー](partners.md)から選んで、ネットワーク機能を Azure Stack Edge デバイスにデプロイすることができます。 Network Function Manager Partners の詳細については、[パートナー](partners.md)のアーティクルを参照してください。

各パートナーには、ネットワーク機能を Azure Stack Edge デバイスにデプロイするためのネットワーク要件があります。 次の構成タスクを完了するには、ネットワーク機能パートナーの製品ドキュメントを参照してください。

* [異なるポートでネットワークを構成する](../databox-online/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md)。
* [Azure Stack Edge デバイスでコンピューティング ネットワークを有効にする](../databox-online/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md#enable-compute-network)。

## <a name="azure-account"></a><a name="account"></a>Azure アカウント

Azure Network Function Manager パートナーが Azure Marketplace からネットワーク機能をデプロイするには、同じ Azure サブスクリプション ID を有効にする必要があります。 Azure サブスクリプション ID がパートナーにオンボードされていることを確認します。

Azure Network Function Manager サービスは、Network Function Manager **デバイス** と Network Function Manager の **ネットワーク機能** リソースで構成されています。 デバイスとネットワークの機能のリソースは、Azure サブスクリプションに含まれています。 Azure Stack Edge Pro デバイスと Network Function Manager のリソースをアクティブ化するために使用する Azure サブスクリプション ID は同じである必要があります。

## <a name="port-requirements-and-firewall-rules"></a><a name="port-firewall"></a>ポートの要件とファイアウォール規則

Azure Stack Edge 上で動作する Network Function Manager (NFM) サービスは、ネットワーク機能をデプロイするための管理トラフィックとして NFM クラウド サービスへのアウトバウンド接続が必要です。 NFM は、Azure Stack Edge サービスと完全に統合されています。 [Azure Stack Edge](../databox-online/azure-stack-edge-gpu-system-requirements.md#networking-port-requirements) デバイスのネットワーク ポートの要件とファイアウォール規則を確認してください。  

ネットワーク機能パートナーでは、パートナー管理ポータルへのトラフィックを管理するためのファイアウォールとポートの構成規則の要件が異なります。 具体的な要件については、担当のネットワーク機能パートナーに確認してください。

## <a name="next-steps"></a>次のステップ

[チュートリアル: Network Function Manager デバイス リソースの作成](create-device.md)。
