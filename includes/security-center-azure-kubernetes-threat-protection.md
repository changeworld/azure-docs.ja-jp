---
author: memildin
ms.author: memildin
manager: rkarlin
ms.date: 10/18/2021
ms.topic: include
ms.openlocfilehash: 74209403f26404435a58e76efffd69e54435d221
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132353901"
---
Defender for Cloud では、コンテナー化された環境に対するリアルタイムの脅威の防止が提供され、不審なアクティビティに対してはアラートが生成されます。 ユーザーは、この情報を使用して、迅速にセキュリティの問題を修復し、コンテナーのセキュリティを強化することができます。

Defender for Cloud では、さまざまなレベルで脅威の防止が提供されます。 

* **ホスト レベル ( Microsoft Defender for servers が提供)** - Microsoft Defender for servers は、Defender for Cloud が他の VM で使用しているものと同じ Log Analytics エージェントを使用して、Web シェルの検出や既知の疑わしい IP アドレスとの接続などの不審なアクティビティについて、Linux Kubernetes ノードを監視します。 エージェントは、特権コンテナーの作成、API サーバーへの不審なアクセス、Docker コンテナー内での SSH (Secure Shell) サーバーの実行など、コンテナー固有の分析についても監視します。

    ホストにエージェントをインストールしなかった場合、脅威の防止によってもたらされるメリットとセキュリティ アラートは限定されます。 その場合でも、ネットワーク分析や悪意のあるサーバーとの通信に関連したアラートは通知されます。

    >[!IMPORTANT]
    > 仮想マシン スケール セットで実行されている Azure Kubernetes Service クラスターへの Log Analytics エージェントのインストールは、現在サポートされていません。

    ホスト レベルのアラートの一覧については、[アラートのリファレンス表](../articles/security-center/alerts-reference.md#alerts-containerhost)に関するページを参照してください。


* **クラスター レベル (Microsoft Defender for Kubernetes が提供)** - クラスター レベルでは、脅威の防止は Kubernetes の監査ログの分析に基づいています。 この **エージェントレス型** 監視を有効にするには、強化された保護機能を有効にします。 クラスターがオンプレミスまたは別のクラウド プロバイダー上にある場合は、[Azure Arc 対応 Kubernetes と Defender 拡張機能](../articles/security-center/defender-for-kubernetes-azure-arc.md)を有効にします。

    このレベルでアラートを生成するために、 Defender for Cloud によってクラスターのログが監視されます。 このレベルのイベントの例として、公開されている Kubernetes ダッシュボード、高い特権ロールの作成、機微なマウントの作成などがあります。

    >[!NOTE]
    > ご利用のサブスクリプションで Defender for Kubernetes プランを有効にした後に生じたアクションやデプロイに対して、Defender for Cloud によってセキュリティのアラートが生成されます。 

    クラスター レベルのアラートの一覧については、[アラートのリファレンス表](../articles/security-center/alerts-reference.md#alerts-k8scluster)を参照してください。

また、Microsoft のセキュリティ研究員から成るグローバル チームも、脅威の状況を絶えず監視しています。 コンテナー固有のアラートや脆弱性は、それらが検出された時点で追加されます。

> [!TIP]
> コンテナーのアラートをシミュレートするには、[こちらのブログ記事](https://techcommunity.microsoft.com/t5/azure-security-center/how-to-demonstrate-the-new-containers-features-in-azure-security/ba-p/1011270)の手順に従います。
