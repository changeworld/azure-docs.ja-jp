---
title: プライベート エンドポイントを使用して Azure Functions と仮想ネットワークを統合する
description: 関数を Azure 仮想ネットワークに接続し、プライベート エンドポイントに固定する方法を説明するステップ バイ ステップ チュートリアル
ms.topic: article
ms.date: 2/22/2021
ms.openlocfilehash: a7bad58167009b4089724165813eb061996f1e6b
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/05/2021
ms.locfileid: "102200208"
---
# <a name="tutorial-integrate-azure-functions-with-an-azure-virtual-network-using-private-endpoints"></a>チュートリアル: プライベート エンドポイントを使用して Azure Functions を Azure 仮想ネットワークに統合する

このチュートリアルでは、Azure Functions を使用して Azure 仮想ネットワーク内のリソースに、プライベート エンドポイントを使用して接続する方法について説明します。 Service Bus キュー トリガーを使用する仮想ネットワークの背後でロックされているストレージ アカウントを使用して、関数を作成します。

> [!div class="checklist"]
> * Premium プランの Function App を作成する
> * Azure リソース (Service Bus、ストレージ アカウント、仮想ネットワーク) を作成する
> * プライベート エンドポイントの背後にあるストレージ アカウントをロックダウンする
> * プライベート エンドポイントの背後にある Service Bus をロックダウンする
> * Service Bus と HTTP トリガーの両方を使用して関数アプリを展開する
> * プライベート エンドポイントの背後で関数アプリをロックダウンする
> * 関数アプリが仮想ネットワークの背後でセキュリティ保護されているかどうかをテストする
> * リソースをクリーンアップする

## <a name="create-a-function-app-in-a-premium-plan"></a>Premium プランの Function App を作成する

まず、このチュートリアルでは C# を使用するので、[Premium プラン]で .NET 関数アプリを作成します。 Windows では、他の言語もサポートされています。 このプランはサーバーレス スケールを提供しながら、仮想ネットワーク統合をサポートします。

1. Azure portal メニューまたは **[ホーム]** ページで、 **[リソースの作成]** を選択します。

1. **[新規]** ページで、 **[計算]**  > 、 **[関数アプリ]** の順に選択します。

1. **[基本]** ページで、下の表に指定されている関数アプリの設定を使用します。

    | 設定      | 推奨値  | 説明 |
    | ------------ | ---------------- | ----------- |
    | **サブスクリプション** | 該当するサブスクリプション | この新しい Function App が作成されるサブスクリプション。 |
    | **[リソース グループ](../azure-resource-manager/management/overview.md)** |  *myResourceGroup* | Function App を作成するための新しいリソース グループの名前。 |
    | **関数アプリ名** | グローバルに一意の名前 | 新しい Function App を識別する名前。 有効な文字は、`a-z` (大文字と小文字の区別をしない)、`0-9`、および `-`です。  |
    |**発行**| コード | コード ファイルまたは Docker コンテナーの発行オプション。 |
    | **ランタイム スタック** | .NET | このチュートリアルでは、.NET を使用します。 |
    |**リージョン**| 優先リージョン | ユーザーに近い[リージョン](https://azure.microsoft.com/regions/)、または関数がアクセスする他のサービスの近くのリージョンを選択します。 |

1. **[Next:ホスティング]** を選択します。 **[ホスティング]** ページで、次の設定を入力します。

    | 設定      | 推奨値  | 説明 |
    | ------------ | ---------------- | ----------- |
    | **[ストレージ アカウント](../storage/common/storage-account-create.md)** |  グローバルに一意の名前 |  Function App で使用されるストレージ アカウントを作成します。 ストレージ アカウント名の長さは 3 ～ 24 文字で、数字と小文字のみを使用できます。 既存のアカウントを使用することもできますが、[ストレージ アカウントの要件](./storage-considerations.md#storage-account-requirements)を満たしている必要があります。 |
    |**オペレーティング システム**| Windows | このチュートリアルでは、Windows を使用します。 |
    | **[プラン](./functions-scale.md)** | Premium | Function App にどのようにリソースが割り当てられるかを定義するホスティング プラン。 **[Premium]** を選択します。 既定では、新しい App Service プランが作成されます。 既定の **SKU とサイズ** は **EP1** です。ここで、EP は "_エラスティック Premium_" を表します。 詳細については、[Premium SKU の一覧](./functions-premium-plan.md#available-instance-skus)を参照してください。<br/>Premium プランで JavaScript 関数を実行する場合は、vCPU の少ないインスタンスを選ぶ必要があります。 詳しくは、[シングルコア Premium プランの選択](./functions-reference-node.md#considerations-for-javascript-functions)に関する記事をご覧ください。  |

1. **[Next:監視]** を選択します。 **[監視]** ページで、次の設定を入力します。

    | 設定      | 推奨値  | 説明 |
    | ------------ | ---------------- | ----------- |
    | **[Application Insights](./functions-monitoring.md)** | Default | 最も近いサポートされているリージョン内に同じ *アプリ名* の Application Insights リソースを作成します。 この設定を展開することによって、 **[新しいリソース名]** を変更するか、データを格納する [Azure 地域](https://azure.microsoft.com/global-infrastructure/geographies/)内の別の **場所** を選択することができます。 |

1. **[確認および作成]** を選択して、アプリ構成の選択内容を確認します。

1. **[確認および作成]** ページで設定を確認して、 **[作成]** を選択し、関数アプリをプロビジョニングしてデプロイします。

1. ポータルの右上隅の **[通知]** アイコンを選択し、"**デプロイメントに成功しました**" というメッセージが表示されるまで待ちます。

1. **[リソースに移動]** を選択して、新しい Function App を確認します。 また、 **[ダッシュボードにピン留めする]** を選択することもできます。 ピン留めすると、ダッシュボードからこの関数アプリ リソースに戻るのが容易になります。

1. おめでとうございます。 これで、Premium 版の関数アプリを作成できました。

## <a name="create-azure-resources"></a>Azure リソースを作成する

### <a name="create-a-storage-account"></a>ストレージ アカウントの作成

仮想ネットワークには、関数アプリを最初に作成したときとは別のストレージ アカウントが必要です。

1. Azure portal メニューまたは **[ホーム]** ページで、 **[リソースの作成]** を選択します。

1. [新規] ページで、 **[ストレージ アカウント]** を検索し、 **[作成]** を選択します。

1. **[基本]** タブの設定を以下の表に従って設定します。 それ以外は既定のままでかまいません。

    | 設定      | 推奨値  | 説明      |
    | ------------ | ---------------- | ---------------- |
    | **サブスクリプション** | 該当するサブスクリプション | リソースが作成されるサブスクリプション。 | 
    | **[リソース グループ](../azure-resource-manager/management/overview.md)**  | myResourceGroup | 関数アプリで作成したリソース グループを選択します。 |
    | **名前** | mysecurestorage| プライベート エンドポイントを適用するストレージ アカウントの名前。 |
    | **[リージョン](https://azure.microsoft.com/regions/)** | myFunctionRegion | 関数アプリを作成したリージョンを選択します。 |

1. **[Review + create]\(レビュー + 作成\)** を選択します。 検証が完了した後、 **[作成]** を選択します。

### <a name="create-a-service-bus"></a>Service Bus の作成

1. Azure portal メニューまたは **[ホーム]** ページで、 **[リソースの作成]** を選択します。

1. [新規] ページで、**Service Bus** を検索し、 **[作成]** を選択します。

1. **[基本]** タブの設定を以下の表に従って設定します。 それ以外は既定のままでかまいません。

    | 設定      | 推奨値  | 説明      |
    | ------------ | ---------------- | ---------------- |
    | **サブスクリプション** | 該当するサブスクリプション | リソースが作成されるサブスクリプション。 |
    | **[リソース グループ](../azure-resource-manager/management/overview.md)**  | myResourceGroup | 関数アプリで作成したリソース グループを選択します。 |
    | **名前** | myServiceBus| プライベート エンドポイントを適用する Service Bus の名前。 |
    | **[リージョン](https://azure.microsoft.com/regions/)** | myFunctionRegion | 関数アプリを作成したリージョンを選択します。 |
    | **価格レベル** | Premium | Service Bus でプライベート エンドポイントを使用するには、この層を選択します。 |

1. **[Review + create]\(レビュー + 作成\)** を選択します。 検証が完了した後、 **[作成]** を選択します。

### <a name="create-a-virtual-network"></a>仮想ネットワークの作成

このチュートリアルの Azure リソースは、仮想ネットワークと統合されているか、仮想ネットワーク内に配置されています。 プライベート エンドポイントを使用して、ネットワーク トラフィックを仮想ネットワーク内に保持します。

このチュートリアルでは、2 つのサブネットを作成します。
- **default**: プライベート エンドポイントのサブネット。 プライベート IP アドレスは、このサブネットから提供されます。
- **functions**: Azure Functions 仮想ネットワーク統合のためのサブネット。 このサブネットは、関数アプリに委任されます。

ここで、関数アプリの統合先となる仮想ネットワークを作成します。

1. Azure portal メニューまたは [ホーム] ページで、 **[リソースの作成]** を選択します。

1. [新規] ページで、**仮想ネットワーク** を検索し、 **[作成]** を選択します。

1. **[基本]** タブで、次のように仮想ネットワーク設定を使用します。

    | 設定      | 推奨値  | 説明      |
    | ------------ | ---------------- | ---------------- |
    | **サブスクリプション** | 該当するサブスクリプション | リソースが作成されるサブスクリプション。 | 
    | **[リソース グループ](../azure-resource-manager/management/overview.md)**  | myResourceGroup | 関数アプリで作成したリソース グループを選択します。 |
    | **名前** | myVirtualNet| 関数アプリが接続する仮想ネットワークの名前。 |
    | **[リージョン](https://azure.microsoft.com/regions/)** | myFunctionRegion | 関数アプリを作成したリージョンを選択します。 |

1. **[IP アドレス]** タブで、 **[サブネットの追加]** を選択します。 サブネットを追加するときは、次の設定を使用します。

    :::image type="content" source="./media/functions-create-vnet/1-create-vnet-ip-address.png" alt-text="[仮想ネットワークの作成] 構成ビューのスクリーンショット。":::

    | 設定      | 推奨値  | 説明      |
    | ------------ | ---------------- | ---------------- |
    | **サブネット名** | functions | 関数アプリの接続先となるサブネットの名前。 | 
    | **サブネットのアドレス範囲** | 10.0.1.0/24 | 上の図の IPv4 アドレス空間は 10.0.0.0/16 です。 上記の値が 10.1.0.0/16 の場合、推奨される *サブネットのアドレス範囲* は 10.1.1.0/24 です。 |

1. **[Review + create]\(レビュー + 作成\)** を選択します。 検証が完了した後、 **[作成]** を選択します。

## <a name="lock-down-your-storage-account-with-private-endpoints"></a>プライベート エンドポイントを使用してストレージ アカウントをロックダウンする

Azure プライベート エンドポイントは、プライベート IP アドレスを使用して特定の Azure リソースに接続するために使用されます。 この接続により、ネットワーク トラフィックは選択した仮想ネットワーク内にとどまり、アクセスは特定のリソースに対してのみ許可されます。 次に、ストレージ アカウントを使用して、Azure File ストレージと Azure Blob ストレージのプライベート エンドポイントを作成します。

1. 新しいストレージ アカウントで、左側のメニューの **[ネットワーク]** を選択します。

1. **[プライベート エンドポイント接続]** タブを選択し、 **[プライベート エンドポイント]** を選択します。

    :::image type="content" source="./media/functions-create-vnet/2-navigate-private-endpoint-store.png" alt-text="ストレージ アカウントのプライベート エンドポイントを作成するための方法を示すスクリーンショット。":::

1. **[基本]** タブで、次のようにプライベート エンドポイント設定を使用します。

    | 設定      | 推奨値  | 説明      |
    | ------------ | ---------------- | ---------------- |
    | **サブスクリプション** | 該当するサブスクリプション | リソースが作成されるサブスクリプション。 | 
    | **[リソース グループ](../azure-resource-manager/management/overview.md)**  | myResourceGroup | 関数アプリで作成したリソース グループを選択します。 | |
    | **名前** | file-endpoint | ストレージ アカウントからのファイルのプライベート エンドポイントの名前。 |
    | **[リージョン](https://azure.microsoft.com/regions/)** | myFunctionRegion | ストレージ アカウントを作成したリージョンを選択します。 |

1. **[リソース]** タブで、次のようにプライベート エンドポイント設定を使用します。

    | 設定      | 推奨値  | 説明      |
    | ------------ | ---------------- | ---------------- |
    | **サブスクリプション** | 該当するサブスクリプション | リソースが作成されるサブスクリプション。 | 
    | **リソースの種類**  | Microsoft.Storage/storageAccounts | これは、ストレージ アカウントのリソースの種類です。 |
    | **リソース** | mysecurestorage | 作成したストレージ アカウントです。 |
    | **ターゲット サブリソース** | file | このプライベート エンドポイントは、ストレージ アカウントからのファイルに使用されます。 |

1. **[構成]** タブで、サブネット設定の **default** を選択します。

1. **[Review + create]\(レビュー + 作成\)** を選択します。 検証が完了した後、 **[作成]** を選択します。 仮想ネットワーク内のリソースがストレージ ファイルと通信できるようになりました。

1. BLOB 用に別のプライベート エンドポイントを作成します。 **[リソース]** タブで、以下の設定を使用します。 その他の設定については、さきほど実行したプライベート エンドポイントの作成手順と同じファイルの設定を使用します。

    | 設定      | 推奨値  | 説明      |
    | ------------ | ---------------- | ---------------- |
    | **サブスクリプション** | 該当するサブスクリプション | リソースが作成されるサブスクリプション。 | 
    | **リソースの種類**  | Microsoft.Storage/storageAccounts | これは、ストレージ アカウントのリソースの種類です。 |
    | **リソース** | mysecurestorage | 作成したストレージ アカウントです。 |
    | **ターゲット サブリソース** | blob (blob) | このプライベート エンドポイントは、ストレージ アカウントからの BLOB に使用されます。 |

## <a name="lock-down-your-service-bus-with-a-private-endpoint"></a>プライベート エンドポイントを使用して Service Bus をロックダウンする

次に、Azure Service Bus のプライベート エンドポイントを作成します。

1. 新しい Service Bus で、左側のメニューの **[ネットワーク]** を選択します。

1. **[プライベート エンドポイント接続]** タブを選択し、 **[プライベート エンドポイント]** を選択します。

    :::image type="content" source="./media/functions-create-vnet/3-navigate-private-endpoint-service-bus.png" alt-text="Service Bus のプライベート エンドポイントに移動する方法を示すスクリーンショット。":::

1. **[基本]** タブで、次のようにプライベート エンドポイント設定を使用します。

    | 設定      | 推奨値  | 説明      |
    | ------------ | ---------------- | ---------------- |
    | **サブスクリプション** | 該当するサブスクリプション | リソースが作成されるサブスクリプション。 | 
    | **[リソース グループ](../azure-resource-manager/management/overview.md)**  | myResourceGroup | 関数アプリで作成したリソース グループを選択します。 |
    | **名前** | sb-endpoint | ストレージ アカウントからのファイルのプライベート エンドポイントの名前。 |
    | **[リージョン](https://azure.microsoft.com/regions/)** | myFunctionRegion | ストレージ アカウントを作成したリージョンを選択します。 |

1. **[リソース]** タブで、次のようにプライベート エンドポイント設定を使用します。

    | 設定      | 推奨値  | 説明      |
    | ------------ | ---------------- | ---------------- |
    | **サブスクリプション** | 該当するサブスクリプション | リソースが作成されるサブスクリプション。 | 
    | **リソースの種類**  | Microsoft.ServiceBus/namespaces | これは Service Bus のリソースの種類です。 |
    | **リソース** | myServiceBus | チュートリアルで作成した Service Bus。 |
    | **ターゲット サブリソース** | namespace | このプライベート エンドポイントは、Service Bus の名前空間に使用されます。 |

1. **[構成]** タブで、サブネット設定の **default** を選択します。

1. **[Review + create]\(レビュー + 作成\)** を選択します。 検証が完了した後、 **[作成]** を選択します。 仮想ネットワーク内のリソースが Service Bus と通信できるようになりました。

## <a name="create-a-file-share"></a>ファイル共有を作成する

1. 作成したストレージ アカウントで、左側のメニューの **[ファイル共有]** を選択します。

1. **[ファイル共有]** を選択します。 このチュートリアルでは、ファイル共有の名前として **files** を指定します。

    :::image type="content" source="./media/functions-create-vnet/4-create-file-share.png" alt-text="ストレージ アカウントにファイル共有を作成する方法を示すスクリーンショット。":::

## <a name="get-storage-account-connection-string"></a>ストレージ アカウントの接続文字列を取得する

1. 作成したストレージ アカウントで、左側のメニューの **[アクセス キー]** を選択します。

1. **[キーの表示/非表示]** を選択します。 接続文字列 key1 をコピーして保存します。 この接続文字列は、後でアプリケーション設定を構成するときに必要になります。

    :::image type="content" source="./media/functions-create-vnet/5-get-store-connection-string.png" alt-text="ストレージ アカウントの接続文字列を取得する方法を示すスクリーンショット。":::

## <a name="create-a-queue"></a>キューを作成する

これは、Azure Functions Service Bus トリガーがイベントを取得するキューです。

1. Service Bus で、左側のメニューの **[キュー]** を選択します。

1. **[共有アクセス ポリシー]** を選択します。 このチュートリアルでは、キューの名前として **queue** を指定します。

    :::image type="content" source="./media/functions-create-vnet/6-create-queue.png" alt-text="Service Bus キューを作成する方法を示すスクリーンショット。":::

## <a name="get-service-bus-connection-string"></a>Service Bus の接続文字列を取得する

1. Service Bus で、左側のメニューの **[共有アクセス ポリシー]** を選択します。

1. **[RootManageSharedAccessKey]** を選択します。 **[プライマリ接続文字列]** をコピーして保存します。 この接続文字列は、後でアプリケーション設定を構成するときに必要になります。

    :::image type="content" source="./media/functions-create-vnet/7-get-service-bus-connection-string.png" alt-text="Service Bus の接続文字列を取得する方法を示すスクリーンショット。":::

## <a name="integrate-function-app-with-your-virtual-network"></a>関数アプリと仮想ネットワークを統合する

仮想ネットワークで関数アプリを使用するには、それをサブネットに参加させる必要があります。 このチュートリアルで作成した他のすべてのプライベート エンドポイントで、Azure Functions 仮想ネットワーク統合と既定のサブネットに対し、特定のサブネットを使用します。

1. 関数アプリで、左側のメニューの **[ネットワーク]** を選択します。

1. [VNet 統合] にある **[ここをクリックして構成]** を選択します。

    :::image type="content" source="./media/functions-create-vnet/8-connect-app-vnet.png" alt-text="仮想ネットワークに移動する方法を示すスクリーンショット。":::

1. **[VNet の追加]** を選択します。

1. **[仮想ネットワーク]** に表示されるブレードで、前の手順で作成した仮想ネットワークを選択します。

1. 前の手順で作成した **サブネット** (**functions**) を選択します。 これで、関数アプリが仮想ネットワークと統合されました。

    :::image type="content" source="./media/functions-create-vnet/9-connect-app-subnet.png" alt-text="関数アプリをサブネットに接続する方法を示すスクリーンショット。":::

## <a name="configure-your-function-app-settings-for-private-endpoints"></a>プライベート エンドポイントの関数アプリ設定を構成する

1. 関数アプリで、左側のメニューから **[構成]** を選択します。

1. 仮想ネットワークで関数アプリを使用するには、次のアプリ設定を更新する必要があります。 **[新しいアプリケーション設定]** を選択するか、必要に応じて、アプリの設定テーブルの右端の列にある鉛筆で **編集** します。 操作が完了したら、**[保存]** をクリックします。

    :::image type="content" source="./media/functions-create-vnet/10-configure-app-settings.png" alt-text="プライベート エンドポイントの関数アプリ設定を構成する方法を示すスクリーンショット。":::

    | 設定      | 推奨値  | 説明      |
    | ------------ | ---------------- | ---------------- |
    | **AzureWebJobsStorage** | mysecurestorageConnectionString | 作成したストレージ アカウントの接続文字列。 これは、「[ストレージ アカウントの接続文字列を取得する](#get-storage-account-connection-string)」のストレージ接続文字列です。 この設定を変更することで、関数アプリは、通常操作の実行時に、セキュリティで保護されたストレージ アカウントを使用するようになります。 | 
    | **WEBSITE_CONTENTAZUREFILECONNECTIONSTRING**  | mysecurestorageConnectionString | 作成したストレージ アカウントの接続文字列。 この設定を変更することで、関数アプリは、セキュリティで保護された Azure Files のストレージ アカウントを使用するようになり、これが展開時に使用されます。 |
    | **WEBSITE_CONTENTSHARE** | files | ストレージ アカウントで作成したファイル共有の名前。 このアプリ設定は、WEBSITE_CONTENTAZUREFILECONNECTIONSTRING とともに使用されます。 |
    | **SERVICEBUS_CONNECTION** | myServiceBusConnectionString | Service Bus の接続文字列のアプリ設定を作成します。 これは、「[Service Bus の接続文字列を取得する](#get-service-bus-connection-string)」のストレージ接続文字列です。|
    | **WEBSITE_CONTENTOVERVNET** | 1 | このアプリ設定を作成します。 値 1 を指定すると、ストレージ アカウントを仮想ネットワークに制限している場合に、関数アプリをスケーリングできます。 ストレージ アカウントを仮想ネットワークに制限する場合は、この設定を有効にする必要があります。 |
    | **WEBSITE_DNS_SERVER** | 168.63.129.16 | このアプリ設定を作成します。 アプリが仮想ネットワークと統合されると、仮想ネットワークと同じ DNS サーバーが使用されます。 これは、関数アプリが Azure DNS プライベート ゾーンで動作し、プライベート エンドポイントを使用する場合に必要となる 2 つの設定のうちの 1 つです。 これらの設定は、アプリから仮想ネットワークへのすべての送信呼び出しを送信します。 |
    | **WEBSITE_VNET_ROUTE_ALL** | 1 | このアプリ設定を作成します。 アプリが仮想ネットワークと統合されると、仮想ネットワークと同じ DNS サーバーが使用されます。 これは、関数アプリが Azure DNS プライベート ゾーンで動作し、プライベート エンドポイントを使用する場合に必要となる 2 つの設定のうちの 1 つです。 これらの設定は、アプリから仮想ネットワークへのすべての送信呼び出しを送信します。 |

1. **[構成]** ビューを表示したまま、 **[関数のランタイム設定]** タブを選択します。

1. **[Runtime Scale Monitoring]\(ランタイム スケールの監視\)** を **[オン]** に設定し、 **[保存]** を選択します。 ランタイム駆動型のスケーリングを使用すると、仮想ネットワーク内で実行されているサービスに非 HTTP トリガー関数を接続できます。

    :::image type="content" source="./media/functions-create-vnet/11-enable-runtime-scaling.png" alt-text="Azure Functions のランタイム駆動型スケーリングを有効にする方法を示すスクリーンショット。":::

## <a name="deploy-a-service-bus-trigger-and-http-trigger-to-your-function-app"></a>関数アプリに Service Bus トリガーと HTTP トリガーを展開する

1. GitHub で、次のサンプル リポジトリに移動します。このリポジトリには、HTTP トリガーと Service Bus キュートリガーという 2 つの関数を含む関数アプリが含まれています。

    <https://github.com/Azure-Samples/functions-vnet-tutorial>

1. ページの上部で、 **[フォーク]** ボタンを選択して、独自の GitHub アカウントまたは組織にこのリポジトリのフォークを作成します。

1. 関数アプリで、左側のメニューから **[配置センター]** を選択します。 **[設定]** を選択します。

1. **[設定]** タブで、次のように展開設定を使用します。

    | 設定      | 推奨値  | 説明      |
    | ------------ | ---------------- | ---------------- |
    | **ソース** | GitHub | 手順 2 のサンプル コードを使用して GitHub リポジトリを作成しておく必要があります。 | 
    | **組織**  | myOrganization | これは、リポジトリがチェックインされる組織 (通常はアカウント) です。 |
    | **リポジトリ** | myRepo | サンプル コードを使用して作成したリポジトリ。 |
    | **ブランチ** | メイン | これは先ほど作成したリポジトリなので、メイン ブランチを使用します。 |
    | **ランタイム スタック** | .NET | サンプル コードは C# で作成されています。 |

1. **[保存]** を選択します。 

    :::image type="content" source="./media/functions-create-vnet/12-deploy-portal.png" alt-text="ポータルを使用して Azure Functions コードを展開する方法のを示すスクリーンショット。":::

1. 最初の展開には数分かかる場合があります。 アプリが正常に展開されると、 **[ログ]** タブに **成功 (アクティブ)** を示すステータス メッセージが表示されます。 必要に応じてページを更新します。 

1. おめでとうございます。 サンプルの関数アプリが正常に展開されました。

## <a name="lock-down-your-function-app-with-a-private-endpoint"></a>プライベート エンドポイントを使用して関数アプリをロックダウンする

次に、関数アプリのプライベート エンドポイントを作成します。 このプライベート エンドポイントは、プライベート IP アドレスを使用して、関数アプリをプライベートかつ安全に仮想ネットワークに接続します。 プライベート エンドポイントの詳細については、[プライベート エンドポイントのドキュメント](https://docs.microsoft.com/azure/private-link/private-endpoint-overview)を参照してください。

1. 関数アプリで、左側のメニューの **[ネットワーク]** を選択します。

1. [プライベート エンドポイント接続] で **[構成するにはここをクリック]** を選択します。

    :::image type="content" source="./media/functions-create-vnet/14-navigate-app-private-endpoint.png" alt-text="関数アプリのプライベート エンドポイントに移動する方法を示すスクリーンショット。":::

1. **[追加]** を選択します。

1. 表示されるメニューで、次のように、プライベート エンドポイント設定を使用します。

    :::image type="content" source="./media/functions-create-vnet/15-create-app-private-endpoint.png" alt-text="関数アプリのプライベート エンドポイントを作成する方法を示すスクリーンショット。":::

1. **[OK]** を選択して、プライベート エンドポイントを追加します。 おめでとうございます。 プライベート エンドポイントを使用して、関数アプリ、Service Bus、およびストレージ アカウントをセキュリティで保護しました。

### <a name="test-your-locked-down-function-app"></a>ロックダウンした関数アプリをテストする

1. 関数アプリで、左側のメニューから **[関数]** を選択します。

1. **[ServiceBusQueueTrigger]** を選択します。

1. 左側のメニューから **[監視]** を選択します。 ここで、アプリを監視できないことがわかります。 これは、お使いのブラウザーが仮想ネットワークにアクセスできず、仮想ネットワーク内のリソースに直接アクセスできないためです。 次に、関数を監視する別の方法、Application Insights について説明します。

1. 関数アプリで、左側のメニューの **[Application Insights]** を選択し、 **[Application Insights データの表示]** を選択します。

    :::image type="content" source="./media/functions-create-vnet/16-app-insights.png" alt-text="関数アプリについて Application Insights を表示する方法を示すスクリーンショット。":::

1. 左側のメニューから **[Live Metrics]** を選択します。

1. 新しいタブを開きます。Service Bus で、左側のメニューから **[キュー]** を選択します。

1. キューを選択します。

1. 左側のメニューから **[Service Bus エクスプローラー]** を選択します。 **[送信]** で、 **[コンテンツの種類]** として **[text/plain]** を選択し、メッセージを入力します。 

1. **[送信]** を選択してメッセージを送信します。

    :::image type="content" source="./media/functions-create-vnet/17-send-service-bus-message.png" alt-text="ポータルを使用して Service Bus メッセージを送信する方法を示すスクリーンショット。":::

1. **[Live Metrics]** を開いているタブで、Service Bus キュー トリガーがトリガーされたことを確認できるはずです。 確認できない場合は、 **[Service Bus エクスプローラー]** からメッセージを再送信します。

    :::image type="content" source="./media/functions-create-vnet/18-hello-world.png" alt-text="関数アプリの Live Metrics を使用してメッセージを表示する方法を示すスクリーンショット。":::

1. おめでとうございます。 これで、プライベート エンドポイントを使用した関数アプリの設定のテストが成功しました。

### <a name="private-dns-zones"></a>プライベート DNS ゾーン
プライベート エンドポイントを使用して Azure リソースに接続することは、パブリック エンドポイントではなくプライベート IP アドレスに接続することを意味します。 既存の Azure サービスは、既存の DNS を使用してパブリック エンドポイントに接続するように構成されています。 プライベート エンドポイントに接続するには、DNS 構成を上書きする必要があります。

プライベート DNS ゾーンは、プライベート エンドポイントで構成された各 Azure リソースに対して作成されています。 DNS A レコードは、プライベート エンドポイントに関連付けられているプライベート IP アドレスごとに作成されます。

このチュートリアルでは、次の DNS ゾーンを作成しました。

- privatelink.file.core.windows.net
- privatelink.blob.core.windows.net
- privatelink.servicebus.windows.net
- privatelink.azurewebsites.net

[!INCLUDE [clean-up-section-portal](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、Premium 版の関数アプリ、ストレージ アカウント、Service Bus を作成し、それらすべてをプライベート エンドポイントの背後でセキュリティ保護しました。 各種ネットワーク機能については、以下を参照してください。

> [!div class="nextstepaction"]
> [関数のネットワーク オプションに関する詳細情報](./functions-networking-options.md)

[Premium プラン]: functions-premium-plan.md
