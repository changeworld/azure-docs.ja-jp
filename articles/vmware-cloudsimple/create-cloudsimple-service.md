---
title: CloudSimple による Azure VMware ソリューションの作成 - サービス
description: Azure portal で CloudSimple サービスを作成する方法について説明します
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/04/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: fc08ce48b3b5a4b2cd28cb66e6dd4d9eae3f3885
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/04/2019
ms.locfileid: "66576675"
---
# <a name="create-azure-vmware-solution-by-cloudsimple---service"></a>CloudSimple による Azure VMware ソリューションの作成 - サービス

CloudSimple による Azure VMware ソリューションを開始するには、Azure portal で CloudSimple による Azure VMware ソリューション サービスを作成します。

> [!NOTE]
> CloudSimple サービスを作成する前に、Azure サブスクリプションに Microsoft.VMwareCloudSimple リソース プロバイダーを登録する必要があります。 [Azure サブスクリプションで Microsoft.VMwareCloudSimple リソース プロバイダーを有効にする](enable-cloudsimple-service.md)方法の手順を実行します。

## <a name="sign-in-to-azure"></a>Azure へのサインイン

Azure Portal ([https://portal.azure.com](https://portal.azure.com)) にサインインします。

## <a name="create-the-service"></a>サービスの作成

1. **[すべてのサービス]** を選択します。

2. **CloudSimple Services** を検索します。

    ![CloudSimple サービスを検索します。](media/create-cloudsimple-service-search.png)

3. **CloudSimple サービス**を選択します。

4. 新しいサービスを作成するには、**Add** (追加) をクリックします。

    ![CloudSimple サービスを追加します。](media/create-cloudsimple-service-add.png)

5. CloudSimple サービスを作成するサブスクリプションを選択します。

6. サービスのリソース グループを選択します。 リソース グループを新規に追加するには、 **[Create New]** (新規作成) をクリックします。

7. サービスを識別する名前を入力します。

8. サービス ゲートウェイの CIDR を入力します。 既存のサブネットと重複しない /28 サブネットを指定します。  これらには、オンプレミスのサブネット、Azure サブネット、または予定されているすべての CloudSimple サブネットが含まれます。 サービスを作成した後は、CIDR を変更できません。

    ![CloudSimple サービスの作成](media/create-cloudsimple-service.png)

9. Click **OK**.

サービスが作成され、サービスの一覧に追加されます。

## <a name="next-steps"></a>次の手順

* [プライベート クラウドを作成する](https://docs.azure.cloudsimple.com/create-private-cloud/)方法を学習する
* [プライベート クラウドの環境を構成する](quickstart-create-private-cloud.md)方法を学習する
