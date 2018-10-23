---
title: Azure Digital Twins をデプロイする | Microsoft Docs
description: このチュートリアルの手順を使用して、Azure Digital Twins のインスタンスをデプロイし、空間リソースを構成する方法について説明します。
services: digital-twins
author: dsk-2015
ms.service: digital-twins
ms.topic: tutorial
ms.date: 10/15/2018
ms.author: dkshir
ms.openlocfilehash: 9ca8a9de9a286d4b14dae4a822f3e9baf4747c60
ms.sourcegitcommit: 6361a3d20ac1b902d22119b640909c3a002185b3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/17/2018
ms.locfileid: "49363335"
---
# <a name="tutorial-deploy-azure-digital-twins-and-configure-a-spatial-graph"></a>チュートリアル: Azure Digital Twins をデプロイし、空間グラフを構成する

Azure Digital Twins サービスでは、人、場所、デバイスを一貫した空間システムにまとめることができます。 この一連のチュートリアルでは、Azure Digital Twins を使用して、温度と空気の質が最適な状態の部屋の使用を検出する方法を示します。 これらのチュートリアルでは、.NET コンソール アプリケーションを使用して、複数のフロアがあり各フロアに部屋があるオフィス ビルのシナリオを構築する手順を説明します。 それぞれの部屋には、モーション、周囲温度、空気の質を検出するセンサーを備えたデバイスがあります。 Digital Twins サービスを使用して、建物内の物理的なエリアとエンティティをデジタル オブジェクトとして複製する方法を学習します。 別のコンソール アプリケーションを使用して、デバイス イベントをシミュレートします。 次に、これらの物理的なエリアとエンティティで発生するイベントをほぼリアルタイムで監視する方法を学習します。 オフィス管理者は、この情報を使用して、この建物内で働く従業員が最適な条件の会議室を予約するのを支援することができます。 オフィス施設管理者は、お客様の設定を使用して、部屋の使用傾向を調べたり、メンテナンスのために作業環境を監視したりすることができます。

このシリーズの最初のチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * Digital Twins をデプロイする
> * アプリにアクセス許可を付与する
> * Digital Twins サンプル アプリを変更する
> * 建物をプロビジョニングする


これらのチュートリアルで使用および変更するサンプルは、概念についてさらに詳しく説明されている、[使用可能な部屋を見つけるためのクイック スタート](quickstart-view-occupancy-dotnet.md)のサンプルと同じです。


## <a name="prerequisites"></a>前提条件

- Azure サブスクリプション。 Azure アカウントがない場合は、[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成します。

- これらのチュートリアルで使用される Digital Twins サンプルは、C# で記述されています。 サンプルをビルドして実行する開発マシンには、必ず [.NET Core SDK バージョン 2.1.403 以上](https://www.microsoft.com/net/download)をインストールしておいてください。 コマンド ウィンドウで `dotnet --version` を実行して、適切なバージョンがマシンにインストールされているかどうかを確認してください。

- [Visual Studio Code](https://code.visualstudio.com/)。サンプル コードを確認するために使用します。 

<a id="deploy" />

## <a name="deploy-digital-twins"></a>Digital Twins をデプロイする

このセクションの手順を使用して、Digital Twins サービスの新しいインスタンスを作成します。 サブスクリプションごとに作成できるインスタンスは 1 つのみです。既に実行中のインスタンスがある場合は、次のセクションまでスキップしてください。 

[!INCLUDE [create-digital-twins-portal](../../includes/create-digital-twins-portal.md)]


<a id="permissions" />

## <a name="grant-permissions-to-your-app"></a>アプリにアクセス許可を付与する

Digital Twins では、[Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) を使用してサービスに対する[読み取り/書き込みアクセス](../active-directory/develop/v1-permissions-and-consent.md)を制御します。 お客様の Digital Twins インスタンスに接続する必要のあるアプリケーションは、Azure Active Directory に登録しなければなりません。 このセクションの手順では、サンプル アプリを登録する方法を示します。

既に "*アプリの登録*" がある場合は、それをサンプルに再利用することができます。 ただし、このセクションを参照して、アプリの登録が適切に構成されていることを確認してください。

[!INCLUDE [digital-twins-permissions](../../includes/digital-twins-permissions.md)]


## <a name="configure-digital-twins-sample"></a>Digital Twins サンプルを構成する

このセクションでは、[Digital Twins REST API](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index) と通信する Digital Twins アプリケーションについて説明します。 

### <a name="download-the-sample"></a>サンプルのダウンロード
[使用可能な部屋を見つけるためのクイック スタート](quickstart-view-occupancy-dotnet.md)用に既にサンプルをダウンロードしている場合は、これらの手順をスキップできます。

1. [Digital Twins .NET サンプル](https://github.com/Azure-Samples/digital-twins-samples-csharp/archive/master.zip)をダウンロードします。 
2. お客様のマシン上に ZIP フォルダーの内容を展開します。 

### <a name="explore-the-sample"></a>サンプルの確認
Visual Studio Code で、展開されたサンプル フォルダー内の **_digital-twins-samples-csharp\digital-twins-samples.code-workspace_** ファイルを開きます。 ここには次の 2 つのプロジェクトが含まれています。 

1. プロビジョニング サンプル **_occupancy-quickstart_** を使用すると、物理空間とそこに含まれるリソースのデジタル化画像である[空間インテリジェンス グラフ](concepts-objectmodel-spatialgraph.md#graph)を構成およびプロビジョニングできます。 これには、スマート ビルのためのオブジェクトを定義する[オブジェクト モデル](concepts-objectmodel-spatialgraph.md#model)が使用されています。 Digital Twins オブジェクトと REST API の完全な一覧については、[この REST API ドキュメント](https://docs.westcentralus.azuresmartspaces.net/management/swagger)または[お客様のインスタンス](#deploy)用に作成された **Management API** URL を参照してください。

   サンプルを確認し、サンプルと Digital Twins インスタンスの通信のしくみを見るには、**_src\actions_** フォルダーから開始できます。 このフォルダー内のファイルによって、これらのチュートリアルで使用するコマンドが実装されます。
    - *provisionSample.cs* ファイルでは、空間グラフのプロビジョニング方法が示されます。
    - *getSpaces.cs* ファイルでは、プロビジョニングされた空間に関する情報が取得されます。
    - *getAvailableAndFreshSpaces.cs* では、ユーザー定義関数と呼ばれるカスタム関数の結果が取得されます。
    - *createEndpoints.cs* では、他のサービスと対話するためのエンドポイントが作成されます。

1. **_device-connectivity_** シミュレーション サンプルを使用すると、センサー データがシミュレートされ、お客様の Digital Twins インスタンス用にプロビジョニングされた IoT ハブにそのデータが送信されます。 このサンプルは、[空間グラフをプロビジョニングした後の次のチュートリアル](tutorial-facilities-udf.md#simulate)で使用します。 このサンプルを構成するために使用されるセンサーとデバイスの識別子は、グラフのプロビジョニングに使用するものと同じである必要があります。

### <a name="configure-the-provisioning-sample"></a>プロビジョニング サンプルの構成
1. コマンド ウィンドウを開き、ダウンロードしたサンプルに移動します。 次のコマンドを実行します。

    ```cmd/sh
    cd occupancy-quickstart/src
    ```

1. 次のコマンドを実行して、依存関係をサンプル プロジェクトに復元します。

    ```cmd/sh
    dotnet restore
    ```

1. Visual Studio Code で **occupancy-quickstart** プロジェクトに属する **_appSettings.json_** ファイルを開き、次の値を更新します。
    1. *ClientId*: [アプリのアクセス許可の設定](#permissions)に関するセクションでメモした、AAD アプリの登録の**アプリケーション ID** を入力します。
    1. *Tenant*: [AAD テナント](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant)の**ディレクトリ ID** を入力します。これも、[アプリのアクセス許可の設定](#permissions)に関するセクションでメモしたものです。
    1. *BaseUrl*: Digital Twins インスタンスの URL を入力します。 この URL を取得するには、URL **https://yourDigitalTwinsName.yourLocation.azuresmartspaces.net/management/api/v1.0/** 内のプレースホルダーをお客様のインスタンスの値に置き換えます。 また、[デプロイに関するセクション](#deploy)に示されている **Management API** URL を変更して **swagger/** を **api/v1.0/** に置き換えることによってもこの URL を取得できます。

1. 次のコマンドを実行して、サンプルを使用して確認できる Digital Twins の機能の一覧を表示します。

    ```cmd/sh
    dotnet run
    ```

<a id="provision-spaces" />

## <a name="understand-provisioning-process"></a>プロビジョニング プロセスを理解する
このセクションでは、サンプルで建物の空間グラフがどのようにプロビジョニングされるかを説明します。 

Visual Studio Code で、**_occupancy-quickstart\src\actions_** フォルダーに移動し、*provisionSample.cs* ファイルを開きます。 次の関数に注目してください。

```csharp
public static async Task<IEnumerable<ProvisionResults.Space>> ProvisionSample(HttpClient httpClient, ILogger logger)
{
    IEnumerable<SpaceDescription> spaceCreateDescriptions;
    using (var r = new StreamReader("actions/provisionSample.yaml"))
    {
        spaceCreateDescriptions = await GetProvisionSampleTopology(r);
    }

    var results = await CreateSpaces(httpClient, logger, spaceCreateDescriptions, Guid.Empty);

    Console.WriteLine($"Completed Provisioning: {JsonConvert.SerializeObject(results, Formatting.Indented)}");

    return results;
}

```

この関数では、同じフォルダー内の *provisionSample.yaml* が使用されています。 このファイルを開き、オフィス ビルの階層が *Venue*、*Floor*、*Area*、*Rooms* となっている点に注目してください。 これらの物理空間のいずれにも、"*デバイス*" と "*センサー*" を含めることができます。 各エントリには、*Floor*、*Room* など、あらかじめ定義された `type` があります。 

サンプルの *yaml* ファイルでは、`Default` Digital Twins オブジェクト モデルを使用して空間グラフが表示されます。 このモデルでは、ほとんどの型 (SensorDataType の Temperature、SpaceBlobType の Map など) および空間型 (たとえば、FocusRoom、ConferenceRoom などのサブタイプを備えた Room) に汎用的な名前が与えられています。建物にはこれで十分です。 異なるタイプの現場 (工場など) の空間グラフを作成する必要がある場合は、別のオブジェクト モデルが必要になることがあります。 プロビジョニング サンプルに対してコマンド ラインでコマンド `dotnet run GetOntologies` を実行すると、使用可能なモデルを見つけることができます。 空間グラフとオブジェクト モデルの詳細については、「[Understanding Digital Twins object model and spatial intelligence graph (Digital Twins オブジェクト モデルと空間インテリジェンス グラフを理解する)](concepts-objectmodel-spatialgraph.md)」を参照してください。 

### <a name="modify-sample-spatial-graph"></a>サンプル空間グラフの変更
*provisionSample.yaml* には、次のノードが含まれています。

- **resources**: `resources` ノードでは、お客様の設定内のデバイスと通信するための IoT ハブ リソースが作成されます。 グラフのルート ノードにある IoT ハブは、グラフ内のすべてのデバイスおよびセンサーと通信できます。  

- **spaces**: Digital Twins オブジェクト モデルでは、`spaces` は物理的な場所を表します。 それぞれの空間には、`Type` (たとえば、*Region*、*Venue*、*Customer*) とわかりやすい `Name` があります。 空間は他の空間に属することができ、これによって階層構造が作られます。 *provisionSample.yaml* には、架空の建物の空間グラフが含まれています。 入れ子になった論理的な空間、つまり `Venue` 内の型 `Floor`、フロア内の `Area`、エリア内の `Room` ノードに注目してください。 

- **devices**: 空間には `devices` を含めることができます。これは、複数のセンサーを管理する物理エンティティまたは仮想エンティティです。 たとえば、ユーザーの電話機、Raspberry Pi センサー ポッド、ゲートウェイなどがデバイスに該当します。サンプルにある架空の建物の *Focus Room* という名前の部屋に *Raspberry Pi 3 A1* デバイスがどのように含まれているかに注目してください。 それぞれのデバイス ノードは一意の `hardwareId` で識別されます。これは、サンプルではハードコーディングされています。 このサンプルを実際の運用環境用に構成する場合は、これらの値をお客様の設定の値に置き換えてください。  

- **sensors**: デバイスは、温度、モーション、バッテリー レベルなどの物理的な変化を検出して記録することができる複数の `sensors` を含むことができます。それぞれのセンサー ノードは `hardwareId` で一意に識別されます。これは、ここではハードコーディングされています。 実際のアプリケーションではこれらを、お客様の設定に含まれているセンサーの一意の識別子に置き換えてください。 *provisionSample.yaml* ファイルには、*Motion* と *CarbonDioxide* を記録する 2 つのセンサーがあります。 CarbonDioxide センサーの行の下に次の行を追加して、*Temperature* を記録する別のセンサーを追加します。 これらは、*provisionSample.yaml* 内でコメントアウトされた行として提供されていることに注意してください。各行の先頭にある "#" 文字を削除するだけでコメント解除することができます。 

    ```yaml
            - dataType: Temperature
              hardwareId: SAMPLE_SENSOR_TEMPERATURE
    ```
    > [!NOTE]
    > `dataType` キーと `hardwareId` キーの位置がこのスニペットの上のステートメントに揃っていることを確認します。 また、エディターで空白がタブに置き換えられないことを確認します。 

*provisionSample.yaml* ファイルを保存して閉じます。 次のチュートリアルでは、このファイルに情報をさらに追加したうえで、Azure Digital Twins のサンプルの建物をプロビジョニングします。


## <a name="clean-up-resources"></a>リソースのクリーンアップ

これ以降 Azure Digital Twins の探索を中止する場合は、このチュートリアルで作成されたリソースを削除してかまいません。

1. [Azure portal](http://portal.azure.com) の左側のメニューの **[すべてのリソース]** をクリックし、目的の Digital Twins リソース グループを選択して**削除**します。
2. 必要に応じて、作業マシン上のサンプル アプリケーションも続けて削除することができます。 


## <a name="next-steps"></a>次の手順

シリーズの次のチュートリアルに進んで、サンプルの建物の状態を監視するためのカスタム ロジックを実装する方法について学習します。 
> [!div class="nextstepaction"]
> [チュートリアル: 建物をプロビジョニングし、作業環境を監視する](tutorial-facilities-udf.md)

