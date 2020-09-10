---
title: Microsoft Azure Stack Edge GPU デバイスで GPU モジュールを実行する | Microsoft Docs
description: Azure portal を使用して、Azure Stack Edge デバイス上の GPU でモジュールを構成して実行する方法について説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/25/2020
ms.author: alkohli
ms.openlocfilehash: 419b8beb866711e80b4366df4398eb248256021b
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/01/2020
ms.locfileid: "89266954"
---
# <a name="configure-and-run-a-module-on-gpu-on-azure-stack-edge-device"></a>Azure Stack Edge デバイス上の GPU でモジュールを構成して実行する

Azure Stack Edge デバイスには、1 つ以上のグラフィックス処理装置 (GPU) が含まれています。 GPU は、並列処理機能を提供し、中央処理装置 (CPU) より画像のレンダリングを速く処理できるため、AI 計算によく使用されます。 Azure Stack Edge デバイスに含まれる GPU の詳細については、[Azure Stack Edge デバイスの技術仕様](azure-stack-edge-gpu-technical-specifications-compliance.md)を参照してください。

この記事では、Azure Stack Edge デバイス上の GPU でモジュールを構成して実行する方法について説明します。 この記事では、Nvidia T4 GPU 用に記述された、一般公開されているコンテナー モジュールの **Digits** を使用します。 この手順は、Nvidia がこの GPU 用に公開している他のすべてのモジュールの構成にも使用できます。


## <a name="prerequisites"></a>前提条件

開始する前に次の点を確認します。

1. GPU 対応の 1 ノード Azure Stack Edge デバイスにアクセスできること。 このデバイスが Azure のリソースでアクティブになっていること。  

## <a name="configure-module-to-use-gpu"></a>GPU を使用するようにモジュールを構成する

Azure Stack Edge デバイス上の GPU を使用するようにモジュールを構成して、モジュールを実行するには、次の手順に従います。

1. Azure portal で、デバイスに関連付けられているリソースに移動します。 

2. **[Edge コンピューティング]、[開始]** の順に移動します。 **[Edge コンピューティングの構成]** タイルで、[構成] を選択します。

    ![GPU を使用するようにモジュールを構成する 1](media/azure-stack-edge-j-series-configure-gpu-modules/configure-compute-1.png)

3. **[Edge コンピューティングの構成]** ブレードで以下を行います。

    1. **[IoT Hub]** には、 **[新規作成]** を選択します。
    2. そのデバイス用に作成する IoT Hub リソースの名前を指定します。 Free レベルを使用するには、既存のリソースを選択します。 
    3. その IoT Hub リソースで作成される IoT Edge デバイスと IoT ゲートウェイ デバイスをメモしておきます。 後の手順でこの情報を使用することになります。

    ![GPU を使用するようにモジュールを構成する 2](media/azure-stack-edge-j-series-configure-gpu-modules/configure-compute-2.png)

4. IoT Hub リソースの作成には数分かかります。 リソースが作成されたら、 **[Edge コンピューティングの構成]** タイルで **[構成の表示]** を選択し、IoT Hub リソースの詳細を表示します。

    ![GPU を使用するようにモジュールを構成する 4](media/azure-stack-edge-j-series-configure-gpu-modules/configure-compute-4.png)

5. **[デバイスの自動管理] > [IoT Edge]** の順に移動します。

    ![GPU を使用するようにモジュールを構成する 6](media/azure-stack-edge-j-series-configure-gpu-modules/configure-gpu-2.png)

    右側のウィンドウに、Azure Stack Edge デバイスに関連付けられている IoT Edge デバイスが表示されます。 これは、前の手順で IoT Hub リソースを作成するときに作成した IoT Edge デバイスに対応しています。 
    
6. この IoT Edge デバイスを選択します。

   ![GPU を使用するようにモジュールを構成する 7](media/azure-stack-edge-j-series-configure-gpu-modules/configure-gpu-3.png)

7.  **[Set modules]\(モジュールの設定\)** を選びます。

    ![GPU を使用するようにモジュールを構成する 8](media/azure-stack-edge-j-series-configure-gpu-modules/configure-gpu-4.png)

8. **[+ 追加]** を選択し、 **[+ IoT Edge モジュール]** を選択します。 

    ![GPU を使用するようにモジュールを構成する 9](media/azure-stack-edge-j-series-configure-gpu-modules/configure-gpu-5.png)

9. **[IoT Edge モジュールを追加する]** タブで以下を行います。

    1. **[イメージの URI]** を指定します。 ここでは、一般公開されている Nvidia モジュールの **Digits** を使用します。 
    
    2. **[再起動ポリシー]** を **[常時]** に設定します。
    
    3. **[必要な状態]** を **[実行中]** に設定します。
    
    ![GPU を使用するようにモジュールを構成する 10](media/azure-stack-edge-j-series-configure-gpu-modules/configure-gpu-6.png)

10. **[環境変数]** タブで、変数の名前と対応する値を指定します。 

    1. 現在のモジュールがこのデバイス上の GPU の 1 つを使用するようにするため、NVIDIA_VISIBLE_DEVICES を使用します。 

    2. 値を 0 または 1 に設定します。 これにより、少なくとも 1 つの GPU がこのモジュールのためにデバイスによって使用されます。 値を「0, 1」に設定すると、デバイス上の両方の GPU がこのモジュールによって使用されていることを意味します。

        ![GPU を使用するようにモジュールを構成する 11](media/azure-stack-edge-j-series-configure-gpu-modules/configure-gpu-7.png)

        Nvidia GPU で使用できる環境変数の詳細については、[nVidia コンテナー ランタイム](https://github.com/NVIDIA/nvidia-container-runtime#environment-variables-oci-spec)に関するセクションを参照してください。

    > [!NOTE]
    > GPU は 1 つのモジュールにのみマップできます。 ただし、モジュールは、1 つの GPU を使用することも両方の GPU を使用することもできますし、GPU を使用しないこともできます。 

11. モジュールの名前を入力します。 ここで、コンテナーの作成オプションを指定したり、モジュール ツインの設定を変更したりするように選択できます。完了したら、 **[追加]** を選択します。 

    ![GPU を使用するようにモジュールを構成する 12](media/azure-stack-edge-j-series-configure-gpu-modules/configure-gpu-8.png)

12. モジュールが実行されていることを確認し、 **[確認および作成]** を選択します。    

    ![GPU を使用するようにモジュールを構成する 13](media/azure-stack-edge-j-series-configure-gpu-modules/configure-gpu-9.png)

13. **[確認および作成]** タブに、選択したデプロイ オプションが表示されます。 オプションを確認し、 **[作成]** を選択します。
    
    ![GPU を使用するようにモジュールを構成する 14](media/azure-stack-edge-j-series-configure-gpu-modules/configure-gpu-10.png)

14. モジュールの **[ランタイムの状態]** をメモします。 
    
    ![GPU を使用するようにモジュールを構成する 15](media/azure-stack-edge-j-series-configure-gpu-modules/configure-gpu-11.png)

    モジュールのデプロイには数分かかります。 **[更新]** を選択すると、 **[ランタイムの状態]** が **[実行中]** に更新されていることが確認できます。

    ![GPU を使用するようにモジュールを構成する 16](media/azure-stack-edge-j-series-configure-gpu-modules/configure-gpu-12.png)


## <a name="next-steps"></a>次のステップ

- [Nvidia GPU で使用できる環境変数](https://github.com/NVIDIA/nvidia-container-runtime#environment-variables-oci-spec)の詳細を確認する。
