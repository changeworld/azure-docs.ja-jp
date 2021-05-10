---
title: 'チュートリアル: Azure portal で Azure Stack Edge Pro R デバイスをアクティブにする'
description: Azure Stack Edge Pro R を配置するチュートリアルでは、ご利用の物理デバイスをアクティブにする方法について説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 02/23/2021
ms.author: alkohli
ms.openlocfilehash: 2908f9c3cb39debed4f5a1bea38d15ccf6a22822
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/31/2021
ms.locfileid: "106059957"
---
# <a name="tutorial-activate-azure-stack-edge-pro-r-device"></a>チュートリアル:Azure Stack Edge Pro R デバイスをアクティブにする

このチュートリアルでは、ローカル Web UI を使用してご利用の Azure Stack Edge Pro R デバイスをアクティブにする方法について説明します。

このアクティブ化プロセスの所要時間は約 5 分です。  

このチュートリアルで学習した内容は次のとおりです。

> [!div class="checklist"]
> * 前提条件
> * 物理デバイスをアクティブにする

## <a name="prerequisites"></a>前提条件

ご利用の Azure Stack Edge Pro R デバイスを構成および設定する前に、次のことを確認してください。

* 物理デバイスについて: 
    
    - [Azure Stack Edge Pro R の設置](azure-stack-edge-pro-r-deploy-install.md)に関する記事の説明に従って、物理デバイスが設置されていること。
    - [ネットワーク、コンピューティング ネットワーク、Web プロキシの構成](azure-stack-edge-pro-r-deploy-configure-network-compute-web-proxy.md)に関するページの説明に従って、ネットワークとコンピューティング ネットワークの設定を構成していること。
    - **[デバイス]** ページでデバイス名または DNS ドメインを変更した場合は、独自のデバイス証明書をデバイスにアップロードしたか、生成したこと。 この手順を実行していない場合、デバイスのアクティブ化中にエラーが表示され、アクティブ化はブロックされます。 詳細については、[証明書の構成](azure-stack-edge-placeholder.md)に関するページを参照してください。
    
* Azure Stack Edge Pro R デバイスを管理するために作成した Azure Stack Edge サービスからのアクティブ化キーを持っていること。 詳細については、[Azure Stack Edge Pro R の配置の準備](azure-stack-edge-pro-r-deploy-prep.md)に関するページをご覧ください。


## <a name="activate-the-device"></a>デバイスをアクティブにする

1. デバイスのローカル Web UI で、 **[開始]** ページに移動します。
2. **[アクティブ化]** タイルで、 **[アクティブにする]** を選択します。 

    ![ローカル Web UI の [Cloud details]\(クラウドの詳細\) ページ 1](./media/azure-stack-edge-pro-r-deploy-activate/activate-1.png)
    
3. **[アクティブ化]** ウィンドウで次の操作を実行します。
    1. [Azure Stack Edge Pro R のアクティブ化キーの取得](azure-stack-edge-pro-r-deploy-prep.md#get-the-activation-key)に関するセクションの説明に従って取得した **アクティブ化キー** を入力します。

    1. デバイスの正常性状態に基づいて Microsoft がログを収集できるようにするために、プロアクティブなログ収集を有効にすることができます。 この方法で収集されたログは、Azure Storage アカウントにアップロードされます。
    
    1. **[適用]** を選択します。

    ![ローカル Web UI の [Cloud details]\(クラウドの詳細\) ページ 2](./media/azure-stack-edge-pro-r-deploy-activate/activate-2.png)


5. まず、デバイスがアクティブ化されます。 キー ファイルをダウンロードするように求められます。
    
    ![ローカル Web UI の [Cloud details]\(クラウドの詳細\) ページ 3](./media/azure-stack-edge-pro-r-deploy-activate/activate-3.png)
    
    **[Download and continue]\(ダウンロードして続行\)** を選択し、*device-serial-no.json* ファイルをデバイスの外部の安全な場所に保存します。 **このキー ファイルには、デバイス上の OS ディスクとデータ ディスクの回復キーが含まれています**。 将来のシステム回復を容易にするために、これらのキーが必要になる場合があります。

    *json* ファイルの内容を次に示します。

        
    ```json
    {
      "Id": "<Device ID>",
      "DataVolumeBitLockerExternalKeys": {
        "hcsinternal": "<BitLocker key for data disk>",
        "hcsdata": "<BitLocker key for data disk>"
      },
      "SystemVolumeBitLockerRecoveryKey": "<BitLocker key for system volume>",
      "ServiceEncryptionKey": "<Azure service encryption key>"
    }
    ```
        
 
    次の表では、さまざまなキーについて説明します。
    
    |フィールド  |説明  |
    |---------|---------|
    |`Id`    | これはデバイスの ID です。        |
    |`DataVolumeBitLockerExternalKeys`|これらはデータ ディスクの BitLockers キーであり、デバイス上のローカル データを復旧するために使用されます。|
    |`SystemVolumeBitLockerRecoveryKey`| これはシステム ボリュームの BitLocker キーです。 このキーは、デバイスのシステム構成とシステム データの回復に役立ちます。 |
    |`ServiceEncryptionKey`| このキーによって、Azure サービスを経由するデータが保護されます。 このキーがあることで、Azure サービスが侵害されても、格納されている情報は侵害されません。 |

6. **[概要]** ページに移動します。 デバイスの状態は **[アクティブ化済み]** と表示されます。

    ![ローカル Web UI の [Cloud details]\(クラウドの詳細\) ページ 4](./media/azure-stack-edge-gpu-deploy-activate/activate-4.png)
 
デバイスのアクティブ化は完了しました。 これで、デバイスで共有を追加できます。

アクティベーション中に問題が発生した場合は、[アクティベーションと Azure Key Vault のエラーのトラブルシューティング](azure-stack-edge-gpu-troubleshoot-activation.md#activation-errors)に関する記事を参照してください。

## <a name="next-steps"></a>次のステップ

このチュートリアルで学習した内容は次のとおりです。

> [!div class="checklist"]
> * 前提条件
> * 物理デバイスをアクティブにする

ご利用の Azure Stack Edge Pro R デバイスを使用してデータを転送する方法については、次を参照してください。

> [!div class="nextstepaction"]
> [Azure Stack Edge Pro R を使用してデータを転送する](./azure-stack-edge-gpu-deploy-add-shares.md)