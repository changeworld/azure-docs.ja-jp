---
title: セキュリティ保護付き仮想ハブとは
description: セキュリティ保護付き仮想ハブについて
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: conceptual
ms.date: 10/09/2019
ms.author: victorh
ms.openlocfilehash: d93c3b47c518962bf45762b846eaf53e490ae8f8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2020
ms.locfileid: "73512008"
---
# <a name="what-is-a-secured-virtual-hub"></a>セキュリティ保護付き仮想ハブとは

[!INCLUDE [Preview](../../includes/firewall-manager-preview-notice.md)]

仮想ハブは、他のリソースからの接続を可能にする Microsoft が管理する仮想ネットワークです。 Azure portal の仮想 WAN から仮想ハブを作成すると、仮想ハブ VNet とゲートウェイ (オプション) がそのコンポーネントとして作成されます。

*セキュリティ保護付き*仮想ハブは、Azure Firewall Manager によって構成された関連するセキュリティ ポリシーとルーティング ポリシーを持つ [Azure Virtual WAN ハブ](../virtual-wan/virtual-wan-about.md#resources)です。 セキュリティ保護付き仮想ハブを使用すると、トラフィック ガバナンスと保護のためのネイティブ セキュリティ サービスを使用して、ハブアンドスポーク アーキテクチャと推移的アーキテクチャを簡単に作成できます。 

セキュリティ保護付き仮想ハブを、オンプレミス接続しない中央のマネージド VNet として使用できます。 これにより、Azure Firewall のデプロイに以前に必要だった中央の VNet が置き換えられます。 セキュリティ保護付き仮想ハブは自動ルーティングを提供するため、独自の UDR (ユーザー定義ルート) を構成しなくても、ファイアウォール経由でトラフィックをルーティングすることができます。

また、セキュリティ保護付き仮想ハブを、完全な仮想 WAN アーキテクチャの一部として使用することもできます。 このアーキテクチャでは、Azure への、および Azure 経由の安全で最適化および自動化されたブランチ接続を提供します。 Azure Firewall およびその他のサードパーティのサービスとしてのセキュリティ (SECaaS) プロバイダーなど、ネットワーク トラフィックを保護および管理するサービスを選択することができます。

## <a name="create-a-secured-virtual-hub"></a>セキュリティ保護付き仮想ハブを作成する

Azure portal で Firewall Manager を使用して、新しいセキュリティ保護付き仮想ハブを作成するか、以前に Azure Virtual WAN を使用して以前作成した既存の仮想ハブを変換することができます。

## <a name="next-steps"></a>次のステップ

セキュリティ保護付き仮想ハブを作成し、それを使用してハブアンドスポーク ネットワークを保護および管理する方法については、[チュートリアル:Azure portal を使用して Azure Firewall Manager でクラウド ネットワークをセキュリティで保護する](secure-cloud-network.md)を参照してください。