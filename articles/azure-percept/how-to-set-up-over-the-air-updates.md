---
title: 更新を無線でデプロイするように Azure IoT Hub を設定する
description: Azure Percept DK に更新を無線でデプロイするように Azure IoT Hub を構成する方法について説明します
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 03/30/2021
ms.custom: template-how-to
ms.openlocfilehash: 5890ca90b0ad0dcb3d5141e62e986475fd386959
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/31/2021
ms.locfileid: "106064122"
---
# <a name="how-to-set-up-azure-iot-hub-to-deploy-over-the-air-updates-to-your-azure-percept-dk"></a>Azure Percept DK に更新を無線でデプロイするように Azure IoT Hub を設定する方法

無線での更新を使用して、Azure Percept DK を安全で最新の状態に保ちます。 簡単なわずかな手順で、Device Update for IoT Hub を使用して Azure 環境を設定し、Azure Percept DK に最新の更新をデプロイできます。

## <a name="prerequisites"></a>前提条件

- Azure Percept DK (開発キット)
- [Azure サブスクリプション](https://azure.microsoft.com/free/)
- [Azure Percept DK セットアップ エクスペリエンス](./quickstart-percept-dk-set-up.md): 開発キットを Wi-Fi ネットワークに接続し、IoT ハブを作成して、開発キットを IoT ハブに接続済みであること

## <a name="create-a-device-update-account"></a>Device Update アカウントの作成

1. [Azure portal](https://portal.azure.com) にアクセスし、Azure Percept で使用している Azure アカウントでサインインします

1. ページの上部にある検索バーで、「**Device Update for IoT Hub**」と入力します。

1. 検索バーに **[Device Update for IoT Hub]** が表示されたら、それを選択します。

1. ページの左上にある **[+ 追加]** ボタンをクリックします。

1. Azure Percept デバイスとその IoT Hub に関連する **Azure サブスクリプション** と **リソース グループ** を選択します。

1. お使いの Device Update アカウントの **名前** と **場所** を指定します

1. 詳細を確認し、 **[確認および作成]** を選択します。

1. デプロイが完了したら、 **[リソースに移動]** をクリックします。

## <a name="create-a-device-update-instance"></a>デバイス更新インスタンスの作成

1. Device Update for IoT Hub リソース内で、 **[Instance Management]\(インスタンス管理\)** の下の **[インスタンス]** をクリックします。

1. **[+ 作成]** をクリックし、インスタンス名を指定して、お使いの Azure Percept デバイスに関連する IoT Hub を選択します。 これは完了するまでに数分かかる場合があります。

1. **Create** をクリックしてください。

## <a name="configure-iot-hub"></a>IoT Hub を構成する

1. インスタンス管理の **[インスタンス]** ページで、デバイス更新インスタンスが **[成功]** 状態になるまで待機します。 **[最新の情報に更新]** アイコンをクリックして、状態を更新します。

1. 作成されたインスタンスを選択し、 **[Configure IoT Hub]\(IoT Hub の構成\)** をクリックします。 左側のペインで、 **[I agree to make these changes]\(これらの変更を行うことに同意します\)** を選択し、 **[更新]** をクリックします。

1. プロセスが正常に完了するまで待ちます。

## <a name="configure-access-control-roles"></a>アクセス制御ロールの構成

最後の手順では、更新を発行およびデプロイするためのアクセス許可をユーザーに付与できます。

1. Device Update for IoT Hub リソース内で、 **[アクセス制御 (IAM)]** をクリックします。

1. **[+ 追加]** をクリックし、 **[ロールの割り当ての追加]** を選択します

1. **[ロール]** で、 **[Device Update Administrator]\(デバイス更新管理者\)** を選択します。 **[アクセスの割り当て先]** で、 **[User, group, or service principle]\(ユーザー、グループ、またはサービス プリンシパル\)** を選択します。 **[選択]** で、自分のアカウントか、更新をデプロイする人のアカウントを選択します。 **[保存]** をクリックします。

> [!TIP]
> 組織内の他のユーザーにもアクセスを許可する場合は、この手順を繰り返して、それらの各ユーザーを **デバイス更新管理者** にすることができます。

## <a name="next-steps"></a>次のステップ

これで、Device Update for IoT Hub を使用して、[Azure Percept 開発キットを無線で更新](./how-to-update-over-the-air.md)する準備が整いました。