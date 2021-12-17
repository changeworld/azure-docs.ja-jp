---
title: 'チュートリアル: 仮想マシンの両方のルーティング優先順位オプションを構成する - Azure portal'
titlesuffix: Azure Virtual Network
description: このチュートリアルを使用して、Azure portal を使用して仮想マシンの両方のルーティング優先順位オプションを構成する方法を学習します。
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: tutorial
ms.date: 10/01/2021
ms.custom: template-tutorial
ms.openlocfilehash: d11e77725dfb99060c96f62233833330867f1c0d
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/01/2021
ms.locfileid: "129369209"
---
# <a name="tutorial-configure-both-routing-preference-options-for-a-virtual-machine-using-the-azure-portal"></a>チュートリアル: Azure portal を使用して仮想マシンの両方のルーティング優先順位オプションを構成する

この記事では、仮想マシン (VM) の両方の[ルーティング優先設定](routing-preference-overview.md)オプション (インターネットと Microsoft グローバル ネットワーク) を構成する方法について説明します。 この構成は、2 つの仮想ネットワーク インターフェイスを使用して実現されます。 一方のネットワーク インターフェイスは、Microsoft グローバル ネットワーク経由でルーティングされるパブリック IP を使用して構成されます。 もう一方のネットワーク インターフェイスは、ISP ネットワーク経由でルーティングされるパブリック IP を使用して構成されます。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * **Microsoft ネットワーク** のルーティング優先順位を使用して、パブリック IP アドレスを持つ仮想マシンを作成する。
> * **インターネット** ルーティング優先順位を使用してパブリック IP アドレスを作成する。
> * 仮想マシン用のセカンダリ ネットワーク インターフェイスを作成する。
> * **インターネット** ルーティング優先順位のパブリック IP を仮想マシンのセカンダリ ネットワーク インターフェイスに関連付ける。
> * セカンダリ ネットワーク インターフェイスを仮想マシンに接続する。

## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="create-the-virtual-machine"></a>仮想マシンの作成

このセクションでは、仮想マシンとパブリック IP アドレスを作成します。 パブリック IP アドレスの構成中に、ルーティング優先順位で **[Microsoft ネットワーク]** を選択します。

1. [Azure portal](https://portal.azure.com) にサインインします。

2. portal の検索ボックスに、「**仮想マシン**」と入力します。 検索結果から **[仮想マシン]** を選択します。

3. **[仮想マシン]** で、 **[+ 作成]** 、 **[+ 仮想マシン]** の順に選択します。

4. **[仮想マシンの作成]** の **[基本]** タブで、次の情報を入力または選択します。

    | 設定 | 値 |
    | ------- | ----- |
    | **プロジェクトの詳細** |   |
    | サブスクリプション | サブスクリプションを選択します。 |
    | Resource group | **[新規作成]** を選択します。 </br> 「**TutorVMMixRoutePref-rg**」と入力します。 **[OK]** を選択します。 |
    | **インスタンスの詳細** |   |
    | 仮想マシン名 | 「**myVM**」と入力します。 |
    | リージョン | **[(米国) 米国西部 2]** を選択します。 |
    | 可用性のオプション | **[インフラストラクチャ冗長は必要ありません]** を選択します。 |
    | Image | **[Windows Server 2019 Datacenter - Gen2]** を選択します。 |
    | Azure Spot インスタンス | 既定値のオフのままにします。 |
    | サイズ | サイズを選択します。 |
    | **管理者アカウント** |   |
    | ユーザー名 | ユーザー名を入力します。 |
    | Password | パスワードを入力します。 |
    | パスワードの確認 | パスワードを再入力します。 |
    | **受信ポートの規則** |
    | パブリック受信ポート | **[選択したポートを許可する]** を選択します。 |
    | 受信ポートの選択 | 既定値の **[RDP (3389)]** のままにします。 </br> "_**運用環境のワークロードでは、インターネットからポート 3389 を開くことはお勧めしません。**_" |

    :::image type="content" source="./media/routing-preference-mixed-network-adapter-portal/create-virtual-machine.png" alt-text="仮想マシンの作成のスクリーンショット。":::

5. **[次へ: ディスク]** 、 **[次へ: ネットワーク]** の順に選択するか、 **[ネットワーク]** タブを選択します。

6. [ネットワーク] タブで、次の情報を入力または選択します。

    | 設定 | 値 |
    | ------- | ----- |
    | **ネットワーク インターフェイス** |   |
    | 仮想ネットワーク | 既定値の **[(新規) TutorVMMixRoutePref-rg-vnet]** のままにします。 |
    | Subnet | 既定値の **(新規) 既定 (10.1.0.0/24)** のままにします。 |
    | パブリック IP | **[新規作成]** を選択します。 </br> **[名前]** に「**myPublicIP**」と入力します。 </br> **[SKU]** で **[Standard]** を選択します。 </br> **[ルーティングの優先順位]** で、 **[Microsoft ネットワーク]** を選択します。 </br> **[OK]** を選択します。 |

    :::image type="content" source="./media/routing-preference-mixed-network-adapter-portal/create-public-ip-ms-rp.png" alt-text="Microsoft ルーティング優先順位を使用したパブリック IP アドレスの作成のスクリーンショット。":::

7. **[Review + create]\(レビュー + 作成\)** を選択します。

8. **［作成］** を選択します

## <a name="create-the-public-ip-address"></a>パブリック IP アドレスの作成

このセクションでは、**インターネット** ルーティング優先順位を使用してパブリック IP アドレスを作成します。

1. portal の検索ボックスに「**パブリック IP アドレス**」と入力します。 検索結果から **[パブリック IP アドレス]** を選択します。

2. **[+ 作成]** を選択します。

3. **[パブリック IP アドレスの作成]** で、次の情報を入力または選択します。

    | 設定 | 値 |
    | ------- | ----- |
    | IP バージョン | 既定値の **[IPv4]** のままにします。 |
    | SKU | 規定値である **[標準]** のままにします。 |
    | レベル | 既定値の **[地域]** のままにします。 |
    | **IPv4 IP アドレスの構成** |   |
    | 名前 | 「**myPublicIP-IR**」と入力します。 |
    | ルーティング設定 | **[インターネット]** を選択します。 |
    | サブスクリプション | サブスクリプションを選択します。 |
    | Resource group | **[TutorVMMixRoutePref-rg]** を選択します。 |
    | 場所 | **[米国西部 2]** を選択します。 |

    :::image type="content" source="./media/routing-preference-mixed-network-adapter-portal/create-public-ip-internet-rp.png" alt-text="インターネット ルーティング優先順位を使用したパブリック IP アドレスの作成のスクリーンショット。":::

4. **［作成］** を選択します

## <a name="create-the-secondary-nic"></a>セカンダリ NIC を作成する

このセクションでは、前に作成した仮想マシンのセカンダリ ネットワーク インターフェイスを作成します。

1. portal の検索ボックスに「**ネットワーク インターフェイス**」と入力します。 検索結果から **[ネットワーク インターフェイス]** を選択します。

2. **[+ 作成]** を選択します。

3. **[ネットワーク インターフェイスの作成]** で、次の情報を入力または選択します。

    | 設定 | 値 |
    | ------- | ----- |
    | **プロジェクトの詳細** |   |
    | サブスクリプション | サブスクリプションを選択します。 |
    | Resource group | **[TutorVMMixRoutePref-rg]** を選択します。 |
    | 名前 | 「**myVMNic2**」と入力します。 |
    | リージョン | **[米国西部 2]** を選択します。 |
    | 仮想ネットワーク | **[TutorVMMixRoutePref-rg-vnet]** を選択します。 |
    | Subnet | **[TutorVMMixRoutePref-rg-vnet/default (10.1.0.0/24)]** を選択します。 |
    | ネットワーク セキュリティ グループ | **[myVM-nsg]** を選択します。 |

    :::image type="content" source="./media/routing-preference-mixed-network-adapter-portal/create-network-interface.png" alt-text="セカンダリ ネットワーク インターフェイスの作成のスクリーンショット。":::

4. **[確認と作成]** タブを選択するか、ページ下部にある **[確認と作成]** ボタンを選択します。

5. **[作成]** を選択します。

## <a name="associate-the-public-ip-address-with-secondary-nic"></a>パブリック IP アドレスをセカンダリ NIC に関連付ける

このセクションでは、前に作成した **インターネット** ルーティング優先順位のパブリック IP アドレスを、前のセクションで作成したネットワーク インターフェイスに関連付けます。

1. portal の検索ボックスに「**パブリック IP アドレス**」と入力します。 検索結果から **パブリック IP アドレス** を選択します。

2. **[myPublicIP-IR]** を選択します。

3. **myPublic-IR** の **[概要]** ページで、 **[関連付け]** を選択します。

    :::image type="content" source="./media/routing-preference-mixed-network-adapter-portal/associate-public-ip.png" alt-text="[関連付け] ボタンのある myPublicIP-IR の [概要] ページのスクリーンショット。":::

4. **[パブリック IP アドレスを関連付ける]** の **[リソースの種類]** プルダウン ボックスで **[ネットワーク インターフェイス]** を選択します。

5. **[ネットワーク インターフェイス]** プルダウン ボックスで、 **[myVMNic2]** を選択します。

    :::image type="content" source="./media/routing-preference-mixed-network-adapter-portal/select-ip-association-resource.png" alt-text="パブリック IP アドレスに関連付けるリソースの選択のスクリーンショット。":::

6. **[OK]** を選択します。

## <a name="attach-secondary-network-interface-to-virtual-machine"></a>セカンダリ ネットワーク インターフェイスを仮想マシンに接続する

このセクションでは、前に作成したセカンダリ ネットワーク インターフェイスを仮想マシンに接続します。

1. portal の検索ボックスに、「**仮想マシン**」と入力します。 検索結果から **[仮想マシン]** を選択します。

2. **[myVM]** を選択します。

3. **myVM** が実行されている場合は停止します。それ以外の場合は、次の手順に進みます。

4. **[myVM]** の **[設定]** で、 **[ネットワーク]** を選択します。

5. **myVM** の **[ネットワーク]** で、 **[ネットワーク インターフェイスの接続]** を選択します。

    :::image type="content" source="./media/routing-preference-mixed-network-adapter-portal/attach-nic-01.png" alt-text="myVM ネットワークの概要ページのスクリーンショット。":::

6. **[ネットワーク インターフェイスの接続]** のプルダウン ボックスで、 **[myVMNic2]** を選択します。

    :::image type="content" source="./media/routing-preference-mixed-network-adapter-portal/attach-nic-02.png" alt-text="[ネットワーク インターフェイスの接続] のスクリーンショット。":::

7. **[OK]** を選択します。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

このアプリケーションの使用を継続しない場合は、次の手順でパブリック IP アドレスと仮想マシンを削除します。

1. ポータル上部の [検索] ボックスに「**リソース グループ**」と入力します。

2. 検索結果から **[リソース グループ]** を選択します。

3. **[TutorVMMixRoutePref-rg]** を選択します。

4. **[リソース グループの削除]** を選択します。

5. **[TYPE THE RESOURCE GROUP NAME]\(リソース グループ名を入力してください\)** に「**myResourceGroup**」と入力し、 **[削除]** を選択します。

## <a name="next-steps"></a>次のステップ

次の記事に進んで、パブリック IP プレフィックスの作成方法を学習してください。
> [!div class="nextstepaction"]
> [Azure CLI を使用して Kubernetes クラスターのルーティング設定を構成する](routing-preference-azure-kubernetes-service-cli.md)