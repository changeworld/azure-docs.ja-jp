---
author: ramonarguelles
ms.service: spatial-anchors
ms.topic: include
ms.date: 1/30/2019
ms.author: rgarcia
ms.openlocfilehash: 0e6f68daf22659b880827ad027268d9757c60063
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/13/2020
ms.locfileid: "91971428"
---
### <a name="open-the-publish-wizard"></a>発行ウィザードを開く

**ソリューション エクスプローラー**で、**SharingService** プロジェクトを右クリックし、 **[発行]** を選択します。

発行ウィザードが起動します。 

**[App Service]** 、 **[発行]** の順に選択し、 **[App Service の作成]** ウィンドウを開きます。

### <a name="sign-in-to-azure"></a>Azure へのサインイン

Azure portal にサインインします。

**[App Service の作成]** ウィンドウで、 **[アカウントの追加]** をクリックし、Azure サブスクリプションにサインインします。 既にサインインしている場合、ドロップダウン リストからアカウントを選択します。

   > [!NOTE]
   > 既にサインインしている場合は、まだ **[作成]** を選択しないでください。
   >

### <a name="create-a-resource-group"></a>リソース グループを作成する

[!INCLUDE [resource group intro text](resource-group.md)]

**[リソース グループ]** の横にある **[新規]** をクリックします。

リソース グループに **myResourceGroup** という名前を付けて、 **[OK]** をクリックします。

### <a name="create-an-app-service-plan"></a>App Service プランを作成する

[!INCLUDE [app-service-plan](app-service-plan.md)]

**[ホスティング プラン]** の隣にある **[新規]** を選択します。

**[ホスティング プランの構成]** ウィンドウで、以下の設定を使用します。

| 設定 | 推奨値 | 説明 |
|-|-|-|
|App Service プラン| MySharingServicePlan | App Service プランの名前 |
| 場所 | 米国西部 | Web アプリがホストされているデータセンター |
| サイズ | Free | ホスティング機能を決める[価格レベル](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) |

**[OK]** を選択します。

### <a name="create-and-publish-the-web-app"></a>Web アプリを作成して発行する

**[アプリ名]** に一意のアプリ名を入力します。 有効な文字は a-z、0-9、ダッシュ (-) です。あるいは、自動的に生成された一意の名前をそのまま使用します。 Web アプリの URL は `https://<app_name>.azurewebsites.net` です。`<app_name>` には自分のアプリの名前を指定します。

**[作成]** をクリックして、Azure リソースの作成を開始します。

   ウィザードの完了後に、Azure に ASP.NET Core Web アプリを発行してから、既定のブラウザーでアプリを開きます。

  ![Azure で発行された ASP.NET Web アプリのスクリーンショット。](./media/spatial-anchors-azure/web-app-running-live.png)

このセクションで使用したアプリ名は、形式 `https://<app_name>.azurewebsites.net` の URL プレフィックスとして使用されます。 後で使用するためにこの URL をテキスト エディターにコピーします。
