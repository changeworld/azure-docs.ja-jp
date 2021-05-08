---
title: インクルード ファイル
description: インクルード ファイル
services: iot-central
author: dominicbetts
ms.service: iot-central
ms.topic: include
ms.date: 10/06/2020
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 472c1770e2793d8da4e8fc76fafbf3b9073b746d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96763428"
---
## <a name="deploy-and-configure-azure-media-services"></a>Azure Media Services をデプロイして構成する

このソリューションでは、IoT Edge ゲートウェイ デバイスによって作成された物体検出ビデオ クリップの保存先として、Azure Media Services アカウントを使用します。

Media Services アカウントを作成する際:

- アカウント名、Azure サブスクリプション、場所、リソース グループ、ストレージ アカウントを指定する必要があります。 Media Services アカウントを作成するときに、既定の設定を使用して新しいストレージ アカウントを作成してください。

- 場所には **米国東部** リージョンを選択します。

- Media Services とストレージ アカウント用に *lva-rg* という新しいリソース グループを **米国東部** リージョンに作成します。 チュートリアルを終えたら、*lva-rg* リソース グループを削除することで、すべてのリソースを簡単に削除できます。

[Azure portal で Media Services アカウント](https://portal.azure.com/?r=1#create/Microsoft.MediaService)を作成します。

> [!TIP]
> これらのチュートリアルでは、すべての例で **米国東部** リージョンを使用しています。 必要に応じて最寄りのリージョンを使用してかまいません。

*scratchpad.txt* ファイルに **Media Services** アカウントの名前を書き留めます。

デプロイが完了したら、Cloud Shell を開き、次のコマンドを実行して、対象のメディア サービス アカウントの **リソース ID** を取得します。

```azurecli
az resource list --resource-group lva-rg --resource-type microsoft.media/mediaservices --output table --query "[].{ResourceID:id}"
```

:::image type="content" source="media/iot-central-video-analytics-part2/get-resource-id.png" alt-text="Cloud Shell を使用してリソース ID を取得する":::

**リソース ID** を *scratchpad.txt* ファイルに書き留めます。この値は、後で IoT Edge モジュールを構成する際に使用します。

次に、Media Services リソースに使用する Azure Active Directory サービス プリンシパルを構成します。 **[API アクセス]** を選択し、 **[サービス プリンシパルの認証]** を選択します。 Media Services リソースと同じ名前で新しい Azure Active Directory アプリを作成し、"*IoT Edge アクセス*" という説明でシークレットを作成します。

:::image type="content" source="./media/iot-central-video-analytics-part2/media-service-authentication.png" alt-text="Azure Media Services 用の Azure AD アプリを構成する":::

シークレットが生成されたら、下へスクロールして、 **[サービス プリンシパル アプリケーションを接続するための資格情報のコピー]** セクションに移動します。 次に、 **[JSON]** を選択します。 すべての資格情報は、ここからまとめてコピーできます。 その情報を *scratchpad.txt* ファイルに書き留めておきます。これは後で IoT Edge デバイスを構成する際に使用します。

> [!WARNING]
> これがシークレットを表示して保存する唯一の機会となります。 なくしてしまった場合は、別のキーを生成する必要があります。

## <a name="create-the-azure-iot-central-application"></a>Azure IoT Central アプリケーションを作成する

このセクションでは、テンプレートから新しい Azure IoT Central アプリケーションを作成します。 チュートリアル シリーズ全体でこのアプリケーションを使用して、完全なソリューションを構築します。

新しい Azure IoT Central アプリケーションを作成するには:

1. [Azure IoT Central のアプリケーション マネージャー](https://aka.ms/iotcentral) Web サイトに移動します。

1. Azure サブスクリプションへのアクセスに使用する資格情報でサインインします。

1. 新しい Azure IoT Central アプリケーションの作成を開始するには、 **[ビルド]** ページの **[新しいアプリケーション]** を選択します。

1. **[小売]** を選択します。 小売ページに、いくつかの小売アプリケーション テンプレートが表示されます。

新しいビデオ分析アプリケーションを作成するには:

1. **ビデオ分析 (物体とモーションの検出)** アプリケーション テンプレートを選択します。 このテンプレートには、チュートリアルで使用するデバイス用のデバイス テンプレートが含まれています。 このテンプレートには、カメラの監視や管理などのタスクを実行するためにオペレーターが使用できるサンプル ダッシュボードが含まれています。

1. 必要に応じて、わかりやすい **アプリケーション名** を選びます。 このアプリケーションは、Northwind Traders という架空の小売店に基づいています。 このチュートリアルでは、*Northwind Traders video analytics* という **アプリケーション名** を使用します。

    > [!NOTE]
    > わかりやすい **アプリケーション名** を使用する場合でも、アプリケーションの **URL** には一意の値を使用する必要があります。

1. Azure サブスクリプションをお持ちの場合は、 **[ディレクトリ]** と **[Azure サブスクリプション]** を選択し、 **[場所]** として **[米国]** を選択します。 サブスクリプションをお持ちでない場合は、**7 日間の無料試用版** を有効にし、必須の連絡先情報を入力できます。 このチュートリアルでは、カメラ 2 台と IoT Edge デバイス 1 台の計 3 台のデバイスを使用します。そのため、無料試用版を使用していない場合、使用量に応じて課金されます。

    ディレクトリ、サブスクリプション、場所の詳細については、[アプリケーションの作成のクイックスタート](../articles/iot-central/core/quick-deploy-iot-central.md)に関するページを参照してください。

1. **［作成］** を選択します

    :::image type="content" source="./media/iot-central-video-analytics-part2/new-application.png" alt-text="Azure IoT Central の [Create Application]\(アプリケーションの作成\) ページ":::

### <a name="retrieve-the-configuration-data"></a>構成データを取得する

このチュートリアルの中で、後から IoT Edge ゲートウェイを構成します。その際、IoT Central アプリケーションの構成データがいくつか必要となります。 IoT Edge デバイスがアプリケーションへの登録と接続を行うためには、この情報が必要です。

**[管理]** セクションで **[お客様のアプリケーション]** を選択し、 **[アプリケーション URL]** と **[アプリケーション ID]** を *scratchpad.txt* ファイルに書き留めます。

:::image type="content" source="./media/iot-central-video-analytics-part2/administration.png" alt-text="ビデオ分析ページの [管理] ペインのスクリーンショット。[アプリケーション URL] と [アプリケーション ID] が強調表示されている。":::

**[API トークン]** を選択し、**オペレーター** ロール用に **LVAEdgeToken** という新しいトークンを生成します。

:::image type="content" source="./media/iot-central-video-analytics-part2/token.png" alt-text="トークンの生成":::

このトークンを後で使用できるよう、*scratchpad.txt* ファイルに書き留めます。 ダイアログを閉じた後に、再びトークンを確認することはできません。

**[管理]** セクションで **[デバイス接続]** を選択し、 **[SAS-IoT-Devices]** を選択します。

*scratchpad.txt* ファイルにデバイスの **プライマリ キー** を書き留めます。 この "*プライマリ グループ Shared Access Signature トークン*" は、後で IoT Edge デバイスを構成する際に使用します。
