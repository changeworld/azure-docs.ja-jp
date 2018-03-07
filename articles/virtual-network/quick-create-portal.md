---
title: "Azure での仮想ネットワークの作成 - ポータル | Microsoft Docs"
description: "Azure ポータルを使用した仮想ネットワークの作成について簡単に説明します。 仮想ネットワークでは、プライベートで相互通信するために、たくさんの種類の Azure リソースを使用できます。"
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: 
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 01/25/2018
ms.author: jdial
ms.custom: 
ms.openlocfilehash: b1dbe96b9f522474cd2eeb2b63f3429f9ea4d8ed
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/24/2018
---
# <a name="create-a-virtual-network-using-the-azure-portal"></a>Azure ポータルを使用した仮想ネットワークの作成

この記事では、仮想ネットワークの作成方法について説明します。 仮想ネットワークを作成した後は、仮想ネットワークに 2 つの仮想マシンをデプロイして、それらのマシン間でプライベート ネットワーク通信をテストします。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="log-in-to-azure"></a>Azure にログインする 

Azure Portal (http://portal.azure.com) にログインします。

## <a name="create-a-virtual-network"></a>仮想ネットワークの作成

1. Azure Portal の左上にある **[+ 新規]** を選択します。

2. **[ネットワーク]** を選択してから、**[仮想ネットワーク]** を選択します。

3. 次の図に示すように、**[名前]** に「*myVirtualNetwork*」、**[リソース グループ]** に「*myResourceGroup*」と入力し、**[場所]**と **[サブスクリプション]** で値を選択し、残りの既定値はそのまま受け入れて、**[作成]** を選択します。 

    ![仮想ネットワークに関する基本情報を入力する](./media/quick-create-portal/virtual-network.png)

    **アドレス範囲**は、CIDR 表記で指定されます。 仮想ネットワークは、0 個または 1 つ以上のサブネットを保持します。 既定のアドレス空間と範囲を使用している仮想ネットワーク内では別のサブネットを作成できないため、既定のサブネットの**アドレス範囲**である 10.0.0.0/24 が仮想ネットワークのアドレス範囲全体で使用されます。 指定されたアドレス範囲には、IP アドレス 10.0.0.0 ～ 10.0.0.254 が含まれています。 ただし、Azure では最初の 4 つのアドレス (0 ～ 3) と各サブネットの最後のアドレスを予約しているため、10.0.0.4 ～ 10.0.0.254 のみが使用可能です。 使用可能な IP アドレスは、仮想ネットワーク内にデプロイされたリソースに割り当てられます。

## <a name="test-network-communication"></a>ネットワーク通信をテストする

仮想ネットワークでは、プライベートで相互通信するために、複数の種類の Azure リソースを使用できます。 仮想ネットワークにデプロイできるリソースの種類の 1 つは、仮想マシンです。 仮想ネットワークに 2 つの仮想マシンを作成して、後の手順でそれらの間のプライベート通信を確認できるようにします。

### <a name="create-virtual-machines"></a>仮想マシンを作成する

1. Azure Portal の左上にある **[新規]** ボタンを選択します。

2. **[コンピューティング]**、**[Windows Server 2016 Datacenter]** の順に選択します。

3. 次の図に示す仮想マシンの情報を入力します。 入力した**ユーザー名**と**パスワード**は、以降の手順で仮想マシンへのログインに使用されます。 パスワードは 12 文字以上で、[定義された複雑さの要件](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm)を満たす必要があります。 お使いの**サブスクリプション**を選択し、既存の *myResourceGroup* リソース グループを使用することを選択して、選択した**場所**が仮想ネットワークを作成した場所と同じであることを確認します。 完了したら、**[OK]** を選択します。

    ![仮想マシンに関する基本情報を入力する](./media/quick-create-portal/virtual-machine-basics.png)

4. 仮想マシンのサイズを選択して、**[選択]** を選択します。 その他のサイズも表示するには、**[すべて表示]** を選択するか、**[Supported disk type (サポートされているディスクの種類)]** フィルターを変更します。 お使いの画面に表示されるサイズは、以下の例とは異なる場合があります。 

    ![仮想マシンのサイズを選択する](./media/quick-create-portal/virtual-machine-size.png)

5. **[設定]** では、既に **[仮想ネットワーク]** に *[myVirtualNetwork]* が選択されているはずですが、そうでない場合は、**[仮想ネットワーク]** を選択して *[myVirtualNetwork]* を選択します。 **[サブネット]** に *[既定]* が選択された状態で、**[OK]** を選択します。

    ![仮想ネットワークを作成する](./media/quick-create-portal/virtual-machine-network-settings.png)

6. **[概要]** ページで、**[作成]** を選択して、仮想マシンのデプロイを開始します。 

7. 仮想マシンの作成には、数分かかります。 作成後、仮想マシンが Azure Portal のダッシュボードに固定され、仮想マシンの概要が自動的に開きます。 **[ネットワーク]** を選択します。

    ![仮想マシンのネットワーク情報](./media/quick-create-portal/virtual-machine-networking.png)

    **プライベート IP** アドレスが *10.0.0.4* であることがわかります。 手順 5 では、**[設定]** で、*myVirtualNetwork* 仮想ネットワークを選択し、**[サブネット]** に*既定* という名前のサブネットを設定しました。 [仮想ネットワークを作成した](#create-a-virtual-network)とき、サブネットの **[アドレス範囲]** には、既定値である 10.0.0.0/24 を設定しました。 Azure の DHCP サーバーは、選択したサブネットで最初に使用可能なアドレスを仮想マシンに割り当てます。 Azure では、各サブネットの最初の 4 つのアドレス (0 ～ 3) を予約しているため、サブネットに使用できる最初の IP アドレスは 10.0.0.4 です。

    割り当てられる**パブリック IP** アドレスは、お使いの仮想マシンに割り当てられているアドレスとは異なります。 Azure では既定で、インターネット ルーティング可能なパブリック IP アドレスを各仮想マシンに割り当てます。 パブリック IP アドレスは、[各 Azure リージョンに割り当てられているアドレスのプール](https://www.microsoft.com/download/details.aspx?id=41653)から、仮想マシンに割り当てられます。 Azure ではどのパブリック IP アドレスが仮想マシンに割り当てられているかを把握していますが、仮想マシンで実行されているオペレーティング システムが、割り当てられたパブリック IP アドレスを認識することはありません。

8. 手順 1 ～ 7 をもう一度完了します。ただし、手順 3 では、仮想マシンの名前を *myVm2* にします。 

9. 仮想マシンを作成した後、手順 7 で行ったように **[ネットワーク]** を選択します。 **プライベート IP** アドレスが *10.0.0.5* であることがわかります。 Azure ではサブネットで最初に使用可能なアドレスである *10.0.0.4* を *myVm1* 仮想マシンに割り当てたので、サブネットで次に使用可能なアドレスである *10.0.0.5* を *myVm2* 仮想マシンに割り当てました。

### <a name="connect-to-a-virtual-machine"></a>仮想マシンへの接続

1. *myVm1* 仮想マシンにリモートで接続します。 Azure Portal の上部で、「*myVm1*」と入力します。 検索結果に **myVm1** が表示されたら、それを選択します。 **[接続]** を選択します。

    ![仮想マシンの概要](./media/quick-create-portal/virtual-machine-overview.png)

2. **[接続]** ボタンを選択した後、リモート デスクトップ プロトコル (.rdp) ファイルが作成され、お使いのコンピューターにダウンロードされます。  

3. ダウンロードされた rdp ファイルを開きます。 メッセージが表示されたら、**[Connect]** を選択します。 仮想マシンを作成するときに指定したユーザー名とパスワードを入力して、**[OK]**  を選択します。 サインイン処理中に証明書の警告が表示される場合があります。 **[はい]** または **[続行]** を選択して接続処理を続行します。

### <a name="validate-communication"></a>通信を検証する

Windows ファイアウォールでは既定で ping が許可されていないため、Windows 仮想マシンへの ping を試行すると、エラーになります。 *myVm1* への ping を許可するには、コマンド プロンプトから以下のコマンドを入力します。

```
netsh advfirewall firewall add rule name=Allow-ping protocol=icmpv4 dir=in action=allow
```

*myVm2* との通信を検証するには、*myVm1* 仮想マシン上でコマンド プロンプトから以下のコマンドを入力します。 仮想マシンを作成したときに使用した資格情報を提供して、接続を完了します。

```
mstsc /v:myVm2
```

両方の仮想マシンが*既定の*サブネットから割り当てられたプライベート IP アドレスを保持し、かつ、リモート デスクトップが既定で Windows ファイアウォール経由で開かれたため、リモート デスクトップ接続に成功します。 Azure は仮想ネットワーク内のすべてのホストに DNS 名前解決を自動的に提供するので、*myVm2* にはホスト名で接続できます。 コマンド プロンプトで、*myVm2* から *myVm1* を ping します。

```
ping myvm1
```

前の手順で *myVm1* 仮想マシン上の Windows ファイアウォール経由での ping を許可したため、ping に成功します。 インターネットへの送信方向の通信を確認するには、以下のコマンドを入力します。

```
ping bing.com
```

bing.com から 4 つの応答を受信します。既定では、仮想ネットワークにあるどの仮想マシンも、インターネットへの送信方向の通信が可能です。 

リモート デスクトップ セッションを終了します。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

リソース グループとそのコンテンツが不要になったら、それらを削除します。 Azure Portal の上部で、「*myResourceGroup*」と入力します。 検索結果に **myResourceGroup** が表示されたら、それを選択してください。 **[削除]**を選択します。

## <a name="next-steps"></a>次の手順

この記事では、1 つのサブネットを含む既定の仮想ネットワークをデプロイしました。 複数のサブネットでカスタム仮想ネットワークを作成する方法を習得するには、カスタム仮想ネットワークの作成に関するチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [カスタム仮想ネットワークの作成](virtual-networks-create-vnet-arm-pportal.md#portal)
