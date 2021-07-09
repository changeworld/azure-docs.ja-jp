---
author: baanders
description: Azure Digital Twins チュートリアルのインクルード ファイル - サンプル プロジェクトを構成する
ms.service: digital-twins
ms.topic: include
ms.date: 5/25/2020
ms.author: baanders
ms.openlocfilehash: 60d65e530c9d98ac86f8d958072d2f26a68836fe
ms.sourcegitcommit: 7f59e3b79a12395d37d569c250285a15df7a1077
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/02/2021
ms.locfileid: "111350946"
---
## <a name="configure-the-sample-project"></a>サンプル プロジェクトを構成する

次に、Azure Digital Twins インスタンスとやり取りするサンプル クライアント アプリケーションを設定します。

マシン上で、以前に [Azure Digital Twins のエンドツーエンド サンプル](/samples/azure-samples/digital-twins-samples/digital-twins-samples)からダウンロードしたファイルに移動します (まだの場合は解凍します)。

フォルダー内の _AdtSampleApp_ に移動します。 Visual Studio 2019 で _**AdtE2ESample.sln**_ を開きます。 

Visual Studio で _[SampleClientApp] > **[appsettings.json]**_ ファイルを選択し、編集ウィンドウで開きます。 このファイルは、プロジェクトを実行するために必要な構成変数を含んだ事前設定済みの JSON ファイルとして機能します。

ファイルの本体で、`instanceUrl` を Azure Digital Twins インスタンスの "*ホスト名 URL*" に変更します (以下に示すように、"*ホスト名*" の前に **_https://_** を追加します)。

```json
{
  "instanceUrl": "https://<your-Azure-Digital-Twins-instance-host-name>"
}
```

ファイルを保存して閉じます。 

次に、*SampleClientApp* をビルドしたときに *appsettings.json* ファイルが出力ディレクトリにコピーされるように構成します。 そのためには、*appsettings.json* ファイルを右クリックし、 **[プロパティ]** を選択します。 **[プロパティ]** インスペクターで、 *[出力ディレクトリにコピー]* プロパティを探します。 まだ設定されてない場合は、その値を **[新しい場合はコピーする]** に変更します。

:::image type="content" source="../articles/digital-twins/media/includes/copy-config.png" alt-text="Visual Studio の Solution Explorer の appsettings.json で、[プロパティ] の [出力ディレクトリにコピー] プロパティが強調表示されているスクリーンショット。" border="false" lightbox="../articles/digital-twins/media/includes/copy-config.png":::

_**AdtE2ESample**_ プロジェクトは、Visual Studio で開いたままにしておいてください。チュートリアルで引き続き使用します。

[!INCLUDE [Azure Digital Twins: local credentials prereq (outer)](digital-twins-local-credentials-outer.md)]
