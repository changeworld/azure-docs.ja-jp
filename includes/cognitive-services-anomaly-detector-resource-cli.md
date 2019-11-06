---
author: aahill
ms.author: aahi
ms.service: cognitive-services
ms.topic: include
ms.date: 10/08/2019
ms.openlocfilehash: a7ae6cb1231e4c202dfd0a39602c03b33099d088
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/17/2019
ms.locfileid: "72554693"
---
次のいずれかの Azure リソースを作成して、Anomaly Detector サービスの使用を開始します。

* [試用版リソース](https://azure.microsoft.com/try/cognitive-services/#decision) (Azure サブスクリプションは不要): 
    * 7 日間有効です (無料)。 試用版のキーとエンドポイントは、サインアップ後に [Azure Web サイト](https://azure.microsoft.com/try/cognitive-services/my-apis/)で入手できます。 
    * Anomaly Detector を試してみたいものの Azure サブスクリプションをお持ちでない方に最適な方法です。

* [Anomaly Detector リソース](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector):
    * ご自身でリソースを削除するまでは [Azure portal](https://ms.portal.azure.com#blade/HubsExtension/BrowseResourceGroupBlade) からご利用いただけます。
    * Free 価格レベルを使ってサービスを試用し、後から運用環境用の有料レベルにアップグレードします。

### <a name="create-an-environment-variable"></a>環境変数を作成する

>[!NOTE]
> 2019 年 7 月 1 日より後に作成された非試用版リソースのエンドポイントでは、次に示すカスタム サブドメイン形式を使用します。 リージョンのエンドポイントの詳細および全一覧については、「[Cognitive Services のカスタム サブドメイン名](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-custom-subdomains)」を参照してください。 

作成したリソースのキーとエンドポイントを使用して、認証用に 2 つの環境変数を作成します。

* `ANOMALY_DETECTOR_KEY` - 要求を認証するためのリソース キー。
* `ANOMALY_DETECTOR_ENDPOINT` - API 要求を送信するためのリソース エンドポイント。 以下のようになります。 
  * `https://<your-custom-subdomain>.api.cognitive.microsoft.com` 

ご利用のオペレーティング システムの手順に従ってください。

#### <a name="windowstabwindows"></a>[Windows](#tab/windows)

```console
setx ANOMALY_DETECTOR_KEY your-anomaly-detector-key
setx ANOMALY_DETECTOR_ENDPOINT your-anomaly-detector-endpoint
```

環境変数を追加したら、コンソール ウィンドウを再起動します。

#### <a name="linuxtablinux"></a>[Linux](#tab/linux)

```bash
export ANOMALY_DETECTOR_KEY=your-anomaly-detector-key
export ANOMALY_DETECTOR_ENDPOINT=your-anomaly-detector-endpoint
```

環境変数を追加した後、変更を有効にするには、コンソール ウィンドウから `source ~/.bashrc` を実行します。

#### <a name="macostabunix"></a>[macOS](#tab/unix)

次のように `.bash_profile` を編集し、環境変数を追加します。

```bash
export ANOMALY_DETECTOR_KEY=your-anomaly-detector-key
export ANOMALY_DETECTOR_ENDPOINT=your-anomaly-detector-endpoint
```

環境変数を追加した後、変更を有効にするには、コンソール ウィンドウから `source .bash_profile` を実行します。

***