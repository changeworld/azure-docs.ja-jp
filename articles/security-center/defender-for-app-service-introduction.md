---
title: Azure Defender for App Service - 利点と機能
description: Azure Defender for App Service の利点と機能について説明します。
author: memildin
ms.author: memildin
ms.date: 9/22/2020
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: c53f7e712668f32766feaf76d6a08582bda9af22
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/29/2020
ms.locfileid: "91449099"
---
# <a name="introduction-to-azure-defender-for-app-service"></a>Azure Defender for App Service の概要

Azure App Service は、インフラストラクチャを管理せず、Web アプリや API をビルドし、ホストするためのフル マネージド プラットフォームです。 エンタープライズ クラスのパフォーマンス、セキュリティ、コンプライアンス要件を満たすための管理機能、監視機能、運用に関する分析情報を提供します。 詳細については、[Azure App Service](https://azure.microsoft.com/services/app-service/)に関するページを参照してください。

**Azure Defender for App Service** では、クラウドのスケールを使用して、App Service で実行されるアプリケーションをターゲットとした攻撃が識別されます。 攻撃者は、Web アプリケーションをプローブして弱点を発見し、悪用します。 Azure で実行されているアプリケーションに対する要求は、特定の環境にルーティングされる前に、複数のゲートウェイを通過し、そこで検査され、ログに記録されます。 その後、このデータは、悪用や攻撃者を特定し、後で使用される新しいパターンを学習するために使用されます。

Security Center では、Azure がクラウド プロバイダーとして備える可視性を使用して、App Service の内部ログが分析され、複数のターゲットに対する攻撃手法が識別されます。 たとえば、広範囲にわたるスキャンや分散型の攻撃などの手法です。 この種の攻撃は通常、IP の小さなサブセットから発生し、複数のホスト上の類似のエンドポイントをクロールするパターンを示します。 攻撃では脆弱なページやプラグインが検索され、単一のホストの観点からは攻撃を特定できません。


## <a name="availability"></a>可用性

|側面|詳細|
|----|:----|
|リリース状態:|プレビュー|
|価格:|[Azure Defender for App Service](azure-defender.md) は、[価格ページ](security-center-pricing.md)に記載されているように課金されます。|
|サポートされている App Service プラン:|![Yes](./media/icons/yes-icon.png) Basic、Standard、Premium、Isolated、または Linux<br>![No](./media/icons/no-icon.png) Free、Shared、または従量課金<br>[App Service プランの詳細情報](https://azure.microsoft.com/pricing/details/app-service/plans/)|
|クラウド:|![Yes](./media/icons/yes-icon.png) 商用クラウド<br>![No](./media/icons/no-icon.png) ナショナル/ソブリン (US Gov、China Gov、その他の Gov)|
|||

## <a name="what-does-azure-defender-for-app-service-protect"></a>Azure Defender for App Service の保護対象は何ですか?

App Service プランを有効にすると、Security Center では、App Service プランの対象となるリソースが評価され、その結果に基づき、セキュリティ上の推奨事項が生成されます。 Security Center では、App Service が実行されている VM インスタンスと管理インターフェイスが保護されます。 App Service で実行されているアプリとの間で送受信される要求と応答も監視されます。

Windows ベースの App Service プランを実行している場合、Security Center では、基になるサンド ボックスおよび VM にアクセスすることもできます。 前述のログ データと組み合わせることで、インフラストラクチャは、流行している新しい攻撃から、お客様のマシンの侵害まで、状況を通知できます。 したがって、Web アプリが悪用された後に Security Center がデプロイされたとしても、進行中の攻撃を検出できる可能性があります。


## <a name="protect-your-azure-app-service-web-apps-and-apis"></a>Azure App Service Web アプリと API を保護する
Azure Defender for App Service で Azure App Service プランを保護するには、次のようにします。

- 確実に専用のコンピューターに関連付けられた、サポートされている App Service プランを利用します。 サポートされているプランは、上記の「[可用性](#availability)」に記載されています。

- [Azure Security Center の価格](security-center-pricing.md)に関する記事で説明されているように、サブスクリプションで **Azure Defender** を有効にします (必要に応じて、**Azure Defender for App Service**プランのみを有効にできます)。

Security Center は App Service とネイティブで統合されており、デプロイやオンボーディングの必要がありません。統合は透過的となっています。

>[!NOTE]
> 価格と設定のページには、**リソース数量**に対するインスタンスの数が記載されています。 これは、このサブスクリプションのすべての App Service プランに含まれるコンピューティング リソースのうち、価格レベルのページを開いたときに実行されていたコンピューティング リソースの総数を表しています。
>
> Azure App Service にはさまざまなプランがあります。 App Service プランによって、Web アプリを実行するための一連のコンピューティング リソースが決まります。 そのようなリソースは、従来の Web ホスティングのサーバー ファームに相当します。 1 つまたは複数のアプリを同じコンピューティング リソース (または、同じ App Service プラン) で実行するように構成することができます。
>
>カウントを検証するには、Azure portal で "App Service プラン" に移動します。App Service プランでは、プラン別に使用されているコンピューティング リソースの数を確認できます。 



## <a name="next-steps"></a>次の手順

この記事では、Azure Defender for App Service について説明しました。 

関連資料については、次の記事をご覧ください。 

- アラートは、Security Center によって生成されたか、別のセキュリティ製品の Security Center によって受信されたかにかかわらず、エクスポートすることができます。 アラートを Azure Sentinel、サードパーティの SIEM、またはその他の外部ツールにエクスポートする場合は、[SIEM へのアラートのエクスポート](continuous-export.md)に関するページの手順に従ってください。
- Azure App Service アラートの一覧については、[アラートのリファレンス表](alerts-reference.md#alerts-azureappserv)に関するページを参照してください。
- App Service プランの詳細については、「[App Service プラン](https://azure.microsoft.com/pricing/details/app-service/plans/)」を参照してください。
- > [!div class="nextstepaction"]
    > [Azure Defender を有効にする](security-center-pricing.md)