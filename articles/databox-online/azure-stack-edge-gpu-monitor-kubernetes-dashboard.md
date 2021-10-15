---
title: Kubernetes ダッシュボードを使用して Azure Stack Edge Pro デバイスを監視する | Microsoft Docs
description: Kubernetes ダッシュボードへのアクセス方法と、これを使用して Azure Stack Edge Pro デバイスを監視する方法について説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/31/2021
ms.author: alkohli
ms.openlocfilehash: 30e46f9425f4015893c08b94382e87cfa93c8be8
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/01/2021
ms.locfileid: "129363157"
---
# <a name="use-kubernetes-dashboard-to-monitor-your-azure-stack-edge-pro-gpu-device"></a>Kubernetes ダッシュボードを使用して Azure Stack Edge Pro GPU デバイスを監視する

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

この記事では、Kubernetes ダッシュボードへのアクセス方法と、これを使用して Azure Stack Edge Pro GPU デバイスを監視する方法について説明します。 デバイスを監視するには、Azure portal のグラフを使用するか、Kubernetes ダッシュボードを表示するか、デバイスの PowerShell インターフェイスを使用して `kubectl` コマンドを実行します。 

この記事では、Kubernetes ダッシュボードで実行できる監視タスクについてのみ説明します。

この記事では、次のことについて説明します。

> [!div class="checklist"]
>
> * デバイス上の Kubernetes ダッシュボードにアクセスする
> * デバイスにデプロイされているモジュールを表示する
> * デバイスにデプロイされているアプリケーションの IP アドレスを取得する
> * デバイスにデプロイされているモジュールのコンテナー ログを表示する


## <a name="about-kubernetes-dashboard"></a>Kubernetes ダッシュボードについて

Kubernetes ダッシュボードは、Web ベースのユーザー インターフェイスで、コンテナー化されたアプリケーションのトラブルシューティングに使用できます。 Kubernetes ダッシュボードは、Kubernetes `kubectl` コマンド ラインの UI ベースの代替手段です。 詳細については、[Kubernetes ダッシュボード](https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/)に関するページを参照してください。 

Azure Stack Edge Pro デバイスでは、Kubernetes ダッシュボードを "*読み取り専用*" モードで使用して、Azure Stack Edge Pro デバイスで実行されているアプリケーションの概要を取得したり、Kubernetes クラスター リソースの状態を表示したり、デバイスで発生したエラーを確認したりできます。

## <a name="access-dashboard"></a>ダッシュボードへのアクセス

Kubernetes ダッシュボードは "*読み取り専用*" で、ポート 31000 の Kubernetes マスター ノードで実行されます。 ダッシュボードにアクセスするには、次の手順に従います。 

1. お使いのデバイスのローカル UI で、 **[デバイス]** 、 **[デバイス エンドポイント]** の順に移動します。 
1. **[Kubernetes ダッシュボード]** エンドポイントをコピーします。 Kubernetes ダッシュボードに接続するには、DNS エントリをクライアントの `C:\Windows\System32\Drivers\etc\hosts` ファイルに作成します。 

    `<IP address of the Kubernetes dashboard>    <Kubernetes dashboard endpoint suffix>` 
        
    ![Kubernetes ダッシュボード エンドポイントの DNS エントリの追加](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/add-domain-name-service-entry-hosts-1.png) 

1. **[Kubernetes ダッシュボード]** エンドポイントの行で、 **[config のダウンロード]** を選択します。この操作により、ダッシュボードにアクセスできるようにする `kubeconfig` をダウンロードします。 `config.json` ファイルをローカル システムに保存します。   

1. ローカル UI から Kubernetes ダッシュボード証明書をダウンロードします。 
    1. デバイスのローカル UI で、 **[証明書]** にアクセスします。
    1. **[Kubernetes ダッシュボード エンドポイント証明書]** のエントリを見つけます。 このエントリの右側にある **[ダウンロード]** を選択して、ダッシュボードへのアクセスに使用するクライアント システムの証明書をダウンロードします。 

    ![Kubernetes ダッシュボード エンドポイント証明書のダウンロード](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/download-kubernetes-dashboard-endpoint-certificate-1.png)  

1. ダウンロードした証明書をクライアントにインストールします。 Windows クライアントを使用している場合は、次の手順を実行します。 
    1. 証明書を選択し、 **[証明書のインポート ウィザード]** で、保存場所として **[ローカル コンピューター]** を選択します。 

        ![クライアント 1 での証明書のインストール](media/azure-stack-edge-gpu-edge-container-registry/install-certificate-1.png) 
    
    1. 証明書を信頼されたルート ストアのローカル コンピューターにインストールします。 

        ![クライアント 2 での証明書のインストール](media/azure-stack-edge-gpu-edge-container-registry/install-certificate-2.png) 
1. Kubernetes ダッシュボード URL をコピーして、ブラウザーでダッシュボードを開くために使用します。 **Kubernetes ダッシュボードのサインイン** ページで次を行います。
    
    1. **kubeconfig** を選択します。 
    1. 省略記号 ( **...** ) を選択します。ローカル システムで以前にダウンロードした `kubeconfig` を参照して指定します。 **[サインイン]** を選択します。
        ![kubeconfig ファイルを参照](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/kubernetes-dashboard-sign-in-2.png)    

6. これで Azure Stack Edge Pro デバイスの Kubernetes ダッシュボードを読み取り専用モードで表示できるようになりました。

    ![Kubernetes ダッシュボードのメイン ページ](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/kubernetes-dashboard-main-page-1.png)

## <a name="view-module-status"></a>モジュールの状態の表示

コンピューティング モジュールは、ビジネス ロジックが実装されたコンテナーです。 ダッシュボードを使用して、Azure Stack Edge Pro デバイスにコンピューティング モジュールが正常にデプロイされたかどうかを確認できます。

モジュールの状態を表示するには、ダッシュボードで次の手順を行います。

1. ダッシュボードの左側のペインで、 **[名前空間]** に移動します。 IoT Edge モジュールが表示されている名前空間でフィルター処理します。この場合は **iotedge** です。
1. 左側のペインで、 **[ワークロード] > [デプロイ]** の順に移動します。
1. 右側のペインには、デバイスにデプロイされているすべてのモジュールが表示されます。 この場合、GettingStartedWithGPU モジュールが Azure Stack Edge Pro にデプロイされています。 モジュールがデプロイされたことを確認できます。

    ![モジュールのデプロイの表示](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/kubernetes-view-module-deployment-1.png)

 
## <a name="get-ip-address-for-services-or-modules"></a>サービスまたはモジュールの IP アドレスを取得する

ダッシュボードを使用して、Kubernetes クラスターの外部で公開するサービスまたはモジュールの IP アドレスを取得できます。 

**[Compute network settings]\(コンピューティング ネットワーク設定\)** ページで、デバイスのローカル Web UI を介してこれらの外部サービスに IP 範囲を割り当てます。 IoT Edge モジュールをデプロイすると、特定のモジュールまたはサービスに割り当てられた IP アドレスを取得することができます。 

IP アドレスを取得するには、ダッシュボードで次の手順を実行します。

1. ダッシュボードの左側のペインで、 **[名前空間]** に移動します。 外部サービスがデプロイされている名前空間でフィルター処理します。この場合は **iotedge** です。
1. 左側のペインで、 **[検出と負荷分散] > [サービス]** に移動します。
1. 右側のペインには、Azure Stack Edge Pro デバイスの `iotedge` 名前空間で実行されているすべてのサービスが表示されます。

    ![外部サービスの IP を取得する](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/kubernetes-get-ip-external-service-1.png)

## <a name="view-container-logs"></a>コンテナー ログの表示

コンテナー ログを表示する必要があるインスタンスがあります。 ダッシュボードを使用して、Kubernetes クラスターにデプロイした特定のコンテナーのログを取得できます。

コンテナー ログを表示するには、ダッシュボードで次の手順を実行します。

1. ダッシュボードの左側のペインで、 **[名前空間]** に移動します。 IoT Edge モジュールがデプロイされている名前空間でフィルター処理します。この場合は **iotedge** です。
1. 左側のペインで、 **[ワークロード] > [ポッド]** の順に移動します。
1. 右側のペインには、デバイスで実行されているすべてのポッドが表示されます。 ログを表示するモジュールを実行しているポッドを特定します。 特定したポッドの縦方向の省略記号を選択し、コンテキスト メニューから **[ログ]** を選択します。

    ![コンテナー ログの表示 1](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/kubernetes-view-container-logs-1.png)

1. ログは、ダッシュボードに組み込まれているログ ビューアーに表示されます。 ログはダウンロードすることもできます。

    ![コンテナー ログの表示 2](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/kubernetes-view-container-logs-1.png)
    

## <a name="view-cpu-memory-usage"></a>CPU、メモリの使用量の表示

Azure Stack Edge Pro デバイスの Kubernetes ダッシュボードには、Kubernetes リソース全体の CPU とメモリの使用量を集計する[メトリック サーバーのアドオン](https://kubernetes.io/docs/tasks/debug-application-cluster/resource-metrics-pipeline/)もあります。
 
たとえば、すべての名前空間のデプロイ全体で消費された CPU とメモリを表示できます。 

![すべてのデプロイの CPU とメモリの使用量の表示](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/view-cpu-memory-all-1.png)

また、特定の名前空間でフィルター処理を行うこともできます。 次の例では、Azure Arc デプロイについてのみ CPU とメモリの消費量を表示できます。  

![Azure Arc デプロイの CPU とメモリの使用量の表示](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/view-cpu-memory-azure-arc-1.png)

Kubernetes メトリック サーバーには、[ポッドの水平オートスケーラー](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/)などの自動スケール パイプラインが用意されています。


## <a name="next-steps"></a>次のステップ

[Azure Monitor を使用して監視](azure-stack-edge-gpu-enable-azure-monitor.md)する方法を確認する。
[診断を実行してログを収集](azure-stack-edge-gpu-troubleshoot.md)する方法を確認する。
