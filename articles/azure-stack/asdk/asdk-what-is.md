---
title: Azure Stack Development Kit (ASDK) の概要 | Microsoft Docs
description: ASDK の概要と、Microsoft Azure Stack を評価するための一般的なユース ケースについて説明します。
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.custom: mvc
ms.date: 10/25/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: 345d62f4045e7b9279d52acf70cc340d088f5db0
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/06/2018
ms.locfileid: "52961333"
---
# <a name="what-is-the-azure-stack-development-kit"></a>Azure Stack Development Kit とは
[Microsoft Azure Stack 統合システム](../azure-stack-poc.md)には 4 ノードから 16 ノードまでのサイズがあり、ハードウェア パートナーと Microsoft によって共同でサポートされます。 Azure Stack 統合システムの使用により、運用ワークロード向けの新しいシナリオが可能になります。 統合システム インフラストラクチャの管理およびサービスの提供を行う Azure Stack オペレーターは、[オペレーター ドキュメント](https://docs.microsoft.com/azure/azure-stack)を参照してください。

Azure Stack Development Kit (ASDK) は、Azure Stack の単一ノード デプロイであり、**無料**でダウンロードして使用できます。 すべての ASDK コンポーネントは、単一のホスト サーバー コンピューター上で実行される仮想マシンにインストールされます。そのコンピューターは、[最低限のハードウェア要件](asdk-deploy-considerations.md#hardware)を満たす必要があります。 ASDK の目的は、"*非運用*" 環境で Azure と一貫性のある API とツールを使用して、Azure Stack の評価と最新アプリケーションの開発ができる環境を提供することです。 

> [!IMPORTANT]
> ASDK の運用環境での実行は、想定されておらず、サポートされていません。

すべての ASDK コンポーネントが単一の開発キット ホスト コンピューターにデプロイされるため、使用可能な物理リソースは限られています。 ASDK のデプロイでは、Azure Stack インフラストラクチャ VM とテナント VM の両方が同じサーバー コンピューター上に共存します。 この構成は、拡張性やパフォーマンスの評価のためのものではありません。

ASDK は、以下のユーザーに Azure の一貫したハイブリッド クラウド エクスペリエンスを提供するように設計されています。
- **管理者** (Azure Stack オペレーター)。 ASDK は、利用可能な Azure Stack サービスの評価と学習に役立つ優れたリソースです。
- **開発者**。 ASDK を使用すると、オンプレミス (開発/テスト環境) でハイブリッド アプリケーションや最新型アプリケーションを開発することができます。 そうすることで、Azure Stack の運用環境のデプロイの前またはそれと同時に、開発環境を再現することができるようになります。 

ASDK の詳細については、次の短い動画をご覧ください。

> [!VIDEO https://www.youtube.com/embed/dbVWDrl00MM]


## <a name="asdk-and-multi-node-azure-stack-differences"></a>ASDK とマルチノード Azure Stack の相違点
単一ノードの ASDK デプロイは、マルチノードの Azure Stack デプロイとはいくつかの点で異なるため、注意が必要です。

|説明|ASDK|マルチノード Azure Stack|
|-----|-----|-----|
|**スケール**|すべてのコンポーネントが、単一ノードのサーバー コンピューターにインストールされます。|サイズは 4 から 16 ノードの範囲で設定できます。|
|**回復力**|単一ノードの構成では、高可用性は提供されません|[高可用性](../azure-stack-key-features.md#high-availability-for-azure-stack)機能がサポートされています。|
|**ネットワーク**|ASDK は、AzS-BGPNAT01 という名前の VM を使用して、すべての ASDK ネットワーク トラフィックをルーティングします。 他のスイッチ要件はありません。|AzS-BGPNAT01 VM は、マルチノード デプロイには存在しません。 Top-Of-Rack (TOR)、ベースボード管理コントローラー (BMC)、境界 (データセンター ネットワーク) スイッチなど、より複雑な[ネットワーク ルーティング インフラストラクチャ](../azure-stack-network.md#network-infrastructure)が必要です。|
|**修正プログラムと更新プログラムの処理**|ASDK の新しいバージョンに移行するには、開発キットのホスト コンピューターで ASDK を再デプロイする必要があります。|インストール済みの Azure Stack バージョンを更新するために、[修正プログラムと更新プログラム](../azure-stack-updates.md)のプロセスが使用されます。|
|**サポート**|MSDN の Azure Stack フォーラム。 Microsoft カスタマー サービス & サポート (CSS) のサポートは、非運用環境では使用 "*できません*"。|[MSDN の Azure Stack フォーラム](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStack)と完全な CSS サポート。|
| | |

## <a name="learn-about-available-services"></a>利用できるサービスの詳細
Azure Stack オペレーターは、ユーザーにどのサービスを提供できるかを知っておく必要があります。 Azure Stack は Azure サービスのサブセットをサポートしており、サポートされるサービスの一覧は常に進化し続けています。

### <a name="foundational-services"></a>基礎となるサービス
ASDK をデプロイすると、Azure Stack には既定で以下の "基礎となるサービス" が含まれています。
- Compute
- Storage
- ネットワーク
- Key Vault

これらの基礎となるサービスによって、最小限の構成でユーザーにサービスとしてのインフラストラクチャ (IaaS) を提供できます。

### <a name="additional-services"></a>その他のサービス
現時点では、その他に以下の、サービスとしてのプラットフォーム (PaaS) サービスがサポートされています。
- App Service
- Azure Functions
- SQL および MySQL データベース

> [!NOTE]
> これらのサービスをユーザーが利用できるようにするには、追加の構成が必要です。ASDK をインストールした時点で、既定では使用できません。

## <a name="service-roadmap"></a>サービスのロードマップ
Azure Stack には、引き続き Azure サービスのサポートが追加される予定です。 Azure Stack の今後の予定については、[Azure Stack ロードマップ](https://azure.microsoft.com/roadmap/?tag=azure-stack)を参照してください。 


## <a name="next-steps"></a>次の手順
Azure Stack の評価を開始するには、最初に[最新の ASDK](asdk-download.md) をダウンロードし、ASDK ホスト コンピューターを準備する必要があります。 開発キット ホストを準備したら、ASDK をインストールし、管理者ポータルとユーザー ポータルにサインインして、Azure Stack の使用を開始することができます。