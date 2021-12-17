---
title: インクルード ファイル
description: インクルード ファイル
author: timlt
ms.service: iot-develop
ms.topic: include
ms.date: 04/28/2021
ms.author: timlt
ms.custom: include file
ms.openlocfilehash: 210978e3ecc817c55103267014fef9bd9e36a0e7
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131032780"
---
## <a name="create-an-application"></a>アプリケーションの作成
デバイスを Azure IoT に接続する方法は複数あります。 このセクションでは、Azure IoT Central を使用してデバイスを接続する方法について説明します。 IoT Central は、IoT ソリューションにおけるデバイス管理のコストと複雑さを軽減する IoT アプリケーション プラットフォームです。

新しいアプリケーションを作成するには:
1. ブラウザーで [Azure IoT Central](https://apps.azureiotcentral.com/) に移動し、Microsoft の個人用アカウントか、職場または学校アカウントでサインインします。
1. **[ビルド]** に移動し、 **[カスタム アプリ]** を選択します。
   :::image type="content" source="media/iot-develop-create-central-app-with-device/iot-central-build.png" alt-text="IoT Central のスタート ページ":::
1. **[アプリケーション名]** に一意の名前を入力するか、生成された名前を使用します。
1. **[URL]** に、アプリケーションの覚えやすい URL プレフィックスを入力するか、生成された URL プレフィックスを使用します。
1. **[アプリケーション テンプレート]** の設定は *[カスタム アプリケーション]* のままにしてください。 
1. **[価格プラン]** オプションを選択します。 
    - アプリケーションを 7 日間無料で使用するには、 **[Free]\(無料\)** を選択します。 無料のアプリケーションは、有効期限が切れる前に標準価格に変換することができます。
    - 必要に応じて標準価格プランを選択可能です。 標準価格を選択した場合は、表示されるオプションが多くなり、 **[ディレクトリ]** 、 **[Azure サブスクリプション]** 、 **[場所]** を設定する必要があります。 価格については、「[Azure IoT Central の価格](https://azure.microsoft.com/pricing/details/iot-central/)」を参照してください。 
        - "**ディレクトリ**" は、アプリケーションを作成する Azure Active Directory です。 Azure Active Directory には、ユーザー ID、資格情報、およびその他の組織情報が含まれています。 Azure Active Directory を持っていない場合は、Azure サブスクリプションを作成するときに作成されます。
        - **Azure サブスクリプション** を使用すると、Azure サービスのインスタンスを作成できます。 IoT Central では、リソースが自分のサブスクリプション内にプロビジョニングされます。 サブスクリプションがない場合は、[無料](https://aka.ms/createazuresubscription)で作成することができます。 サブスクリプションがない場合は、ドロップダウンから選択することができます。
        - **[場所]** は、アプリケーションの作成先となる [Azure 地域](https://azure.microsoft.com/global-infrastructure/geographies/)です。 最適なパフォーマンスを得るには、ご利用のデバイスに物理的に最も近い場所を選択してください。 いったん場所を選択すると、後でアプリケーションを別の場所に移動することはできません。

    :::image type="content" source="media/iot-develop-create-central-app-with-device/iot-central-pricing.png" alt-text="IoT Central の [新しいアプリケーション] ダイアログ":::
1. **［作成］** を選択します
    
    IoT Central によってアプリケーションが作成された後、アプリケーション ダッシュボードにリダイレクトされます。
    :::image type="content" source="media/iot-develop-create-central-app-with-device/iot-central-created.png" alt-text="IoT Central の [新しいアプリケーション] ダッシュボード":::

## <a name="add-a-device"></a>デバイスを追加する
このセクションでは、IoT Central アプリケーションに新しいデバイスを追加します。 このデバイスはデバイス テンプレートのインスタンスであり、アプリケーションに接続するデバイスを表しています。 

新しいデバイスを作成するには:
1. 左ペインで **[デバイス]** を選択し、 **[+ 新規]** を選択します。
1. **[Device template]\(デバイス テンプレート\)** は *[Unassigned]\(割り当てなし\)* のままにし、 **[Simulate this device?]\(このデバイスをシミュレート\)** は *[No]\(いいえ\)* に設定します。

1. わかりやすい **デバイス名** と **デバイス ID** を選択します。 必要に応じて、生成された値を使用します。
    :::image type="content" source="media/iot-develop-create-central-app-with-device/iot-central-create-device.png" alt-text="IoT Central の新しいデバイス ダイアログ":::

1. **［作成］** を選択します

    作成したデバイスが **[すべてのデバイス]** 一覧に表示されます。
    :::image type="content" source="media/iot-develop-create-central-app-with-device/iot-central-devices-list.png" alt-text="IoT Central の [すべてのデバイス] 一覧":::
    
新しいデバイスの接続の詳細を取得するには:
1. **[すべてのデバイス]** 一覧で、デバイス名のリンクをクリックすると詳細が表示されます。 
1. 上部のメニューで、**[接続]** を選択します。

    **[デバイス接続]** ダイアログに、接続の詳細が表示されます。:::image type="content" source="media/iot-develop-create-central-app-with-device/iot-central-device-connect.png" alt-text="IoT Central デバイスの接続の詳細":::
1. **[デバイス接続]** ダイアログから以下の値をコピーして、安全な場所に保存します。 これらの値は、デバイスを IoT Central に接続する際に使用します。
    * `ID scope`
    * `Device ID`
    * `Primary key`