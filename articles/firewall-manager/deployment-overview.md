---
title: Azure Firewall Manager プレビューのデプロイの概要
description: Azure Firewall Manager プレビューに必要なデプロイ手順の概要について説明します。
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: overview
ms.date: 02/18/2020
ms.author: victorh
ms.openlocfilehash: c3a94cea838609f65511a21ee2f64e8782a6adea
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/26/2020
ms.locfileid: "77443127"
---
# <a name="azure-firewall-manager-preview-deployment-overview"></a>Azure Firewall Manager プレビューのデプロイの概要

[!INCLUDE [Preview](../../includes/firewall-manager-preview-notice.md)]

Azure Firewall Manager プレビューをデプロイする方法は複数ありますが、次の一般的な手順をお勧めします。

## <a name="general-deployment-process"></a>一般的なデプロイ プロセス

### <a name="hub-virtual-networks"></a>ハブ仮想ネットワーク

1.  ファイアウォール ポリシーを作成する

    - 新しいポリシーの作成
<br>*or*<br>
    - 基本ポリシーを派生させてローカル ポリシーをカスタマイズします。
<br>*or*<br>
    - 既存の Azure Firewall から規則をインポートします。 複数のファイアウォールにわたって適用すべき NAT 規則はポリシーから削除してください。
1. ハブとスポークのアーキテクチャを作成する
   - Azure Firewall Manager を使用してハブ仮想ネットワークを作成し、仮想ネットワーク ピアリングを使用して、そこにスポーク仮想ネットワークをピアリングします。
<br>*or*<br>
    - 仮想ネットワークを作成して仮想ネットワーク接続を追加し、仮想ネットワーク ピアリングを使用して、そこにスポーク仮想ネットワークをピアリングします。

3. セキュリティ プロバイダーを選択し、ファイアウォール ポリシーを関連付ける。 現在、サポートされるプロバイダーは Azure Firewall だけです。

   - これは、ハブ仮想ネットワークの作成時に行います。
<br>*or*<br>
    - 既存の仮想ネットワークをハブ仮想ネットワークに変換します。 複数の仮想ネットワークを変換することもできます。

4. ハブ仮想ネットワーク ファイアウォールへのトラフィックをルーティングするユーザー定義ルートを構成する。


### <a name="secured-virtual-hubs"></a>セキュリティ保護付き仮想ハブ

1. ハブとスポークのアーキテクチャを作成する

   - Azure Firewall Manager を使用してセキュリティ保護付き仮想ハブを作成し、仮想ネットワーク接続を追加します。<br>*or*<br>
   - 仮想 WAN ハブを作成し、仮想ネットワーク接続を追加します。
2. セキュリティ プロバイダーを選択する

   - セキュリティ保護付き仮想ハブの作成中に完了します。<br>*or*<br>
   - 既存の仮想 WAN ハブをセキュリティ保護付き仮想ハブに変換します。
3. ファイアウォール ポリシーを作成してハブに関連付ける

   - Azure Firewall を使用する場合にのみ適用されます。
   - サードパーティのサービスとしてのセキュリティ (SECaaS) ポリシーは、パートナー管理エクスペリエンスを介して構成されます。
4. トラフィックをセキュリティ保護付きハブにルーティングするようにルート設定を構成する

   - トラフィックをフィルター処理とログ記録のためにセキュリティ保護付きハブに簡単にルーティングするには、スポークの仮想ネットワーク上のユーザー定義ルート (UDR) を使用せずに、セキュリティ保護付き仮想ハブのルート設定ページを使用します。

> [!NOTE]
> - 1 つのリージョンで仮想 WAN ごとに複数のハブを使用することはできません。 ただし、リージョンに複数の仮想 WAN を追加して、これを実現することができます。
> - vWAN のハブの IP 空間を重複させることはできません。
> - ハブ VNet 接続は、ハブと同じリージョンにある必要があります。

## <a name="next-steps"></a>次のステップ

- [チュートリアル:Azure portal を使用して Azure Firewall Manager Preview でクラウド ネットワークをセキュリティで保護する](secure-cloud-network.md)