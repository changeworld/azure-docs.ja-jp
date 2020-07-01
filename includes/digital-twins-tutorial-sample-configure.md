---
author: baanders
description: Azure Digital Twins チュートリアルのインクルード ファイル - サンプル プロジェクトを構成する
ms.service: digital-twins
ms.topic: include
ms.date: 5/25/2020
ms.author: baanders
ms.openlocfilehash: 3a7c9f61af0124c656f98b64ba9295bc659c572a
ms.sourcegitcommit: 635114a0f07a2de310b34720856dd074aaf4f9cd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/23/2020
ms.locfileid: "85262349"
---
## <a name="configure-the-sample-project"></a>サンプル プロジェクトを構成する

次に、Azure Digital Twins インスタンスとやり取りするサンプル クライアント アプリケーションを設定します。 サンプル プロジェクトをまだダウンロードしていない場合は、[Azure Digital Twins サンプル](https://docs.microsoft.com/samples/azure-samples/digital-twins-samples/digital-twins-samples)のランディング ページで、タイトルの下にある *[Download ZIP]\(ZIP をダウンロード\)* ボタンをクリックして入手してください。

お使いのマシン上のダウンロードしたファイルに移動し、ファイルを解凍します。

解凍したフォルダー内の _AdtSampleApp/_ に移動します。 Visual Studio 2019 で _**AdtE2ESample.sln**_ を開きます。 

Visual Studio の *[ソリューション エクスプローラー]* ペインを使用して、 _[SampleClientApp] > **[serviceConfig.json.TEMPLATE]**_ ファイルのコピーを作成します (右クリック メニューを使用してコピーし、貼り付けることができます)。 コピーの名前を *serviceConfig.json* に変更します。 このファイルは、プロジェクトを実行するために必要な構成変数を含んだ事前設定済みの JSON ファイルとして機能します。

*serviceConfig.json* ファイルを選択して、編集ウィンドウで開きます。 `tenantId` は実際の "*ディレクトリ ID*" に、`clientId` は実際の "*アプリケーション ID*" に、`instanceUrl` は実際の Azure Digital Twins インスタンスの *hostName* URL (以下に示したように先頭に *https://* を付ける) に変更してください。

```json
{
  "tenantId": "<your-directory-ID>",
  "clientId": "<your-application-ID>",
  "instanceUrl": "https://<your-Azure-Digital-Twins-instance-hostName>"
}
```

ファイルを保存して閉じます。 

次に、*SampleClientApp* をビルドしたときに *serviceConfig.json* ファイルが出力ディレクトリにコピーされるように構成します。 そのためには、*serviceConfig.json* ファイルを右クリックし、 *[プロパティ]* を選択します。 *[プロパティ]* インスペクターで、 *[出力ディレクトリにコピー]* プロパティの値を *[新しい場合はコピーする]* に変更してください。

:::image type="content" source="../articles/digital-twins/media/include-tutorial/copy-config.png" alt-text="Visual Studio ウィンドウの [ソリューション エクスプローラー] ペイン (serviceConfig.json が強調表示されている) とプロパティ ペイン ([出力ディレクトリにコピー] プロパティが [新しい場合はコピーする] に設定されている)" border="false":::

_**AdtE2ESample**_ プロジェクトは、Visual Studio で開いたままにしておいてください。チュートリアルで引き続き使用します。

