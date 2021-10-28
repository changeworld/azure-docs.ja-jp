---
title: Azure Application Gateway を使用してパブリック IP アドレスを管理する
titleSuffix: Azure Virtual Network
description: Azure Application Gateway と共にパブリック IP アドレスを使用する方法と、構成を変更して管理する方法について説明します。
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: how-to
ms.date: 06/28/2021
ms.custom: template-how-to
ms.openlocfilehash: c700ca1bdd1e596d3c393f1a857adba81116b576
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130248984"
---
# <a name="manage-a-public-ip-address-with-an-azure-application-gateway"></a>Azure Application Gateway を使用してパブリック IP アドレスを管理する

Azure Application Gateway は、Web アプリケーションへのトラフィックを管理する Web トラフィック ロード バランサーです。 Application Gateway では、HTTP 要求の属性に基づいてルーティングを決定できます。 属性の例として、URI パスやホスト ヘッダーなどがあります。  Application Gateway のフロントエンドは、バックエンド プール内のアプリケーションの接続ポイントです。 

Application Gateway のフロントエンドは、プライベート IP アドレス、パブリック IP アドレス、またはその両方です。  Application Gateway の V1 SKU では、Basic パブリック IP (静的または動的) がサポートされています。  V2 SKU では、静的な Standard SKU パブリック IP のみがサポートされています。 Application Gateway V2 SKU では、内部 IP アドレスはフロントエンドのみのため、サポートされていません。  詳細については、「[Application Gateway のフロントエンド IP アドレスの構成](../../application-gateway/configuration-front-end-ip.md)」を参照してください。  

この記事では、サブスクリプション内の既存のパブリック IP を使用して Application Gateway を作成する方法について説明します。 

## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料で作成できます](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。
- サブスクリプション内の 2 つの Standard SKU のパブリック IP アドレス。 この IP アドレスは、どのリソースにも関連付けることができません。 Standard SKU のパブリック IP アドレスの作成の詳細については、[パブリック IP の作成 - Azure portal](./create-public-ip-portal.md) に関するページを参照してください。
    - この記事の例では、新しいパブリック IP アドレスに **myStandardPublicIP-1** および **myStandardPublicIP-2** という名前を付けます。

## <a name="create-application-gateway-existing-public-ip"></a>Application Gateway の既存のパブリック IP を作成する

このセクションでは、Application Gateway リソースを作成します。 前提条件で作成した IP アドレスを Application Gateway のパブリック IP として選択します。

1. [Azure portal](https://portal.azure.com) にサインインします。

2. ポータルの上部にある検索ボックスに、「**Application gateway**」と入力します。

3. 検索結果から **[Application gateways]** を選択します。

4. **[+ 作成]** を選択します。

5. **[アプリケーション ゲートウェイの作成]** で、次の情報を入力または選択します。

    | 設定 | 値 |
    | ------- | ----- |
    | **プロジェクトの詳細** |   |
    | サブスクリプション | サブスクリプションを選択します。 |
    | Resource group | **[新規作成]** を選択します。<ul><li>「**myResourceGroupAppGW**」と入力します。</li></ul>**[OK]** を選択します。 |
    | **インスタンスの詳細** |   |
    | アプリケーション ゲートウェイ名 | 「**myAppGateway**」と入力します。 |
    | リージョン | **[(米国) 米国西部 2]** を選択します。 |
    | 階層 | 既定値である **[Standard V2]** のままにします。 |
    | 自動スケーリングを有効にする | 既定値の **[はい]** のままにします。 |
    | 最小インスタンス数 | 既定値の **[0]** のままにします。 |
    | 最大インスタンス数 | 既定値の **10** のままにします。 |
    | 可用性ゾーン | 既定値の **[なし]** のままにします。 |
    | HTTP2 | 既定値の **[無効]** のままにします。 |
    | 仮想ネットワーク | **[新規作成]** を選択します。 <ul><li>**[仮想ネットワークの作成]** で、名前として「**myVNet**」と入力します。</li><li>**[アドレス空間]** を既定のアドレス空間のままにします。</li><li>**[サブネット]** で、**既定値** を **[myAGSubnet]** に変更します。</li><li>2 つ目のサブネット名に、「**myBackendSubnet**」と入力します。</li><li>**[アドレス範囲]** に、既定のアドレス空間内の範囲を入力します。</li></ul>**[OK]** を選択します。|

6. **[次へ: フロントエンド]** を選択します。

7. **[フロントエンド]** タブの **[パブリック IP アドレス]** で、 **[myStandardPublicIP-1]** またはお使いのパブリック IP アドレスを選択します。

8. **バックエンド** を選択します。 

9. **バックエンド プールの追加** を選択します。

10. **[バックエンド プールの追加]** で、名前として「**myBackendPool**」と入力します。

11. **[Next:構成]** を選択します。

12. **[+ ルーティング規則の追加]** を選択します。 次の情報を入力または選択します。

    | 設定 | 値 |
    | ------- | ----- |
    | 規則の名前 | 「**myRoutingRule**」と入力します。 |
    | **リスナー** |    |
    | リスナー名 | 「**myListener**」と入力します。 |
    | フロントエンド IP | **[パブリック]** を選択します。 |
    | フロントエンド IP プロトコル | 既定値の **[HTTP]** のままにします。 |
    | Port | 既定値の **[80]** のままにします。 |
    | **追加設定** |   |
    | リスナーの種類 | 既定値の **[Basic]** のままにします。 |
    | エラー ページの URL | 既定値の **[いいえ]** のままにします。 |
    | **バックエンド ターゲット** |    |
    | 変換後の型 | 既定値の **[バックエンド プール]** のままにします。 |
    | バックエンド ターゲット | **[myBackendPool]** を選択します。 |
    | HTTP 設定 | **[新規追加]** を選択します。<ul><li>**[名前]** に「**myHTTPsetting**」と入力します。</li><li>その他の設定は、既定値のままにします。</li></ul>**[追加]** を選択します。|

13. **[追加]** を選択します。

14. **[次へ: タグ]** 、 **[次へ: 確認と作成]** の順に選択します。

15. **［作成］** を選択します

> [!NOTE]
> これは、Application Gateway の簡単なデプロイです。 高度な構成とセットアップについては、「[クイック スタート:Azure Application Gateway による Web トラフィックのルーティング - Azure portal](../../application-gateway/quick-create-portal.md)」を参照してください
>
> Application Gateway の詳細については、「[Azure Application Gateway とは](../../application-gateway/overview.md)」を参照してください

## <a name="change-or-remove-public-ip-address"></a>パブリック IP アドレスを変更または削除する

Application Gateway では、作成後のパブリック IP アドレスの変更はサポートされていません。

## <a name="more-information"></a>詳細情報

* 動的 Basic SKU IP は、Application Gateway のフロントエンドに関連付けられている場合、ゲートウェイが停止または起動したときにのみ変更されます。 Application Gateway のフロントエンドに関連付けられている DNS 名は変更されません。 

## <a name="caveats"></a>注意事項

* 現時点では、パブリック IPv6 アドレスは Application Gateway でサポートされていません。  

## <a name="next-steps"></a>次のステップ

この記事では、Application Gateway を作成し、既存のパブリック IP を使用する方法について説明しました。 

- Azure Virtual Network NAT の詳細については、[Azure Virtual Network NAT の概要](../nat-gateway/nat-overview.md)ページを参照してください。
- Azure のパブリック IP アドレスの詳細については、「[パブリック IP アドレス](./public-ip-addresses.md)」を参照してください。