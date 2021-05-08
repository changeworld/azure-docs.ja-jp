---
title: Azure 仮想ネットワーク NAT ゲートウェイを使用して Azure Functions の送信 IP を制御する
description: Azure 仮想ネットワークに接続されている関数の NAT を構成する方法を説明するステップ バイ ステップのチュートリアル
ms.topic: tutorial
ms.author: kyburns
ms.date: 2/26/2021
ms.openlocfilehash: 5bb491e367ed813f09197a193745c231261c88c7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104658159"
---
# <a name="tutorial-control-azure-functions-outbound-ip-with-an-azure-virtual-network-nat-gateway"></a>チュートリアル: Azure 仮想ネットワーク NAT ゲートウェイを使用して Azure Functions の送信 IP を制御する

仮想ネットワーク アドレス変換 (NAT) を使用すると、仮想ネットワークで送信のみのインターネット接続が簡単になります。 これをサブネットに対して構成した場合、指定した静的パブリック IP アドレスがすべてのアウトバウンド接続で使用されます。 NAT は、セキュリティ対策として IP アドレスの許可リストが使われるサードパーティのサービスを使用する必要がある Azure Functions または Web Apps で役に立つ場合があります。 詳細については、「[Virtual Network NAT とは](../virtual-network/nat-overview.md)」を参照してください。

このチュートリアルでは、仮想ネットワーク NAT を使用して、HTTP によってトリガーされる関数からの送信トラフィックをルーティングする方法について説明します。 この関数を使用すると、それ自体の送信 IP アドレスを確認できます。 このチュートリアルでは、以下のことを行います。

> [!div class="checklist"]
> * 仮想ネットワークの作成
> * Premium プランの関数アプリを作成する
> * パブリック IP アドレスの作成
> * NAT ゲートウェイを作成する
> * NAT ゲートウェイ経由で送信トラフィックをルーティングするように関数アプリを構成する

## <a name="topology"></a>トポロジ

次の図は、作成するソリューションのアーキテクチャを示します。

![NAT ゲートウェイ統合の UI](./media/functions-how-to-use-nat-gateway/topology.png)

Premium プランで実行されている関数には、VNet 統合機能を含む、Azure App Service の Web アプリと同じホスティング機能があります。 トラブルシューティングや高度な構成など、VNet 統合の詳細については、「[アプリを Azure 仮想ネットワークに統合する](../app-service/web-sites-integrate-with-vnet.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

このチュートリアルでは、IP アドレスの割り当てとサブネット化を理解することが重要です。 [アドレスの割り当てとサブネット化の基本を取り上げたこの記事](https://support.microsoft.com/help/164015/understanding-tcp-ip-addressing-and-subnetting-basics)から開始できます。 多くの記事およびビデオをオンラインで利用できます。

Azure サブスクリプションがない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成してください。

既に [Functions と Azure 仮想ネットワークの統合](./functions-create-vnet.md)に関するチュートリアルを完了している場合は、「[HTTP トリガー関数の作成](#create-function)」に進むことができます。

## <a name="create-a-virtual-network"></a>仮想ネットワークの作成

1. Azure portal メニューから **[リソースの作成]** を選択します。 Azure Marketplace で、 **[ネットワーク]**  >  **[仮想ネットワーク]** を選択します。

1. **[仮想ネットワークの作成]** で、次の表で指定されている設定を入力するか選択します。

    | 設定 | 値 |
    | ------- | ----- |
    | サブスクリプション | サブスクリプションを選択します。|
    | Resource group | **[新規作成]** を選択し、「*myResourceGroup*」と入力して、 **[OK]** を選択します。 |
    | 名前 | 「*myResourceGroup-vnet*」と入力します。 |
    | 場所 | **[米国東部]** を選択します。|

1. **[次へ: IP アドレス]** を選択し、 **[IPv4 アドレス空間]** に「*10.10.0.0/16*」と入力します。

1. **[サブネットの追加]** を選択し、 **[サブネット名]** に「*Tutorial-Net*」、 **[サブネットのアドレス範囲]** に「*10.10.1.0/24*」と入力します。

    ![VNet を作成するための [IP アドレス] タブ](./media/functions-how-to-use-nat-gateway/create-vnet-2-ip-space.png)

1. **[追加]** を選択し、 **[Review + create]\(確認と作成\)** を選択します。 残りは既定値のままにして、 **[作成]** を選択します。

1. **[仮想ネットワークの作成]** で、 **[作成]** を選択します。

次に、[Premium プラン](functions-premium-plan.md)で関数アプリを作成します。 このプランはサーバーレス スケールを提供しながら、仮想ネットワーク統合をサポートします。

## <a name="create-a-function-app-in-a-premium-plan"></a>Premium プランの Function App を作成する

> [!NOTE]  
> このチュートリアルに最適なエクスペリエンスを実現するには、ランタイム スタックに .NET を選択し、オペレーティング システムに Windows を選択します。 また、仮想ネットワークと同じリージョンに関数アプリを作成します。

[!INCLUDE [functions-premium-create](../../includes/functions-premium-create.md)]  

## <a name="connect-your-function-app-to-the-virtual-network"></a>Function App を仮想ネットワークに接続する

これで、関数アプリを仮想ネットワークに接続できるようになりました。

1. 関数アプリで左側のメニューの **[ネットワーク]** を選択し、 **[VNet 統合]** で **[ここをクリックして構成]** を選択します。

    :::image type="content" source="./media/functions-how-to-use-nat-gateway/networking-0.png" alt-text="Function App で [ネットワーク] を選択する":::

1. **[VNET 統合]** ページで、 **[VNet の追加]** を選択します。

1. **[ネットワーク機能の状態]** で、画像の下にある表の設定を使用します。

    ![関数アプリの仮想ネットワークを定義する](./media/functions-how-to-use-nat-gateway/networking-3.png)

    | 設定      | 推奨値  | 説明      |
    | ------------ | ---------------- | ---------------- |
    | **Virtual Network** | MyResourceGroup-vnet | この仮想ネットワークは前に作成したものです。 |
    | **サブネット** | 新しいサブネットを作成します | 使用する Function App 用の仮想ネットワークにサブネットを作成します。 VNet 統合は、空のサブネットを使用するように構成する必要があります。 |
    | **サブネット名** | Function-Net | 新しいサブネットの名前です。 |
    | **仮想ネットワーク アドレス ブロック** | 10.10.0.0/16 | 1 つのアドレス ブロックだけを定義してください。 |
    | **サブネット アドレス ブロック** | 10.10.2.0/24   | サブネット サイズは、Premium プランの Function App がスケールアウトできるインスタンスの合計数を制限します。 この例では、254 のホスト アドレスが使用可能な `/24` サブネットを使用します。 このサブネットはオーバープロビジョニングされていますが、計算は簡単です。 |

1. **[OK]** を選択して、サブネットを追加します。 **[VNet 統合]** ページおよび **[ネットワーク機能の状態]** ページを閉じ、Function App のページに戻ります。

関数アプリから仮想ネットワークにアクセスできるようになりました。 次に、HTTP によってトリガーされる関数を関数アプリに追加します。

## <a name="create-an-http-trigger-function"></a><a name="create-function"></a>HTTP トリガー関数の作成

1. **[関数]** ウィンドウの左側のメニューで、 **[関数]** を選択し、上部のメニューから **[追加]** を選択します。 
 
1. **[新しい関数]** ウィンドウで、 **[Http トリガー]** を選択し、 **[新しい関数]** の既定の名前を受け入れるか、新しい名前を入力します。 

1. **[Code + Test]\(コード + テスト\)** で、テンプレートによって生成された C# スクリプト (.csx) コードを次のコードに置き換えます。 

    ```csharp
    #r "Newtonsoft.Json"
    
    using System.Net;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Extensions.Primitives;
    using Newtonsoft.Json;
    
    public static async Task<IActionResult> Run(HttpRequest req, ILogger log)
    {
        log.LogInformation("C# HTTP trigger function processed a request.");
    
        var client = new HttpClient();
        var response = await client.GetAsync(@"https://ifconfig.me");
        var responseMessage = await response.Content.ReadAsStringAsync();
    
        return new OkObjectResult(responseMessage);
    }
    ```

    このコードは、呼び出し元 (この例ではこの関数) の IP アドレスを返す外部 Web サイトを呼び出します。 この方法では、関数アプリによって使用されている送信 IP アドレスを簡単に確認できます。

これで、関数を実行し、現在の送信 IP を確認する準備ができました。

## <a name="verify-current-outbound-ips"></a>現在の送信 IP を確認する

これで、関数を実行できるようになりました。 ただし、まずはポータルにチェックインし、関数アプリによってどの送信 IP が使用されているかを確認します。  

1. 自分の関数アプリで、 **[プロパティ]** を選択し、 **[送信 IP アドレス]** フィールドを確認します。

    ![関数アプリの送信 IP アドレスを表示する](./media/functions-how-to-use-nat-gateway/function-properties-ip.png)

1. 次に、HTTP トリガー関数に戻り、 **[Code + Test]\(コード + テスト\)** 、 **[Test/Run]\(テスト/実行\)** の順に選択します。

    ![関数のテスト](./media/functions-how-to-use-nat-gateway/function-code-test.png)

1. **[実行]** を選択して関数を実行し、 **[出力]** に切り替えます。 

    ![関数のテストの出力](./media/functions-how-to-use-nat-gateway/function-test-1-output.png)

1. HTTP 応答本文の IP アドレスが、前に表示した送信 IP アドレスの値のうちの 1 つであることを確認します。

これで、パブリック IP を作成し、NAT ゲートウェイを使用してこの送信 IP アドレスを変更できるようになりました。

## <a name="create-public-ip"></a>パブリック IP を作成する

1. 自分のリソース グループで **[追加]** を選択し、Azure Marketplace で **[パブリック IP アドレス]** を検索して、 **[作成]** を選択します。 画像の下の表に示した設定を使用してください。

    ![パブリック IP アドレスを作成する](./media/functions-how-to-use-nat-gateway/create-public-ip.png)

    | 設定      | 推奨値  |
    | ------------ | ---------------- |
    | **IP バージョン** | IPv4 |
    | **SKU** | Standard |
    | **レベル** | 地域 |
    | **名前** | Outbound-IP |
    | **サブスクリプション** | 自分のサブスクリプションが表示されていることを確認します | 
    | **リソース グループ** | myResourceGroup (または自分がリソース グループに割り当てた名前) |
    | **場所** | 米国東部 (または他のリソースに割り当てた場所) |
    | **可用性ゾーン** | ゾーンなし |

1. **[作成]** を選択してデプロイを送信します。

1. デプロイが完了したら、新しく作成されたパブリック IP アドレス リソースに移動し、 **[概要]** で IP アドレスを確認します。

    ![パブリック IP アドレスを表示する](./media/functions-how-to-use-nat-gateway/public-ip-overview.png)

## <a name="create-nat-gateway"></a>NAT ゲートウェイの作成

次に、NAT ゲートウェイを作成しましょう。 [前の仮想ネットワークのチュートリアル](functions-create-vnet.md)から始めた場合、そのチュートリアルで推奨されたサブネット名は `Function-Net` であり、推奨された仮想ネットワーク名は `MyResourceGroup-vnet` でした。

1. 自分のリソース グループで **[追加]** を選択し、Azure Marketplace で **[NAT ゲートウェイ]** を検索して、 **[作成]** を選択します。 図の下の表の設定を使用して、 **[基本]** タブに値を指定します。

    ![NAT ゲートウェイの作成](./media/functions-how-to-use-nat-gateway/create-nat-1-basics.png)

    | 設定      | 推奨値  |
    | ------------ | ---------------- |  
    | **サブスクリプション** | 該当するサブスクリプション | 
    | **リソース グループ** | myResourceGroup (または自分がリソース グループに割り当てた名前) |
    | **NAT ゲートウェイ名** | myNatGateway |
    | **リージョン** | 米国東部 (または他のリソースに割り当てた場所) |
    | **可用性ゾーン** | なし |

1. **[次へ: 送信 IP]** を選択します。 **[パブリック IP アドレス]** フィールドで、前に作成したパブリック IP アドレスを選択します。 **[パブリック IP プレフィックス]** は、選択しないままにしておきます。

1. **[次へ: サブネット]** を選択します。 **[仮想ネットワーク]** フィールドの *myResourceGroup-vnet* リソースと、サブネット *Function-Net* を選択します。

    ![サブネットを選択する](./media/functions-how-to-use-nat-gateway/create-nat-3-subnet.png)

1. **[確認および作成]** 、 **[作成]** の順に選択し、デプロイを送信します。

デプロイが完了すると、NAT ゲートウェイは、関数アプリのサブネットからインターネットにトラフィックをルーティングできるようになります。

## <a name="update-function-configuration"></a>関数の構成を更新する

ここで、アプリケーション設定 `WEBSITE_VNET_ROUTE_ALL` を追加し、値を `1` に設定する必要があります。  この設定により、送信トラフィックは仮想ネットワークおよび関連する NAT ゲートウェイを通過するように強制されます。 この設定がないと、インターネット トラフィックは統合された仮想ネットワーク経由でルーティングされず、同じ送信 IP が表示されます。 

1. Azure portal で関数アプリに移動し、左側のメニューで **[構成]** を選択します。

1. **[アプリケーション設定]** で、 **[+ 新しいアプリケーション設定]** を選択し、次の値をフィールドに入力します。

    |フィールド名  |値 |
    |---|---|
    |**名前**    |WEBSITE_VNET_ROUTE_ALL|
    |**Value**   |1|

1. **[OK]** を選択して、新しいアプリケーション設定のダイアログを閉じます。

1. **[保存]** 、 **[続行]** の順に選択して、設定を保存します。

これで、関数アプリは関連付けられている仮想ネットワークを経由してトラフィックをルーティングするように構成されました。

## <a name="verify-new-outbound-ips"></a>新しい送信 IP を確認する

[前の手順](#verify-current-outbound-ips)を繰り返して、関数を再度実行します。 これで、NAT で構成した送信 IP アドレスが、関数の出力に表示されるようになったはずです。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

このチュートリアルを完了するためにリソースを作成しました。 これらのリソースには、[アカウントの状態](https://azure.microsoft.com/account/)と[サービスの価格](https://azure.microsoft.com/pricing/)に応じて課金されます。 追加のコストが発生しないようにするには、リソースが不要になったときに削除します。 

[!INCLUDE [functions-quickstart-cleanup-inner](../../includes/functions-quickstart-cleanup-inner.md)]

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Azure Functions のネットワーク オプション](functions-networking-options.md)
