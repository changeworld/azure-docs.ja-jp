---
title: 世界中の Azure リージョン全体でディザスター リカバリーを有効にする
description: この記事では、Azure Site Recovery のグローバル ディザスター リカバリー機能について説明します。
author: JYOTHIRMAISURI
manager: evansma
ms.service: site-recovery
ms.topic: article
ms.date: 08/09/2021
ms.author: v-jysur
ms.openlocfilehash: 1179558e01ff23c66db652395e10c7d6259af09b
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121780685"
---
# <a name="enable-global-disaster-recovery-using-azure-site-recovery"></a>Azure Site Recovery を使用してグローバル ディザスター リカバリーを有効にする

この記事では、同じまたは異なる Azure リージョン内の可用性ゾーン間で Azure 仮想マシンをレプリケート、フェールオーバー、およびフェールバックする方法について説明します。

>[!NOTE]
> Azure Site Recovery では、ユーザーが選択したソース リージョンおよびターゲット リージョンの外部にデータを移動したり格納したりすることはありません。 ユーザーは、別の (第 3 の) リージョンの Recovery Services コンテナーを選択することもできます。 Recovery Services コンテナーにはメタデータが含まれますが、実際の顧客データは含まれません。

Azure Site Recovery サービスは、計画された停止や計画外の停止の際にビジネス アプリの稼働状態を維持することで、事業継続とディザスター リカバリー戦略に貢献します。 リージョン障害が発生した場合にアプリケーションの稼働状態を維持するために、ディザスター リカバリー オプションを推奨しています。

## <a name="disaster-recovery-for-global-azure-regions"></a>グローバル Azure リージョンのディザスター リカバリー

Azure は、Azure から Azure へのディザスター リカバリーを使用して、ファーストクラスのクラウド ネイティブなディザスター リカバリー ソリューションを開始した、最初の大規模なパブリック クラウド プロバイダーでした。 このオファリングにより、同じ大陸内の Azure リージョン間でアプリケーションをレプリケートし、フェールオーバーすることができました。 また、ゾーン間ディザスター リカバリーもサポートされています。   

Azure Site Recovery では、グローバル ディザスター リカバリーがサポートされるようになりました。 これで、大陸間で任意の Azure リージョンからアプリケーションをレプリケートおよびフェールオーバーできるようになりました。

多くのユーザーは、グローバルなビジネスを経営し、Azure のグローバル プレゼンスを活用して世界中でアプリケーションをホストし、レプリケートしたいと考えています。 このような場合に、Azure Site Recovery を通じてグローバル ディザスター リカバリーを活用できます。 この新しいオファリングでは、Azure から Azure へのディザスター リカバリーでの大陸の境界がなくなります。 強力なグローバル ネットワークのバックボーンによって支援され、世界中の任意の Azure リージョンにアプリケーションをレプリケートおよびフェールオーバーできます。  

このオファリングによって、ゾーン間ディザスター リカバリー、大陸内ディザスター リカバリー、およびグローバル ディザスター リカバリーという、ネイティブ パブリックなクラウド ディザスター リカバリーのポートフォリオが完結します。

>[!NOTE]
>離れた大陸のディザスター リカバリー (DR) リージョンを選択すると、回復ポイントの目標 (RPO) にわずかな影響がありますが、Azure Site Recovery の SLA に影響を与えるほどではありません。

このオファリングによって、ゾーン間ディザスター リカバリー、大陸内ディザスター リカバリー、およびグローバル ディザスター リカバリーという、ネイティブ パブリックなクラウド ディザスター リカバリーのポートフォリオが完結します。  

## <a name="before-you-begin"></a>開始する前に
この記事は、[Azure から Azure へのディザスター リカバリーのチュートリアル](azure-to-azure-tutorial-enable-replication.md)の説明に従って Azure Site Recovery のデプロイの準備が完了していることを前提としています。

前提条件が満たされ、Recovery Services コンテナーが作成されていることを確認してください。

>[!NOTE]
> 仮想マシンでレプリケーションを有効にする前に、[サポート マトリックス](azure-to-azure-support-matrix.md)を参照してください。

## <a name="supported-platform-features"></a>サポートされているプラットフォーム機能

| **機能** | **サポートに関する声明** |
| --- | --- |
| クラシック VM | いいえ |
| ARM VM |  はい |
| Azure Disk Encryption v1 (デュアル パス、Azure Active Directory (Azure AD) あり) |  はい |
| Azure Disk Encryption v2 (シングル パス、Azure AD なし) |  はい |
|     |  いいえ |
| マネージド ディスク |  はい |
| カスタマー マネージド キー |   はい |
| 近接通信配置グループ |  はい |
| バックアップの相互運用性 | ファイル レベルのバックアップと復元がサポートされています。 ディスクおよび VM レベルのバックアップと復元はサポートされていません。 |
| ホット アド/削除 | ゾーン間のレプリケーションを有効にした後で、ディスクを追加できます。 ゾーン間のレプリケーションを有効にした後のディスクの削除はサポートされていません。 |

## <a name="enable-replication"></a>レプリケーションを有効にする

アプライアンスをすばやくレプリケートするには、次の手順を使用します。

1. ポータルで仮想マシンに移動します。

2. 左側の **[操作]** で **[ディザスター リカバリー]** を選択します

3. **[基本]** で、**ターゲット リージョン** を選択します。

   世界中の使用できるすべての Azure リージョンが、ドロップダウン メニューの下に表示されます。 希望するターゲット リージョンを選択します。  

   ![グローバル ディザスター リカバリーの設定](./media/azure-to-azure-enable-global-disaster-recovery/enable-global-disaster-recovery.png)

   必要に応じて、他の構成を変更できます。

4. **[ターゲット サブスクリプション]** 、 **[ターゲット仮想ネットワーク]** 、 **[Target Availability]\(ターゲット可用性\)** 、 **[Target Proximity Placement Group]\(ターゲット近接配置グループ\)** など、他の設定については、 **[詳細設定]** をクリックします。

5. **[レプリケーションを確認して開始する]** をクリックします。

## <a name="next-steps"></a>次のステップ

- [ゾーン間ディザスター リカバリー](azure-to-azure-how-to-enable-zone-to-zone-disaster-recovery.md)について学習します。
- [Azure から Azure へのディザスター リカバリー アーキテクチャ](azure-to-azure-architecture.md)について学習します
