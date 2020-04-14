---
title: Azure Firewall のリモート作業のサポート
description: この記事では、Azure Firewall でリモート作業要員に関する要件をどのようにサポートできるかを示します。
services: firewall
author: vhorne
ms.service: firewall
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: victorh
ms.openlocfilehash: 5abe9344b0512433c48df50335cce5cf1e3e3547
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "80289408"
---
# <a name="azure-firewall-remote-work-support"></a>Azure Firewall のリモート作業のサポート

Azure Firewall は、Azure 仮想ネットワーク リソースを保護する、クラウドベースのマネージド ネットワーク セキュリティ サービスです。 組み込みの高可用性とクラウドの無制限のスケーラビリティを備えた、完全にステートフルなサービスとしてのファイアウォールです。 

## <a name="firewall-rules"></a>ファイアウォール規則

Azure Firewall を使い、Azure Firewall [DNAT ルール](rule-processing.md)を使用して、Azure 仮想ネットワークへの仮想デスクトップ インフラストラクチャ (VDI) の受信 RDP アクセスをセキュリティで保護できます。 Windows Virtual Desktop (WVD) では、仮想ネットワークへの受信アクセスを開く必要はありません。 しかし、仮想ネットワークで実行される WVD 仮想マシンに対して、一連の送信ネットワーク接続を許可する必要があります。 詳細については、「[Windows Virtual Desktop とは](../virtual-desktop/overview.md#requirements)」を参照してください

この送信アクセスは、Azure Firewall アプリケーション ルールを使用して構成できます。 詳細については、「[チュートリアル:Deploy and configure Azure Firewall using the Azure portal (チュートリアル: Azure portal を使用して Azure Firewall のデプロイと構成を行う)](tutorial-firewall-deploy-portal.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

[Windows Virtual Desktop](https://docs.microsoft.com/azure/virtual-desktop/) について、さらに詳しく学習します。