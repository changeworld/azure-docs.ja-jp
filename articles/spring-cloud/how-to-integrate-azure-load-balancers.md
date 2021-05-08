---
title: チュートリアル - Azure Spring Cloud と Azure の負荷分散ソリューションを統合する
description: Azure Spring Cloud と Azure の負荷分散ソリューションを統合する方法
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 04/20/2020
ms.custom: devx-track-java
ms.openlocfilehash: 50b09fd82461221ae6cd008f6918ac2f3a26fd94
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2021
ms.locfileid: "104877420"
---
# <a name="integrate-azure-spring-cloud-with-azure-load-balance-solutions"></a>Azure Spring Cloud と Azure の負荷分散ソリューションを統合する

**この記事の適用対象:** ✔️ Java ✔️ C#

Azure Spring Cloud では、Azure 上のマイクロサービスがサポートされています。  ビジネスを拡大するには、複数のデータ センターで Azure Spring Cloud の複数のインスタンスの管理が必要になる場合があります。

Azure には、さまざまな負荷分散ソリューションが既に用意されています。 Azure Spring Cloud と Azure の負荷分散ソリューションを統合するには、次の 3 つのオプションがあります。

1.  Azure Spring Cloud と Azure Traffic Manager を統合する
2.  Azure Spring Cloud と Azure App Gateway を統合する
3.  Azure Spring Cloud と Azure Front Door を統合する

## <a name="prerequisites"></a>前提条件

* Azure Spring Cloud:[ Azure Spring Cloud サービスを作成する方法](./spring-cloud-quickstart.md)
* Azure Traffic Manager:[Traffic Manager を作成する方法](../traffic-manager/quickstart-create-traffic-manager-profile.md)
* Azure App Gateway:[アプリケーション ゲートウェイを作成する方法](../application-gateway/quick-create-portal.md)
* Azure Front Door:[フロント ドアを作成する方法](../frontdoor/quickstart-create-front-door.md)

## <a name="integrate-azure-spring-cloud-with-azure-traffic-manager"></a>Azure Spring Cloud と Azure Traffic Manager を統合する

Azure Spring Cloud を Traffic Manager と統合するには、そのパブリック エンドポイントを Traffic Manager のエンドポイントとして追加し、Traffic Manager と Azure Spring Cloud の両方にカスタム ドメインを構成します。

### <a name="add-endpoint-in-traffic-manager"></a>Traffic Manager でエンドポイントを追加する
Traffic Manager でエンドポイントを追加します。
1.  **[タイプ]** に *[外部エンドポイント]* を指定します。
1.  各 Azure Spring Cloud パブリック エンドポイントの完全修飾ドメイン名 (FQDN) を入力します。
1. **[OK]** をクリックします。

    ![Traffic Manager 1](media/spring-cloud-load-balancers/traffic-manager-1.png) ![Traffic Manager 2](media/spring-cloud-load-balancers/traffic-manager-2.png)

### <a name="configure-custom-domain"></a>カスタム ドメインを構成する
構成を終了するには:
1.  ドメイン プロバイダーの Web サイトにサインインし、カスタム ドメインから Traffic Manager の Azure の既定のドメイン名への CNAME レコード マッピングを作成します。
1.  [カスタム ドメインを Azure Spring Cloud に追加する方法](spring-cloud-tutorial-custom-domain.md)の手順に従います。
1. Traffic Manager への上記のカスタム ドメイン バインディングを Azure Spring Cloud 対応のアプリ サービスに追加し、そこに SSL 証明書をアップロードします。

    ![Traffic Manager 3](media/spring-cloud-load-balancers/traffic-manager-3.png)

## <a name="integrate-azure-spring-cloud-with-azure-app-gateway"></a>Azure Spring Cloud と Azure App Gateway を統合する

Azure Spring Cloud サービスと統合するには、次の構成を完了します。

### <a name="configure-backend-pool"></a>バックエンド プールを構成する
1. **[ターゲットの種類]** に *[IP アドレス* または *FQDN]* を指定します。
1. Azure Spring Cloud のパブリック エンドポイントを入力します。

    ![App Gateway 1](media/spring-cloud-load-balancers/app-gateway-1.png)

### <a name="add-custom-probe"></a>カスタム プローブを追加する
1. **[正常性プローブ]** 、 **[追加]** の順に選択して、カスタムの **[プローブ]** ダイアログを開きます。 
1. 重要なのは、 **[ホスト名をバックエンド HTTP 設定から選択します]** オプションに *[はい]* を選択することです。

    ![App Gateway 2](media/spring-cloud-load-balancers/app-gateway-2.png)

### <a name="configure-http-setting"></a>HTTP 設定を構成する
1.  **[HTTP 設定]** 、 **[追加]** の順に選択して、HTTP 設定を追加します。
1.  **[新しいホスト名でオーバーライドする]:** *[はい]* を選択します。
1.  **[ホスト名をオーバーライドする]** : **[バックエンド ターゲットからホスト名を選択する]** を選択します。
1.  **[カスタム プローブの使用]** : *[はい]* を選択し、上で作成したカスタム プローブを選択します。

    ![App Gateway 3](media/spring-cloud-load-balancers/app-gateway-3.png)

### <a name="configure-rewrite-set"></a>書き換えセットを構成する
1.  **[書き換え]** 、 **[書き換えセット]** の順に選択し、書き換えセットを追加します。
1.  Azure Spring Cloud パブリック エンドポイントに要求をルーティングするルーティング規則を選択します。
1.  **[書き換えルールの構成]** タブで、 **[書き換え規則の追加]** を選択します。
1.  **[書き換えの種類]** : **[要求ヘッダー]** を選択します
1.  **[アクションの種類]** : **[削除]** を選択します
1.  **[ヘッダー名]** : **[共通ヘッダー]** を選択します
1.  **[共通ヘッダー]** : **[X-Forwarded-Proto]** を選択します

    ![App Gateway 4](media/spring-cloud-load-balancers/app-gateway-4.png)

## <a name="integrate-azure-spring-cloud-with-azure-front-door"></a>Azure Spring Cloud と Azure Front Door を統合する

Azure Spring Cloud サービスと統合し、バックエンド プールを構成するには、以下を実行します。 
1. **バックエンド プールを追加します**。
1. ホストを追加してバックエンド エンドポイントを指定します。

    ![フロント ドア 1](media/spring-cloud-load-balancers/front-door-1.png)

1.  **[バックエンド ホストの種類]** に *[カスタム ホスト]* を指定します。
1.  **[バックエンド ホスト名]** に、Azure Spring Cloud パブリック エンドポイントの FQDN を入力します。
1.  **[バックエンド ホスト ヘッダー]** は、既定値 (**バックエンド ホスト名** と同じ) をそのまま使用します。

    ![フロント ドア 2](media/spring-cloud-load-balancers/front-door-2.png)

## <a name="next-steps"></a>次のステップ
* [Traffic Manager を作成する方法](../traffic-manager/quickstart-create-traffic-manager-profile.md)
* [アプリケーション ゲートウェイを作成する方法](../application-gateway/quick-create-portal.md)
* [フロント ドアを作成する方法](../frontdoor/quickstart-create-front-door.md)