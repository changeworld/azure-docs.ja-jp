---
title: Device Update for Azure IoT Hub でデバイス更新アカウントを作成する | Microsoft Docs
description: Device Update for Azure IoT Hub でデバイス更新アカウントを作成します。
author: vimeht
ms.author: vimeht
ms.date: 2/11/2021
ms.topic: how-to
ms.service: iot-hub-device-update
ms.custom: subject-rbac-steps
ms.openlocfilehash: 5ad119017f4fe1dda0703547480c7444978d1878
ms.sourcegitcommit: 695a33a2123429289ac316028265711a79542b1c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/01/2021
ms.locfileid: "113129052"
---
# <a name="device-update-for-iot-hub-resource-management"></a>Device Update for IoT Hub リソース管理

Device Update の使用を開始するには、Device Update アカウント、インスタンスを作成し、アクセス制御ロールを設定する必要があります。 

## <a name="prerequisites"></a>前提条件

* IoT Hub へのアクセス。 S1 (Standard) レベル以上を使用することをお勧めします。 
* サポートされているブラウザー:
  * [Microsoft Edge](https://www.microsoft.com/edge)
  * Google Chrome

## <a name="create-a-device-update-account"></a>Device Update アカウントの作成

1. [Azure portal](https://portal.azure.com) に移動します

2. **[リソースの作成]** をクリックし、[Device Update for IoT Hub] を探します

   :::image type="content" source="media/create-device-update-account/device-update-marketplace.png" alt-text="Device Update for IoT Hub リソースのスクリーンショット。" lightbox="media/create-device-update-account/device-update-marketplace.png":::

3. **[作成]**  ->  **[Device Update for IoT Hub]** をクリックします。

4. Device Update アカウントおよびリソース グループに関連付ける Azure サブスクリプションを指定します

5. Device Update アカウントの名前と場所を指定します

   :::image type="content" source="media/create-device-update-account/account-details.png" alt-text="アカウントの詳細のスクリーンショット。" lightbox="media/create-device-update-account/account-details.png":::

 > [!NOTE]
 > [リージョン別の Azure 製品ページ](https://azure.microsoft.com/global-infrastructure/services/?products=iot-hub)に移動して、Device Update for IoT Hub が利用可能なリージョンを見つけることができます。 Device Update for IoT Hub がリージョンで利用できない場合は、最も近い利用可能なリージョンにアカウントを作成するように選択できます。 

6. 必要に応じて、チェック ボックスをオンにして、デバイス更新管理者ロールを自分に割り当てることができます。 「アクセス制御ロールの構成」セクションに記載されている手順を使用し、ロールの組み合わせを使ってユーザーとアプリケーションに適切なレベルのアクセスを提供することもできます。

8. **[次へ: 確認と作成 >]** をクリックします

   :::image type="content" source="media/create-device-update-account/account-review.png" alt-text="アカウントの詳細の確認のスクリーンショット。" lightbox="media/create-device-update-account/account-review.png":::

7. 詳細を確認し、 **[作成]** を選択します。 デプロイが進行中であることがわかります。 

   :::image type="content" source="media/create-device-update-account/account-deployment-inprogress.png" alt-text="アカウントのデプロイが進行中のスクリーンショット。" lightbox="media/create-device-update-account/account-deployment-inprogress.png":::

8. 数分でデプロイの状態が "完了" に変わるのがわかります。 **[リソースに移動]** をクリックします。

   :::image type="content" source="media/create-device-update-account/account-complete.png" alt-text="アカウントのデプロイの完了のスクリーンショット。" lightbox="media/create-device-update-account/account-complete.png":::

## <a name="create-a-device-update-instance"></a>Device Update インスタンスの作成 

Device Update のインスタンスは、1 つの IoT ハブに関連付けられます。 Device Update で使用する IoT ハブを選択します。 この手順で新しい共有アクセス ポリシーを作成し、Device Update で IoT ハブを操作する (レジストリ書き込みとサービス接続) ために必要なアクセス許可のみが使用されるようにします。 このポリシーにより、アクセスは Device Update のみに制限されます。

アカウントが作成されたら、Device Update インスタンスを作成します。

1. 新しく作成したアカウント リソースで、[インスタンス管理] の **[インスタンス]** ブレードに移動します

   :::image type="content" source="media/create-device-update-account/instance-blade.png" alt-text="アカウント内のインスタンス管理のスクリーンショット。" lightbox="media/create-device-update-account/instance-blade.png":::

2. **[作成]** をクリックし、インスタンス名を指定して、IoT Hub を選択します

   :::image type="content" source="media/create-device-update-account/instance-details.png" alt-text="インスタンスの詳細のスクリーンショット。" lightbox="media/create-device-update-account/instance-details.png":::

   > [!NOTE] 
   > Device Update リソースにリンクする IoT ハブは、Device Update アカウントと同じリージョンにある必要はありません。 ただし、パフォーマンス向上のため、Device Update アカウントのリージョンと同じか、または近いリージョンに IoT ハブを置くことが推奨されます。 

3. **[作成]** をクリックします。 インスタンスが [作成中] 状態で表示されます。 

   :::image type="content" source="media/create-device-update-account/instance-creating.png" alt-text="インスタンスが作成中のスクリーンショット。" lightbox="media/create-device-update-account/instance-creating.png":::

4. インスタンスのデプロイが完了するまでに 5 から 10 分かかります。 [プロビジョニングの状態] が [成功] になるまで状態を更新します。

   :::image type="content" source="media/create-device-update-account/instance-succeeded.png" alt-text="インスタンス作成が成功したスクリーンショット。" lightbox="media/create-device-update-account/instance-succeeded.png":::

## <a name="configure-iot-hub"></a>IoT Hub を構成する 

Device Update で、IoT Hub からの変更通知を受け取るために、Device Update は "組み込みの" イベント ハブと統合されています。 [IoT Hub の構成] ボタンをクリックすると、IoT デバイスと通信するために必要なメッセージ ルートとアクセス ポリシーが構成されます。 

IoT Hub を構成するには

1. インスタンスの [プロビジョニングの状態] が [成功] に変わったら、[インスタンスの管理] ブレードでインスタンスを選択します。 **[IoT Hub の構成]** をクリックします

   :::image type="content" source="media/create-device-update-account/instance-configure.png" alt-text="インスタンスの IoT Hub の構成のスクリーンショット。" lightbox="media/create-device-update-account/instance-configure.png":::

2. **[これらの変更を行うことに同意する]** を選択します

   :::image type="content" source="media/create-device-update-account/instance-configure-selected.png" alt-text="インスタンスの IoT Hub の構成に同意するスクリーンショット。" lightbox="media/create-device-update-account/instance-configure-selected.png":::

3. **[Update]\(更新\)** をクリックします。

   > [!NOTE] 
   > Azure IoT Hub の Free レベルを使用している場合、許可されるメッセージ ルートの数は 5 つに制限されます。 Device Update for IoT Hub では、4 つのメッセージ ルートが想定どおりに動作するように構成する必要があります。 

[構成されたメッセージ ルートを確認します。](device-update-resources.md) 


## <a name="configure-access-control-roles"></a>アクセス制御ロールの構成

他のユーザーが Device Update にアクセスできるようにするには、ユーザーにこのリソースへのアクセス権が付与されている必要があります。 アカウントの作成時にデバイス更新管理者ロールを自分に割り当てており、追加のユーザーやアプリケーションにアクセス権を提供する必要がない場合は、この手順をスキップできます。 

1. Device Update アカウント内のアクセス制御 (IAM) に移動します

   :::image type="content" source="media/create-device-update-account/account-access-control.png" alt-text="Device Update アカウント内のアクセス制御のスクリーンショット。" lightbox="media/create-device-update-account/account-access-control.png":::

2. **[ロールの割り当ての追加]** をクリックします

3. [ロール] タブで、指定したオプションから Device Update ロールを選択します
     - Device Update 管理者
     - Device Update 閲覧者
     - Device Update コンテンツ管理者
     - Device Update コンテンツ閲覧者
     - Device Update デプロイ管理者
     - Device Update デプロイ閲覧者
     
   :::image type="content" source="media/create-device-update-account/role-assignment.png" alt-text="Device Update アカウント内のアクセス制御ロール割り当てのスクリーンショット。" lightbox="media/create-device-update-account/role-assignment.png":::
    
    [Device Update for IoT Hub でのロールベースのアクセス制御について確認します](device-update-control-access.md) 
    
4. **[次へ]** をクリックします。
5. ユーザーまたは Azure AD グループにアクセス権を割り当てます
6. メンバーを選択します
   
   :::image type="content" source="media/create-device-update-account/role-assignment-2.png" alt-text="Device Update アカウント内でのアクセス制御のメンバー選択のスクリーンショット。" lightbox="media/create-device-update-account/role-assignment-2.png":::

6. **[確認と割り当て]** をクリックします
7. 新しいロールの割り当てを確認し、 **[確認と割り当て]** をもう一度クリックします
8. これで、IoT Hub 内から Device Update エクスペリエンスを使用する準備ができました

## <a name="next-steps"></a>次の手順

次のクイック チュートリアルのいずれかを使用して、デバイスを更新してみてください。

 - [シミュレーターでのデバイス更新](device-update-simulator.md)
 - [Raspberry Pi でのデバイス更新](device-update-raspberry-pi.md)
 - [Ubuntu Server 18.04 x64 Package エージェントでのデバイス更新](device-update-ubuntu-agent.md)

[Device Update アカウントおよびインスタンスについて確認します。](device-update-resources.md) 

[Device Update アクセス制御ロールについて確認します。](device-update-control-access.md) 

