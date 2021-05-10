---
author: memildin
ms.author: memildin
manager: rkarlin
ms.date: 04/07/2021
ms.topic: include
ms.openlocfilehash: 73886b966676af75cc74484ccdc0632f080b041a
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/07/2021
ms.locfileid: "107029143"
---
Azure Defender では、コンテナー化された環境に対するリアルタイムの脅威の防止が提供され、疑わしいアクティビティに対してはアラートが生成されます。 ユーザーは、この情報を使用して、迅速にセキュリティの問題を修復し、コンテナーのセキュリティを強化することができます。

Azure Defender では、さまざまなレベルで脅威の防止が提供されます。 

* **ホスト レベル (Azure Defender for servers が提供)** - Azure Defender は、Security Center が他の VM で使用しているものと同じ Log Analytics エージェントを使用して、Web シェルの検出や既知の疑わしい IP アドレスとの接続などの不審なアクティビティについて、Linux Kubernetes ノードを監視します。 エージェントは、特権コンテナーの作成、API サーバーへの不審なアクセス、Docker コンテナー内での SSH (Secure Shell) サーバーの実行など、コンテナー固有の分析についても監視します。

    ホストにエージェントをインストールしなかった場合、脅威の防止によってもたらされるメリットとセキュリティ アラートは限定されます。 その場合でも、ネットワーク分析や悪意のあるサーバーとの通信に関連したアラートは通知されます。

    >[!IMPORTANT]
    > 仮想マシン スケール セットで実行されている Azure Kubernetes Service クラスターへの Log Analytics エージェントのインストールは、現在サポートされていません。

    クラスター レベルのアラートの一覧については、[アラートのリファレンス表](../articles/security-center/alerts-reference.md#alerts-containerhost)を参照してください。


* **クラスター レベル (Azure Defender for Kubernetes が提供)** - クラスター レベルでは、脅威の防止は Kubernetes の監査ログの分析に基づいています。 この **エージェントレス型** 監視を有効にするには、Azure Defender を有効にします。 クラスターがオンプレミスまたは別のクラウド プロバイダー上にある場合は、[Arc 対応 Kubernetes と Azure Defender 拡張機能](../articles/security-center/defender-for-kubernetes-azure-arc.md)を有効にします。

    このレベルでアラートを生成するために、Azure Defender によってクラスターのログが監視されます。 このレベルのイベントの例として、公開されている Kubernetes ダッシュボード、高い特権ロールの作成、機微なマウントの作成などがあります。

    >[!NOTE]
    > ご利用のサブスクリプションで Defender for Kubernetes プランを有効にした後に生じたアクションやデプロイに対して、Azure Defender によってセキュリティのアラートが生成されます。 

    クラスター レベルのアラートの一覧については、[アラートのリファレンス表](../articles/security-center/alerts-reference.md#alerts-akscluster)を参照してください。

また、Microsoft のセキュリティ研究員から成るグローバル チームも、脅威の状況を絶えず監視しています。 コンテナー固有のアラートや脆弱性は、それらが検出された時点で追加されます。

> [!TIP]
> コンテナーのアラートをシミュレートするには、[こちらのブログ記事](https://techcommunity.microsoft.com/t5/azure-security-center/how-to-demonstrate-the-new-containers-features-in-azure-security/ba-p/1011270)の手順に従います。