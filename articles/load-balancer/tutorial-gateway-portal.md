---
title: 'チュートリアル: ゲートウェイ ロード バランサーを作成する - Azure portal'
titleSuffix: Azure Load Balancer
description: このチュートリアルでは、Azure portal を使用して、ゲートウェイ ロード バランサーを作成する方法を学習します。
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: tutorial
ms.date: 11/02/2021
ms.custom: template-tutorial, ignite-fall-2021
ms.openlocfilehash: 918f2da42a57752db2373e65665804d0a49ad31a
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131091948"
---
# <a name="tutorial-create-a-gateway-load-balancer-using-the-azure-portal"></a>チュートリアル: Azure portal を使用してゲートウェイ ロード バランサーを作成する

Azure Load Balancer は、Standard、Basic、および Gateway SKU で構成されます。 Azure Gateway Load Balancer は、ネットワーク仮想アプライアンス (NVA) の透過的な挿入に使用されます。 ハイ パフォーマンスと NVA の高いスケーラビリティを必要とするシナリオには、Azure Gateway Load Balancer を使用します。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * プレビュー機能を登録します。
> * 仮想ネットワークを作成します。
> * ネットワーク セキュリティ グループを作成します。
> * ゲートウェイ ロード バランサーを作成します。
> * ロード バランサー フロントエンドをゲートウェイ ロード バランサーにチェーンします。

> [!IMPORTANT]
> Gateway Azure Load Balancer は、現在パブリック プレビューの段階にあります。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- 既存のパブリック Standard SKU Azure Load Balancer。 ロード バランサーの作成の詳細については、 **[Azure portal を使用したパブリック ロード バランサーの作成](quickstart-load-balancer-standard-public-portal.md)** に関する記事を参照してください。
    - このチュートリアルの目的のため、例のロード バランサーの名前は **myLoadBalancer** です。

## <a name="register-preview-feature"></a>プレビュー機能を登録する

パブリック プレビューの一環として、プロバイダーを Azure サブスクリプションに登録する必要があります。 サブスクリプションを有効にするには、次の PowerShell または Azure CLI の例を使用します。

### <a name="powershell"></a>PowerShell

次のように [Register-AzProviderFeature](/powershell/module/az.resources/register-azproviderfeature) を使用して、**AllowGatewayLoadBalancer** プロバイダー機能を登録します。

```azurepowershell-interactive
Register-AzProviderFeature -ProviderNamespace Microsoft.Network -FeatureName AllowGatewayLoadBalancer

```

次のように [Register-AzResourceProvider](/powershell/module/az.resources/register-azresourceprovider) を使用して、**Microsoft.Network** リソース プロバイダーを登録します。

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Network

```

### <a name="azure-cli"></a>Azure CLI

次のように [az feature register](/cli/azure/feature#az_feature_register) を使用して、**AllowGatewayLoadBalancer** プロバイダー機能を登録します。

```azurecli-interactive
  az feature register \
    --name AllowGatewayLoadBalancer \
    --namespace Microsoft.Network
```

次のように [az provider register](/cli/azure/provider#az_provider_register) を使用して、**Microsoft.Network** リソース プロバイダーを登録します。

```azurecli-interactive
  az provider register \
    --namespace Microsoft.Network
```

## <a name="sign-in-to-azure"></a>Azure へのサインイン

Azure Portal [https://preview.portal.azure.com](https://preview.portal.azure.com) にサインインします。

## <a name="create-virtual-network"></a>Create virtual network

ゲートウェイ ロード バランサーのバックエンド プールにあるリソースには、仮想ネットワークが必要です。 

1. ポータルの上部にある検索ボックスに、「**仮想ネットワーク**」と入力します。 検索結果で、 **[仮想ネットワーク]** を選択します。

2. **[仮想ネットワーク]** で **[+ 作成]** を選択します。

3. **[仮想ネットワークの作成]** の **[基本]** タブで次の情報を入力または選択します。

    | **設定**          | **Value**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **プロジェクトの詳細**  |                                                                 |
    | サブスクリプション     | Azure サブスクリプションを選択します。                                  |
    | リソース グループ   | **[新規作成]** を選択します。 </br> **[名前]** に「**TutorGwLB-rg**」と入力します。 </br> **[OK]** を選択します。 |
    | **インスタンスの詳細** |                                                                 |
    | 名前             | 「**myVNet**」と入力します                                    |
    | リージョン           | **[米国東部]** を選択します。 |

4. **[IP アドレス]** タブを選択するか、ページの下部にある **[Next: IP Addresses]\(次へ: IP アドレス\)** ボタンを選択します。

5. **[IP アドレス]** タブで、次の情報を入力します。

    | 設定            | 値                      |
    |--------------------|----------------------------|
    | IPv4 アドレス空間 | 「**10.1.0.0/16**」と入力します。 |

6. **[サブネット名]** で、 **[default]\(既定\)** という単語を選択します。

7. **[サブネットの編集]** に次の情報を入力します。

    | 設定            | 値                      |
    |--------------------|----------------------------|
    | サブネット名 | 「**myBackendSubnet**」と入力します |
    | サブネットのアドレス範囲 | 「**10.1.0.0/24**」と入力します。 |

8. **[保存]** を選択します。

9. **[セキュリティ]** タブをクリックします。

10. **[BastionHost]** で **[有効にする]** を選択します。 この情報を入力します。

    | 設定            | 値                      |
    |--------------------|----------------------------|
    | 要塞名 | 「**myBastionHost**」と入力します |
    | AzureBastionSubnet のアドレス空間 | 「**10.1.1.0/27**」 と入力します |
    | パブリック IP アドレス | **[新規作成]** を選択します。 </br> **[名前]** に「**myBastionIP**」と入力します。 </br> **[OK]** を選択します。 |


11. **[確認と作成]** タブを選択するか、 **[確認と作成]** ボタンを選択します。

12. **［作成］** を選択します

## <a name="create-nsg"></a>Create NSG

次の例を使用して、ネットワーク セキュリティ グループを作成します。 前に作成した仮想ネットワーク内のネットワーク トラフィックに、必要な NSG ルールを構成します。

1. ポータルの上部にある検索ボックスに、「**ネットワーク セキュリティ**」と入力します。 検索結果から **[ネットワーク セキュリティ グループ]** を選択します。

2. **[+ 作成]** を選択します。

3. **[ネットワーク セキュリティ グループの作成]** の **[基本]** タブで、次の情報を入力または選択します。

    | 設定 | 値 |
    | ------- | ----- |
    | **プロジェクトの詳細** |   |
    | サブスクリプション | サブスクリプションを選択します。 |
    | Resource group | **[TutorGwLB-rg]** を選択します |
    | **インスタンスの詳細** |   |
    | 名前 | 「**myNSG**」と入力します。 |
    | リージョン | **[米国東部]** を選択します。 |

4. **[確認と作成]** タブを選択するか、 **[確認と作成]** ボタンを選択します。

5. **［作成］** を選択します

6. ポータルの上部にある検索ボックスに、「**ネットワーク セキュリティ**」と入力します。 検索結果から **[ネットワーク セキュリティ グループ]** を選択します。

7. **[myNSG]** を選択します。

8. **[myNSG]** の **[設定]** で、 **[受信セキュリティ規則]** を選択します。

9. **[+ 追加]** を選択します。

10. **[受信セキュリティ規則の追加]** で、次の情報を入力または選択します。

    | 設定 | 値 |
    | ------- | ----- |
    | source | 既定値の **[Any]\(すべて\)** のままにします。 |
    | Source port ranges | 既定値の **'*'** のままにします。 |
    | 到着地 | 既定値の **[Any]\(すべて\)** のままにします。 |
    | サービス | 既定値の **[Custom]\(カスタム\)** のままにします。 |
    | 宛先ポート範囲 | **'*'** と入力します。 |
    | Protocol | **[任意]** をクリックします。 |
    | アクション | 既定値の **[Allow]\(許可\)** のままにします。 |
    | 優先度 | 「**100**」と入力します。 | 
    | 名前 | 「**myNSGRule-AllowAll-All**」と入力します |

11. **[追加]** を選択します。

12. **[設定]** で **[送信セキュリティ規則]** を選択します。

13. **[+ 追加]** を選択します。

14. **[送信セキュリティ規則の追加]** で、次の情報を入力または選択します。

    | 設定 | 値 |
    | ------- | ----- |
    | source | 既定値の **[Any]\(すべて\)** のままにします。 |
    | Source port ranges | 既定値の **'*'** のままにします。 |
    | 到着地 | 既定値の **[Any]\(すべて\)** のままにします。 |
    | サービス | 既定値の **[Custom]\(カスタム\)** のままにします。 |
    | 宛先ポート範囲 | **'*'** と入力します。 |
    | Protocol | **[TCP]** を選択します。 |
    | アクション | 既定値の **[Allow]\(許可\)** のままにします。 |
    | 優先度 | 「**100**」と入力します。 | 
    | 名前 | 「**myNSGRule-AllowAll-TCP-Out**」と入力します |

15. **[追加]** を選択します。

NVA をデプロイのために作成しているとき、この NSG を選択します。

## <a name="create-gateway-load-balancer"></a>Azure Gateway Load Balancer を作成する

このセクションでは、構成を作成し、ゲートウェイ ロード バランサーをデプロイします。 

1. ポータルの上部にある検索ボックスに、「**ロード バランサー**」と入力します。 検索結果で **[ロード バランサー]** を選択します。

2. **[ロード バランサー]** ページで、 **[作成]** を選択します。

3. **[ロード バランサーの作成]** ページの **[基本]** タブで、次の情報を入力または選択します。 

    | 設定                 | 値                                              |
    | ---                     | ---                                                |
    | **プロジェクトの詳細** |   |
    | サブスクリプション               | サブスクリプションを選択します。    |    
    | Resource group         | **[TutorGwLB-rg]** を選択します。 |
    | **インスタンスの詳細** |   |
    | 名前                   | 「**myLoadBalancer-gw**」と入力します                                   |
    | リージョン         | **[(米国) 米国東部]** を選択します。                                        |
    | Type          | **[内部]** を選択します。                                        |
    | SKU           | **[Gateway]** を選択します。 |

    :::image type="content" source="./media/tutorial-gateway-portal/create-load-balancer.png" alt-text="Standard ロード バランサーを作成する [基本] タブのスクリーンショット。" border="true":::

4. ページ下部にある **[次へ: フロントエンド IP の構成]** を選択します。

5. **[フロントエンド IP 構成]** で、 **[+ フロントエンド IP の追加]** を選択します。

6. **[名前]** に「**MyFrontEnd**」と入力します。

7. **[サブネット]** で **[myBackendSubnet]** を選択します。

8. **[割り当て]** で **[動的]** を選択します。

9. **[追加]** を選択します。

10. ページ下部で **[次へ: バックエンド プール]** を選択します。

11. **[バックエンド プール]** タブで、 **[+ バックエンド プールの追加]** を選択します。

12. **[バックエンド プールの追加]** で、次の情報を入力または選択します。

    | 設定 | [値] |
    | ------- | ----- |
    | 名前 | 「**myBackendPool**」と入力します。 |
    | バックエンド プールの構成 | **[NIC]** を選択します。 |
    | IP バージョン | **[IPv4]** を選択します。 |
    | **ゲートウェイ ロード バランサーの構成** |   |
    | Type | **[内部と外部]** を選択します。 |
    | 内部ポート | 既定値の **10800** のままにします。 |
    | 内部識別子 | 既定値の **800** のままにします。 |
    | 外部ポート | 既定値の **10801** のままにします。 |
    | 外部識別子 | 既定値の **801** のままにします。 |

13. **[追加]** を選択します。

14. ページ下部にある **[次へ: 受信規則]** ボタンを選択します。

15. **[受信規則]** タブの **[負荷分散規則]** で、 **[+ 負荷分散規則の追加]** を選択します。

16. **[負荷分散規則の追加]** で、次の情報を入力または選択します。

    | 設定 | [値] |
    | ------- | ----- |
    | 名前 | 「**myLBRule**」と入力します |
    | IP バージョン | 要件に応じて、 **[IPv4]** または **[IPv6]** を選択します。 |
    | フロントエンド IP アドレス | **[MyFrontend]** を選択します。 |
    | バックエンド プール | **[myBackendPool]** を選択します。 |
    | 正常性プローブ | **[新規作成]** を選択します。 </br> **[名前]** に、「**myHealthProbe**」と入力します。 </br> **[プロトコル]** で、 **[HTTP]** を選択します。 </br> 残りの部分は既定値のままにし、 **[OK]** を選択します。 |
    | セッション永続化 | **[なし]** を選択します。 |

    :::image type="content" source="./media/tutorial-gateway-portal/add-load-balancing-rule.png" alt-text="[負荷分散規則の作成] のスクリーンショット。" border="true":::

17. **[追加]** を選択します。

18. ページ下部にある青色の **[確認と作成]** ボタンを選択します。

19. **［作成］** を選択します

## <a name="add-network-virtual-appliances-to-the-gateway-load-balancer-backend-pool"></a>ネットワーク仮想アプライアンスをバックエンド プールの Azure Gateway Load Balancer に追加する
Azure Marketplace を介して NVA をデプロイします。 デプロイされたら、Gateway Load Balancer の [バックエンド プール] タブに移動して NVA 仮想マシンをバックエンド プールに追加します。

## <a name="chain-load-balancer-frontend-to-gateway-load-balancer"></a>ロード バランサー フロントエンドをゲートウェイ ロード バランサーにチェーンする

この例では、Standard ロード バランサーのフロントエンドをゲートウェイ ロード バランサーにチェーンします。 

サブスクリプション内の既存のロード バランサーのフロントエンド IP に、フロントエンドを追加します。

1. Azure portal の検索ボックスに、「**ロード バランサー**」と入力します。 検索結果で **[ロード バランサー]** を選択します。

2. **[ロード バランサー]** で、 **[myLoadBalancer]** または既存のロード バランサー名を選択します。

3. ロード バランサー ページの **[設定]** で、 **[フロントエンド IP の構成]** を選択します。

4. ロード バランサーのフロントエンド IP を選択します。 この例では、フロントエンドの名前は **myFrontendIP** です。

    :::image type="content" source="./media/tutorial-gateway-portal/frontend-ip.png" alt-text="フロントエンド IP 構成のスクリーンショット。" border="true":::

5. **ゲートウェイのロード バランサー** の横にあるプルダウン ボックスで **[myFrontendIP (10.1.0.4)]** を選択します。

6. <bpt id="p1">**</bpt>[保存]<ept id="p1">**</ept> を選択します。

    :::image type="content" source="./media/tutorial-gateway-portal/select-gateway-load-balancer.png" alt-text="フロントエンド IP にゲートウェイ ロード バランサーを追加したスクリーンショット。" border="true":::


## <a name="clean-up-resources"></a>リソースをクリーンアップする

リソース グループ、ロード バランサー、および関連するすべてのリソースは、不要になったら削除します。 これを行うには、リソースを含むリソース グループ (**TutorGwLB-rg**) を選択し、 **[削除]** を選択します。

## <a name="next-steps"></a>次のステップ

Azure でネットワーク仮想アプライアンスを作成します。 

NVA を作成する場合は、このチュートリアルで作成したリソースを選択します。

* 仮想ネットワーク

* Subnet

* ネットワーク セキュリティ グループ

* ゲートウェイ ロード バランサー

次の記事に進んで、リージョン間 Azure Load Balancer の作成方法を学習してください。
> [!div class="nextstepaction"]
> [リージョン間のロード バランサー](tutorial-cross-region-powershell.md)
