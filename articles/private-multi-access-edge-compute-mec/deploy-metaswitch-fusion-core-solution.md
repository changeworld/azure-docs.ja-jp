---
title: Azure Stack Edge デバイスに Fusion Core をデプロイする
description: Microsoft Azure と Metaswitch Networks からクラウド ソリューションをデプロイする方法について説明します。これにより、ネットワークが将来においても有効になり、コストを削減し、新しいビジネス モデルと収益ストリームを作成するのに役立ちます。
author: djrmetaswitch
ms.service: private-multi-access-edge-compute-mec
ms.topic: how-to
ms.date: 06/16/2021
ms.author: drichards
ms.openlocfilehash: 93e7511cbe8202f15dd055467c253044fa617e74
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/16/2021
ms.locfileid: "114286914"
---
# <a name="deploy-fusion-core-on-an-azure-stack-edge-device"></a>Azure Stack Edge デバイスに Fusion Core をデプロイする

この記事では、Fusion Core を Azure Stack Edge デバイスにデプロイするプロセスの概要について説明します。

## <a name="collect-required-azure-resources"></a>必要な Azure リソースを収集する

次のものが必要です。

- 完全にデプロイされた GPU 搭載の Azure Stack Edge Pro。 デバイスのポートは、次のように接続する必要があります。

  - ポート 5 - LAN
  - ポート 6 - WAN
  - 管理ネットワークに接続されている 1 つのポート。 1 から 4 の任意のポートを選択できます。 「[チュートリアル: GPU 搭載の Azure Stack Edge Pro 用のネットワークを構成する](../databox-online/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md)」の「コンピューティング ネットワークを有効にする」の手順で説明されているように、コンピューティング用に選択したポートが有効になっている必要があります。
- アクティブなサブスクリプションを持ち、次へのアクセス権がある Azure アカウント。

  - Azure Network Function Manager サービス。 これには、リソース プロバイダー名前空間 Microsoft.HybridNetwork があります。 詳細については、「[Azure Network Function Manager とは (プレビュー)](../network-function-manager/overview.md)」を参照してください。
  - Fusion Core - 5G パケット コア マネージド アプリケーション。 [https://azuremarketplace.microsoft.com/marketplace/apps/metaswitch.fusioncore_0-1-0?tab=Overview](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/metaswitch.fusioncore_0-1-0?tab=Overview) にアクセスし、 **[お問い合わせ]** ボタンを使用してアクセスを要求する必要があります。 営業担当者より、Fusion Core を評価または購入する方法に関する情報を提供します。 これらのオプションのいずれかを選択すると、マネージド アプリケーションへのアクセス権が付与されます。
  - サブスクリプション スコープでの組み込みの **所有者** ロール。 ご自身の組織でこれを利用できない場合は、Metaswitch サポート担当者にお問い合わせください。
- Azure Stack Edge デバイスを表す構成済みの **Azure Network Function Manager - Device** オブジェクト。

## <a name="deploy-fusion-core"></a>Fusion Core のデプロイ

次の図は、作成するオブジェクトやインストール後に継続的に管理する方法を含めた、Fusion Core をデプロイするプロセスを示しています。

:::image type="content" source="./media/deploy-metaswitch-solution/fusion-core-on-azure-stack-edge-deployment-process.png" alt-text="Fusion Core のデプロイ プロセス":::  

Azure Marketplace から ASE 用 Fusion Core を選択した後、Azure サブスクリプション内の選択したリソース グループに Fusion Core マネージド アプリケーションの名前付きインスタンスをデプロイします。

Fusion Core マネージド アプリケーションによって、Azure Stack Edge (ASE) デバイスにインストールされる、Packet Core Network Function リソースと Relay リソースが含まれる管理対象リソース グループが作成されます。

- Packet Core Network Function リソースは、Fusion Core ベース VM のバージョンとその構成パラメーターを定義します。 Azure portal では既定で非表示になっています。
- Relay リソースには、インストールと監視インターフェイスへのリモート アクセスに必要な接続を提供するハイブリッド接続が多数含まれています。

その後、Fusion Core が ASE デバイスに自動的にデプロイされます。

1. ASE デバイスによって、Fusion Core ベース VM がダウンロードされて起動されます。
1. Metaswitch Service Management アプリケーションによって、Fusion Core ベース VM 内の Kubernetes クラスターに Fusion Core がインストールされてプロビジョニングされます。

Fusion Core のインストールの調整に加えて、Metaswitch のサポート担当者は Metaswitch Service Management アプリケーションを使用して、システムの正常性を監視し、診断にアクセスできます。


## <a name="next-steps"></a>次のステップ
- Azure Stack Edge デバイスに Fusion Core をデプロイするステップ バイ ステップの手順については、[プライベート 5G エッジ Fusion Core ソリューション ガイドをダウンロード](https://go.microsoft.com/fwlink/?linkid=2165096)してください。
- [プライベート 5G エッジ Fusion Core](metaswitch-fusion-core-overview.md) の詳細を確認する