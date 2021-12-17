---
title: 'チュートリアル: VM 用にルーティング優先設定を構成する - Azure portal'
description: このチュートリアルでは、Azure portal を使用して、ルーティング優先設定を選択してパブリック IP アドレスを持つ VM を作成する方法について説明します。
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: tutorial
ms.date: 10/01/2021
ms.custom: template-tutorial
ms.openlocfilehash: 6a06ec95b6970e4c3f8d4bde4cc180bf832f3a6a
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/01/2021
ms.locfileid: "129369507"
---
# <a name="tutorial-configure-routing-preference-for-a-vm-using-the-azure-portal"></a>チュートリアル: Azure portal を使用して VM 用にルーティング優先設定を構成する 

このチュートリアルでは、仮想マシン用にルーティング優先設定を構成する方法について説明します。 ルーティング優先設定オプションとして **[インターネット]** を選択すると、VM からインターネットへのトラフィックは ISP ネットワーク経由でルーティングされます。 既定のルーティングは、Microsoft グローバル ネットワーク経由です。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * **インターネット** ルーティング優先設定用に構成されたパブリック IP アドレスを持つ仮想マシンを作成します。
> * パブリック IP アドレスが **インターネット** ルーティング優先設定に設定されていることを確認します。

## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="create-virtual-machine"></a>仮想マシンの作成

このセクションでは、仮想マシンとパブリック IP アドレスを作成します。 パブリック IP アドレスの構成中に、ルーティング優先設定で **[インターネット]** を選択します。

1. [Azure portal](https://portal.azure.com) にサインインします。

2. ポータルの検索ボックスに、「**仮想マシン**」と入力します。 検索結果から **[仮想マシン]** を選択します。

3. **[仮想マシン]** で、 **[+ 作成]** 、 **[+ 仮想マシン]** の順に選択します。

4. **[仮想マシンの作成]** の **[基本]** タブで、次の情報を入力または選択します。

    | 設定 | 値 |
    | ------- | ----- |
    | **プロジェクトの詳細** |   |
    | サブスクリプション | サブスクリプションを選択します。 |
    | Resource group | **[新規作成]** を選択します。 </br> 「**TutorVMRoutePref-rg**」と入力します。 **[OK]** を選択します。 |
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
    | 受信ポートの選択 | 既定値の **[RDP (3389)]** のままにします。 </br> _**運用環境のワークロードでは、インターネットからポート 3389 を開くことはお勧めしません。**_ |

    :::image type="content" source="./media/tutorial-routing-preference-virtual-machine-portal/create-virtual-machine.png" alt-text="仮想マシンの作成のスクリーンショット。":::

5. **[次へ: ディスク]** 、さらに **[次へ: ネットワーク]** を選択するか、 **[ネットワーク]** タブを選択します。

6. [ネットワーク] タブで、次の情報を入力または選択します。

    | 設定 | 値 |
    | ------- | ----- |
    | **ネットワーク インターフェイス** |   |
    | 仮想ネットワーク | 既定値の **(新規) TutorVMRoutePref-rg-vnet** のままにします。 |
    | Subnet | 既定値の **(新規) 既定 (10.1.0.0/24)** のままにします。 |
    | パブリック IP | **[新規作成]** を選択します。 </br> **[名前]** に「**myPublicIP**」と入力します。 </br> **[SKU]** で **[Standard]** を選択します。 </br> **[ルーティングの優先順位]** で、 **[インターネット]** を選択します。 </br> **[OK]** を選択します。 |

    :::image type="content" source="./media/tutorial-routing-preference-virtual-machine-portal/create-public-ip.png" alt-text="パブリック IP アドレスの作成のスクリーンショット。":::

7. **[Review + create]\(レビュー + 作成\)** を選択します。

8. **［作成］** を選択します

## <a name="verify-internet-routing-preference"></a>インターネット ルーティング優先設定の確認

このセクションでは、以前に作成したパブリック IP アドレスを検索し、インターネット ルーティング優先設定を確認します。

1. ポータルの検索ボックスに「**パブリック IP アドレス**」と入力します。 検索結果から **パブリック IP アドレス** を選択します。

2. **[パブリック IP アドレス]** で、 **[myPublicIP]** を選択します。

3. **[設定]** で **[プロパティ]** を選択します。

4. **[ルーティングの優先順位]** に **[インターネット]** が表示されていることを確認します。 

    :::image type="content" source="./media/tutorial-routing-preference-virtual-machine-portal/verify-routing-preference.png" alt-text="インターネット ルーティング優先設定を確認するスクリーンショット。":::

## <a name="clean-up-resources"></a>リソースをクリーンアップする

このアプリケーションの使用を継続しない場合は、次の手順でパブリック IP アドレスを削除します。

1. ポータル上部の [検索] ボックスに「**リソース グループ**」と入力します。

2. 検索結果から **[リソース グループ]** を選択します。

3. **[TutorVMRoutePref-rg]** を選択します。

4. **[リソース グループの削除]** を選択します。

5. **[TYPE THE RESOURCE GROUP NAME]\(リソース グループ名を入力してください\)** に「**myResourceGroup**」と入力し、 **[削除]** を選択します。

## <a name="next-steps"></a>次のステップ

次の記事に進み、ルーティング優先設定を組み合わせて仮想マシンを作成する方法について説明します。
> [!div class="nextstepaction"]
> [仮想マシンの両方のルーティング優先設定オプションを構成する](routing-preference-mixed-network-adapter-portal.md)

