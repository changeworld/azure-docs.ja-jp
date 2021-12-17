---
author: v-dalc
ms.service: databox
ms.topic: include
ms.date: 08/17/2021
ms.author: alkohli
ms.openlocfilehash: 708ac746156582291cf071e730701013f1e005b0
ms.sourcegitcommit: ddac53ddc870643585f4a1f6dc24e13db25a6ed6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/18/2021
ms.locfileid: "122397866"
---
Azure Edge ハードウェア センターを通して注文されたデバイスの管理リソースを作成するには、次の手順を実行します。

1. Microsoft Azure 資格情報を使用し、Azure portal にサインインします。URL は [https://portal.azure.com](https://portal.azure.com) です。

1. 新しい管理リソースの作成を開始するには、次の 2 つの方法があります。

    - Azure Edge Hardware Center を使用する: **[Azure Edge Hardware Center]** を検索して選択します。 Hardware Center で、 **[すべての注文項目]** を表示します。 項目の **[名前]** をクリックします。 項目の **[概要]** で、 **[ハードウェアの構成]** を選択します。
    
       **[ハードウェアの構成]** オプションは、デバイスの出荷後に表示されます。 

       ![Azure Edge Hardware Center の注文項目から管理リソース作成を開始するための 4 つの手順を示す図。](media/azure-edge-hardware-center-create-management-resource/create-management-resource-01-a.png#lightbox) 
    
    - Azure Stack Edge を使用する: **[Azure Stack Edge]** を検索して選択します。 **[+ 作成]** を選択します。 次に、 **[管理リソースの作成]** を選択します。
    
       ![Azure Stack Edge で管理リソース作成を開始するための 3 つの手順を示す図。](media/azure-edge-hardware-center-create-management-resource/create-management-resource-01-b.png#lightbox) 

    **[Create management resource]\(管理リソースの作成\)** ウィザードが開きます。

1. **[基本]** タブで、以下の設定を入力します。

    |設定                                  |値                                                                                       |
    |-----------------------------------------|--------------------------------------------------------------------------------------------|
    |**サブスクリプションの選択**<sup>1</sup>    |管理リソースで使用するサブスクリプションを選択します。                                 |
    |**リソース グループ**<sup>1</sup>           |管理リソースで使用するリソース グループを選択します。 |
    |**名前**                                 |管理リソースの名前を指定します。                                                 |
    |**Azure リソースのデプロイ先**             |管理リソースのメタデータが保管される国またはリージョンを選択します。 メタデータは、物理デバイスとは異なる場所に格納できます。 |
    |**デバイスの種類**<sup>2</sup>              |デバイスの種類を選択します。 このオプションは、Azure Edge Hardware Center の注文でハードウェア製品に対して選択された構成に対応します。<br>たとえば、Azure Stack Edge Pro - GPU デバイスの場合、デバイスの種類は **Azure Stack Edge Pro - 1 GPU** と **Azure Stack Edge Pro - 1 GPU** のどちらかです。|       

    <sup>1</sup> 組織は、デバイスの管理に使用するものとは異なるサブスクリプションとリソース グループを使用してデバイスを注文できます。

    <sup>2</sup> 注文項目から **[ハードウェアの構成]** を使用すると、デバイスの種類は注文項目から取得され、 **[デバイスの詳細]** は表示されません。 

    ![[管理リソースの作成] の [基本] タブのスクリーンショット。 [基本] タブ、オプション、および [確認と作成] ボタンが強調表示されています。](media/azure-edge-hardware-center-create-management-resource/create-management-resource-02.png)

    **[Review + create]\(確認と作成\)** を選択して続行します。

5. **[Review + create]\(確認と作成\)** タブで、管理リソースの基本設定と使用条件を確認します。 **[作成]** を選択します。

    - Azure Edge Hardware Center の注文で配送済み項目の **[ハードウェアの構成]** をクリックしてこの手順を開始した場合は、デバイス、注文リソース名、注文の状態が画面の上部に表示されます。 

      ![Azure Edge Hardware Center の注文項目に対して Azure Stack Edge 管理リソースを作成しているときの [確認と作成] タブのスクリーンショット。 デバイスの注文情報が強調表示されています。](media/azure-edge-hardware-center-create-management-resource/create-management-resource-03.png)

    - Azure Stack Edge で開始した場合は、デバイスの注文情報ではなく、デバイスの種類が **[基本]** に一覧表示されます。 

      ![Azure Stack Edge 管理リソースをAzure Stack Edge で開始しているときの [確認と作成] タブのスクリーンショット。 デバイスの種類が [基本] で強調表示されています。](media/azure-edge-hardware-center-create-management-resource/create-management-resource-04.png)  

    **[作成]** ボタンは、すべての検証チェックに合格するまで使用できません。

6. プロセスが完了すると、新しいリソースの [概要] ペインが開きます。

    ![Azure Stack Edge の完了した管理リソースを示すスクリーンショット。](media/azure-edge-hardware-center-create-management-resource/create-management-resource-05\.png) 
