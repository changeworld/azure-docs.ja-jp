---
author: baanders
description: Azure Digital Twins チュートリアルのインクルード ファイル - サンプル プロジェクトを構成する
ms.service: digital-twins
ms.topic: include
ms.date: 5/25/2020
ms.author: baanders
ms.openlocfilehash: ff44e4ae45f8b7c6bff3685522440ca7118f337e
ms.sourcegitcommit: 03713bf705301e7f567010714beb236e7c8cee6f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/21/2020
ms.locfileid: "92347111"
---
## <a name="configure-the-sample-project"></a>サンプル プロジェクトを構成する

次に、Azure Digital Twins インスタンスとやり取りするサンプル クライアント アプリケーションを設定します。

マシン上で、 [*Azure Digital Twins のエンドツーエンド サンプル*](/samples/azure-samples/digital-twins-samples/digital-twins-samples)からダウンロードしたファイルに移動します (まだの場合は解凍します)。

フォルダー内の _AdtSampleApp_ に移動します。 Visual Studio 2019 で _**AdtE2ESample.sln**_ を開きます。 

Visual Studio で _[SampleClientApp] > **[appsettings.json]**_ ファイルを選択し、編集ウィンドウで開きます。 このファイルは、プロジェクトを実行するために必要な構成変数を含んだ事前設定済みの JSON ファイルとして機能します。

ファイルの本体で、`instanceUrl` を Azure Digital Twins インスタンスの *hostName* URL に変更します (以下に示したように、先頭に *https://* を付けます)。

```json
{
  "instanceUrl": "https://<your-Azure-Digital-Twins-instance-hostName>"
}
```

ファイルを保存して閉じます。 

次に、 *SampleClientApp* をビルドしたときに *appsettings.json* ファイルが出力ディレクトリにコピーされるように構成します。 そのためには、 *appsettings.json* ファイルを右クリックし、 *[プロパティ]* を選択します。 *[プロパティ]* インスペクターで、 *[出力ディレクトリにコピー]* プロパティの値を *[新しい場合はコピーする]* に変更してください。

:::image type="content" source="../articles/digital-twins/media/includes/copy-config.png" alt-text="Visual Studio ウィンドウの [ソリューション エクスプローラー] ペイン (appsettings.json が強調表示されている) とプロパティ ペイン ([出力ディレクトリにコピー] プロパティが [新しい場合はコピーする] に設定されている)" border="false":::

_**AdtE2ESample**_ プロジェクトは、Visual Studio で開いたままにしておいてください。チュートリアルで引き続き使用します。

