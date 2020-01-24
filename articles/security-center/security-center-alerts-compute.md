---
title: Azure Security Center でのクラウド ネイティブ コンピューティングの脅威検出 | Microsoft Docs
description: この記事では、Azure Security Center で使用可能なクラウド ネイティブ コンピューティングのアラートについて説明します。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 5aa5efcf-9f6f-4aa1-9f72-d651c6a7c9cd
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/05/2020
ms.author: memildin
ms.openlocfilehash: a85ec565077ac229cd24bb7b9cf753015aa56ebf
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/15/2020
ms.locfileid: "76024857"
---
# <a name="threat-detection-for-cloud-native-computing-in-azure-security-center"></a>Azure Security Center でのクラウド ネイティブ コンピューティングの脅威検出

Azure Security Center はネイティブ ソリューションとして、内部ログに対する独自の可視性を備え、複数のターゲットに対する攻撃手法を識別します。 この記事では、次の Azure サービスで使用可能なアラートについて説明します。

* [Azure App Service](#app-services)
* [Azure コンテナー](#azure-containers) 

> [!NOTE]
> Azure Government およびソブリン クラウド リージョンでは現在、これらのサービスを利用できません。

## Azure App Service<a name="app-services"></a>

Security Center では、クラウドのスケールを使用して、App Service で実行されるアプリケーションをターゲットとした攻撃が識別されます。 攻撃者は、Web アプリケーションをプローブして弱点を発見し、悪用します。 Azure で実行されているアプリケーションに対する要求は、特定の環境にルーティングされる前に、複数のゲートウェイを通過し、そこで検査され、ログに記録されます。 その後、このデータは、悪用や攻撃者を特定し、後で使用される新しいパターンを学習するために使用されます。

Security Center では、Azure がクラウド プロバイダーとして備える可視性を使用して、App Service の内部ログが分析され、複数のターゲットに対する攻撃手法が識別されます。 たとえば、広範囲にわたるスキャンや分散型の攻撃などの手法です。 この種の攻撃は通常、IP の小さなサブセットから発生し、複数のホスト上の類似のエンドポイントをクロールするパターンを示します。 攻撃では脆弱なページやプラグインが検索され、単一のホストの観点からは攻撃を特定できません。

Windows ベースの App Service プランを実行している場合、Security Center では、基になるサンド ボックスおよび VM にアクセスすることもできます。 前述のログ データと組み合わせることで、インフラストラクチャは、流行している新しい攻撃から、お客様のマシンの侵害まで、状況を通知できます。 したがって、Web アプリが悪用された後に Security Center がデプロイされたとしても、進行中の攻撃を検出できる可能性があります。

Azure App Service アラートの一覧については、[アラートのリファレンス表](alerts-reference.md#alerts-azureappserv)に関するページを参照してください。

## Azure コンテナー <a name="azure-containers"></a>

Security Center は、コンテナー化された環境に対するリアルタイムの脅威検出機能を備えており、不審なアクティビティに対してはアラートが生成されます。 ユーザーは、この情報を使用して、迅速にセキュリティの問題を修復し、コンテナーのセキュリティを強化することができます。

さまざまなレベルの脅威が検出されます。 

* **ホスト レベル** - Security Center のエージェント (Standard レベルで利用可能。詳細については、[価格](security-center-pricing.md)に関するページを参照) が、Linux に対する不審なアクティビティを監視します。 ノードまたはそこで実行されているコンテナーを発生源とする不審なアクティビティについては、エージェントによってアラートがトリガーされます。 そのようなアクティビティとしては、たとえば、Web シェルの検出や既知の不審な IP アドレスとの接続が挙げられます。

    コンテナー化された環境のセキュリティについて、より詳しい分析情報を得るために、エージェントは、コンテナー固有の分析情報を監視します。 特権コンテナーの作成、API サーバーへの不審なアクセス、Docker コンテナー内での SSH (Secure Shell) サーバーの実行などのイベントが発生すると、アラートがトリガーされます。

    >[!NOTE]
    > ホストにエージェントをインストールしなかった場合、脅威検出によってもたらされるメリットとアラートは限定されます。 その場合でも、ネットワーク分析や悪意のあるサーバーとの通信に関連したアラートは通知されます。

    ホスト レベルのアラートの一覧については、[アラートのリファレンス表](alerts-reference.md#alerts-containerhost)に関するページを参照してください。


* **AKS クラスター レベル** - Kubernetes の監査ログ分析に基づく脅威検出の監視が行われます。 この**エージェントレス**の監視を有効にするには、 **[価格と設定]** ページから、ご利用のサブスクリプションに Kubernetes オプションを追加してください ([価格](security-center-pricing.md)に関するページを参照)。 このレベルのアラートを生成するために、Security Center は、AKS によって取得されたログを使用して、AKS のマネージド サービスを監視します。 このレベルのイベントとしては、たとえば、Kubernetes ダッシュボードの露出、高い特権ロールの作成、機密情報にまつわるマウントの作成が挙げられます。

    >[!NOTE]
    > Azure Kubernetes Service のアクションやデプロイについての検出アラートが Security Center から生成されるのは、サブスクリプションの設定で Kubernetes オプションが有効にされた後になります。 

    AKS クラスター レベルのアラートの一覧については、[アラートのリファレンス表](alerts-reference.md#alerts-akscluster)に関するページを参照してください。

また、Microsoft のセキュリティ研究員から成るグローバル チームも、脅威の状況を絶えず監視しています。 コンテナー固有のアラートや脆弱性は、それらが検出された時点で追加されます。

## <a name="next-steps"></a>次のステップ

* App Service プランの詳細については、「[App Service プラン](https://azure.microsoft.com/pricing/details/app-service/plans/)」を参照してください。