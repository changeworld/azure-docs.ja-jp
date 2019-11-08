---
title: Azure Firewall Manager プレビューのデプロイの概要
description: Azure Firewall Manager プレビューに必要なデプロイ手順の概要について説明します。
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: overview
ms.date: 10/25/2019
ms.author: victorh
ms.openlocfilehash: df87e652d2969d4ae12e97a2b455648cf39382c3
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2019
ms.locfileid: "73488258"
---
# <a name="azure-firewall-manager-preview-deployment-overview"></a>Azure Firewall Manager プレビューのデプロイの概要

[!INCLUDE [Preview](../../includes/firewall-manager-preview-notice.md)]

Azure Firewall Manager プレビューをデプロイする方法は複数ありますが、次の一般的な手順をお勧めします。

## <a name="prerequisites"></a>前提条件

> [!IMPORTANT]
> `Register-AzProviderFeature` PowerShell コマンドを使用して、Azure Firewall Manager Preview を明示的に有効にする必要があります。
>PowerShell コマンド プロンプトから、次のコマンドを実行します。
>
>```azure-powershell
>connect-azaccount
>Register-AzProviderFeature -FeatureName AllowCortexSecurity -ProviderNamespace Microsoft.Network
>```
>機能の登録が完了するまで、最長で 30 分かかります。 次のコマンドを実行して、登録状態を確認します。
>
>`Get-AzProviderFeature -FeatureName AllowCortexSecurity -ProviderNamespace Microsoft.Network`



## <a name="general-deployment-process"></a>一般的なデプロイ プロセス

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

## <a name="next-steps"></a>次の手順

- [チュートリアル:Azure portal を使用して Azure Firewall Manager Preview でクラウド ネットワークをセキュリティで保護する](secure-cloud-network.md)