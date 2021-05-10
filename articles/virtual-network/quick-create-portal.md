---
title: 'クイック スタート: 仮想ネットワークを作成する - Azure portal'
titleSuffix: Azure Virtual Network
description: このクイックスタートでは、Azure portal を使用した仮想ネットワークの作成方法について説明します。
author: KumudD
ms.author: kumud
ms.date: 03/17/2021
ms.topic: quickstart
ms.service: virtual-network
ms.workload: infrastructure
ms.tgt_pltfrm: virtual-network
ms.devlang: na
tags:
- azure-resource-manager
ms.custom:
- mode-portal
ms.openlocfilehash: 43c45b43084656a45d2509ee2c7a4376cdc7c052
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/16/2021
ms.locfileid: "107531181"
---
# <a name="quickstart-create-a-virtual-network-using-the-azure-portal"></a>クイック スタート:Azure Portal を使用した仮想ネットワークの作成

このクイックスタートでは、Azure Portal を使用した仮想ネットワークの作成方法について説明します。 2 つの仮想マシン (VM) をデプロイします。 次に、VM 間で安全な通信を行い、インターネットから VM に接続します。 仮想ネットワークは、Azure 内のプライベート ネットワークの基本的な構成要素です。 それにより、VM などの Azure リソースは、相互に、またはインターネットと、安全に通信することができます。

## <a name="prerequisites"></a>前提条件

* アクティブなサブスクリプションが含まれる Azure アカウント。 [無料で作成できます](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。

## <a name="sign-in-to-azure"></a>Azure へのサインイン

[Azure portal](https://portal.azure.com) にサインインします。

## <a name="create-a-virtual-network"></a>仮想ネットワークの作成

1. ポータルの左上隅にある **[リソースの作成]** を選択します。

2. 検索ボックスに、「**仮想ネットワーク**」と入力します。 検索結果から **[仮想ネットワーク]** を選択します。

3. **[仮想ネットワーク]** ページで、 **[作成]** を選択します。

4. **[仮想ネットワークの作成]** の **[基本]** タブで次の情報を入力または選択します。

    | 設定 | 値 |
    | ------- | ----- |
    | **プロジェクトの詳細** |   |
    | サブスクリプション | サブスクリプションを選択します。 |
    | Resource group | **[新規作成]** を選択します。  </br> 「**myResourceGroup**」と入力します。 </br> **[OK]** を選択します。 |
    | **インスタンスの詳細** |   |
    | 名前 | 「**myVNet**」と入力します。 |
    | リージョン | **[(米国) 米国東部]** を選択します。 |

    :::image type="content" source="./media/quick-create-portal/create-virtual-network.png" alt-text="Azure portal で仮想ネットワークを作成する" border="true":::

5. **[IP アドレス]** タブを選択するか、ページ下部の **[次へ: IP アドレス]** ボタンを選択します。

6. **[IPv4 アドレス空間]** で、既存のアドレス空間を選択し、「**10.1.0.0/16**」に変更します。

7. **[サブネットの追加]** を選択し、 **[サブネット名]** に「**MySubnet**」、 **[サブネットのアドレス範囲]** に「**10.1.0.0/24**」と入力します。

8. **[追加]** を選択します。

9. **[セキュリティ]** タブを選択するか、ページ下部にある **[次へ: セキュリティ]** ボタンを選択します。

10. **[BastionHost]** で **[有効にする]** を選択します。 この情報を入力します。

    | 設定            | 値                      |
    |--------------------|----------------------------|
    | 要塞名 | 「**myBastionHost**」と入力します |
    | AzureBastionSubnet のアドレス空間 | 「**10.1.1.0/24**」と入力します |
    | パブリック IP アドレス | **[新規作成]** を選択します。 </br> **[名前]** に「**myBastionIP**」と入力します。 </br> **[OK]** を選択します。 |

11. **[確認と作成]** タブを選択するか、 **[確認と作成]** ボタンを選択します。

12. **［作成］** を選択します

## <a name="create-virtual-machines"></a>仮想マシンを作成する

仮想ネットワークに 2 つの VM を作成します。

### <a name="create-the-first-vm"></a>最初の VM を作成する

1. ポータルの左上で、 **[リソースの作成]**  >  **[Compute]**  >  **[仮想マシン]** を選択します。 
   
2. **[仮想マシンの作成]** の **[Basic]** タブに、値を入力するか選択します。

    | 設定 | 値                                          |
    |-----------------------|----------------------------------|
    | **プロジェクトの詳細** |  |
    | サブスクリプション | Azure サブスクリプションを選択します。 |
    | リソース グループ | **[myResourceGroup]** を選択します |
    | **インスタンスの詳細** |  |
    | 仮想マシン名 | 「**myVM1**」と入力します |
    | リージョン | **[(米国) 米国東部]** を選択します |
    | 可用性オプション | **[インフラストラクチャ冗長は必要ありません]** を選択します |
    | Image | **[Windows Server 2019 Datacenter]** を選択します |
    | Azure Spot インスタンス | **[いいえ]**  を選択します |
    | サイズ | VM サイズを選択するか、既定の設定を使用します |
    | **管理者アカウント** |  |
    | ユーザー名 | ユーザー名を入力します |
    | Password | [パスワード] を入力します |
    | [パスワードの確認入力] | パスワードを再入力します |
    | **受信ポートの規則** |    |
    | パブリック受信ポート | **[なし]** を選択します。 |
    |

3. **[ネットワーク]** タブまたは **[次へ: ディスク]** を選択してから **[次へ: ネットワーク]** を選択します。
  
4. [ネットワーク] タブで、次を選択または入力します。

    | 設定 | 値 |
    |-|-|
    | **ネットワーク インターフェイス** |  |
    | 仮想ネットワーク | **[myVNet]** を選択します。 |
    | Subnet | **mySubnet** の選択 |
    | パブリック IP | **[なし]** を選択します |
    | NIC ネットワーク セキュリティ グループ | **[Basic]** を選択します|
    | パブリック受信ポート ネットワーク | **[なし]** を選択します。 |
   
5. **[Review + create]\(確認と作成\)** タブを選択するか、ページの下部にある青色の **[Review + create]\(確認と作成\)** ボタンを選択します。
  
6. 設定を確認し、 **[作成]** を選択します。

### <a name="create-the-second-vm"></a>2 つ目の VM を作成する

1. ポータルの左上で、 **[リソースの作成]**  >  **[Compute]**  >  **[仮想マシン]** を選択します。 
   
2. **[仮想マシンの作成]** の **[Basic]** タブに、値を入力するか選択します。

    | 設定 | 値                                          |
    |-----------------------|----------------------------------|
    | **プロジェクトの詳細** |  |
    | サブスクリプション | Azure サブスクリプションを選択します。 |
    | リソース グループ | **[myResourceGroup]** を選択します |
    | **インスタンスの詳細** |  |
    | 仮想マシン名 | 「**myVM2**」と入力します |
    | リージョン | **[(米国) 米国東部]** を選択します |
    | 可用性オプション | **[インフラストラクチャ冗長は必要ありません]** を選択します |
    | Image | **[Windows Server 2019 Datacenter]** を選択します |
    | Azure Spot インスタンス | **[いいえ]**  を選択します |
    | サイズ | VM サイズを選択するか、既定の設定を使用します |
    | **管理者アカウント** |  |
    | ユーザー名 | ユーザー名を入力します |
    | Password | [パスワード] を入力します |
    | [パスワードの確認入力] | パスワードを再入力します |
    | **受信ポートの規則** |    |
    | パブリック受信ポート | **[なし]** を選択します。 |
    |

3. **[ネットワーク]** タブまたは **[次へ: ディスク]** を選択してから **[次へ: ネットワーク]** を選択します。
  
4. [ネットワーク] タブで、次を選択または入力します。

    | 設定 | 値 |
    |-|-|
    | **ネットワーク インターフェイス** |  |
    | 仮想ネットワーク | **[myVNet]** を選択します。 |
    | Subnet | **mySubnet** の選択 |
    | パブリック IP | **[なし]** を選択します |
    | NIC ネットワーク セキュリティ グループ | **[Basic]** を選択します|
    | パブリック受信ポート ネットワーク | **[なし]** を選択します。 |
   
5. **[Review + create]\(確認と作成\)** タブを選択するか、ページの下部にある青色の **[Review + create]\(確認と作成\)** ボタンを選択します。
  
6. 設定を確認し、 **[作成]** を選択します。

[!INCLUDE [ephemeral-ip-note.md](../../includes/ephemeral-ip-note.md)]

## <a name="connect-to-myvm1"></a>myVM1 に接続する

1. [Azure portal](https://portal.azure.com) に移動して、プライベート VM を管理します。 **[仮想マシン]** を検索して選択します。

2. プライベート仮想マシンの名前「**myVM1**」を選択します。

3. VM のメニュー バーで、 **[接続]** を選択し、 **[Bastion]** を選択します。

    :::image type="content" source="./media/quick-create-portal/connect-to-virtual-machine.png" alt-text="Azure Bastion を使用して myVM1 に接続する" border="true":::

4. **[接続]** ページで、青色の **[Bastion を使用する]** ボタンを選択します。

5. **[Bastion]** ページで、前に仮想マシン用に作成したユーザー名とパスワードを入力します。

6. **[接続]** を選択します。

## <a name="communicate-between-vms"></a>VM 間の通信

1. **myVM1** の Bastion 接続で、PowerShell を開きます。

2. 「`ping myvm2`」と入力します。

    次の出力のようなメッセージが返されます。

    ```powershell
    Pinging myvm2.cs4wv3rxdjgedggsfghkjrxuqf.bx.internal.cloudapp.net [10.1.0.5] with 32 bytes of data:
    Reply from 10.1.0.5: bytes=32 time=3ms TTL=128
    Reply from 10.1.0.5: bytes=32 time=1ms TTL=128
    Reply from 10.1.0.5: bytes=32 time=1ms TTL=128
    Reply from 10.1.0.5: bytes=32 time=1ms TTL=128

    Ping statistics for 10.1.0.5:
        Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
    Approximate round trip times in milli-seconds:
        Minimum = 1ms, Maximum = 3ms, Average = 1ms
    ```

3. **myVM1** への Bastion 接続を閉じます。

4. 「[myVM1に接続する](#connect-to-myvm1)」の手順を完了し、「**myVM2**」に接続します。

5. **myVM2** で PowerShell を開き、`ping myvm1` と入力します。

    次のようなメッセージが表示されます。

    ```powershell
    Pinging myvm1.cs4wv3rxdjgedggsfghkjrxuqf.bx.internal.cloudapp.net [10.1.0.4] with 32 bytes of data:
    Reply from 10.1.0.4: bytes=32 time=1ms TTL=128
    Reply from 10.1.0.4: bytes=32 time=1ms TTL=128
    Reply from 10.1.0.4: bytes=32 time=1ms TTL=128
    Reply from 10.1.0.4: bytes=32 time=1ms TTL=128

    Ping statistics for 10.1.0.4:
        Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
    Approximate round trip times in milli-seconds:
        Minimum = 1ms, Maximum = 1ms, Average = 1ms
    ```

7. **myVM2** への Bastion 接続を閉じます。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

このクイック スタートでは、既定の仮想ネットワークと 2 つの VM を作成しました。 

インターネットから 1 つの VM に接続し、2 つの VM の間で安全に通信を行いました。

仮想ネットワークと VM を使い終えたら、リソース グループと含まれるすべてのリソースを削除します。

1. **myResourceGroup** を検索して選択します。

1. **[リソース グループの削除]** を選択します。

1. **[TYPE THE RESOURCE GROUP NAME]\(リソース グループ名を入力してください\)** に「**myResourceGroup**」と入力し、 **[削除]** を選択します。

## <a name="next-steps"></a>次のステップ

仮想ネットワーク設定の詳細については、「[仮想ネットワークの作成、変更、削除](manage-virtual-network.md)」をご覧ください。

VM ネットワーク通信の種類の詳細については、[ネットワーク トラフィックのフィルター処理](tutorial-filter-network-traffic.md)に関する記事を参照してください。
