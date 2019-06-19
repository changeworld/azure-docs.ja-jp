---
title: デバイスでのブロック blob の格納 -Azure IoT Edge | Microsoft Docs
description: 階層化機能と Time-To-Live 機能を理解してから、サポートされている Blob Storage の操作を確認し、その後、ご自身の Blob Storage アカウントに接続します。
author: kgremban
manager: philmea
ms.author: kgremban
ms.reviewer: arduppal
ms.date: 05/21/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 396af2dfd9fc53c080163a27e376328c1369d5e1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "65991466"
---
# <a name="store-data-at-the-edge-with-azure-blob-storage-on-iot-edge-preview"></a>IoT Edge 上の Azure Blob Storage を使用してエッジにデータを格納する (プレビュー)

IoT Edge の Azure Blob Storage では、エッジで[ブロック BLOB](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-block-blobs) ストレージのソリューションが提供されます。 ご利用の IoT Edge デバイス上の BLOB ストレージ モジュールは Azure のブロック BLOB サービスのように動作しますが、そのブロック BLOB はご利用の IoT Edge デバイス上でローカルに格納されます。 同じ Azure Storage SDK メソッドまたは既に慣れているブロック BLOB API 呼び出しを使用して、ご自分の BLOB にアクセスできます。

> [!NOTE]
> IoT Edge の Azure Blob Storage は、[パブリック プレビュー](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)中です。

このモジュールには、**階層化**機能と **Time-to-Live** 機能が付属しています。

> [!NOTE]
> 現時点では、階層化機能と Time-to-Live 機能は、Linux AMD64 Linux ARM32 でのみ使用できます。

**階層化**は構成可能な機能で、断続的なインターネット接続のサポートによりローカルの Blob Storage から Azure にデータを自動的にアップロードすることができます。 これにより次の操作を行うことができます。

- ティアリング機能のオン/オフを切り替える
- データを Azure にコピーする順序 (NewestFirst や OldestFirst など) を選択する
- データのアップロード先の Azure Storage アカウントを選択する。
- Azure にアップロードするコンテナーを指定する。 このモジュールでは、ソースとターゲットの両方のコンテナー名を指定できます。
- 完全な BLOB 階層化 (`Put Blob` 操作を使用) とブロック レベルの階層化 (`Put Block` および `Put Block List` 操作を使用) を行う。

このモジュールは、BLOB がブロックで構成されている場合、ブロック レベルの階層化を使用します。 一般的なシナリオのいくつかを次に示します。

- アプリケーションが以前にアップロードした BLOB のいくつかのブロックを更新すると、このモジュールでは、BLOB 全体ではなく更新されたブロックのみがアップロードされます。
- BLOB のアップロード中にインターネット接続がなくなると、接続が戻ったときに、BLOB 全体ではなく残りのブロックのみアップロードされます。

BLOB のアップロード中に予期しないプロセスの終了 (電源障害など) が発生すると、モジュールがオンラインに戻ったときに、アップロード予定だったすべてのブロックが再度アップロードされます。

**Time to Live** (TTL) は構成可能な機能です。この機能では、指定した期限 (分単位) が過ぎると、モジュールによって BLOB がローカル ストレージから自動的に削除されます。 TTL では次の操作を行うことができます。

- ティアリング機能のオン/オフを切り替える
- TTL を分単位で指定する

ビデオ、画像、財務データ、病院データなど、ローカルに格納する必要があるデータで、後でローカルで処理またはクラウドに転送できるシナリオは、このモジュールを使用するよい例です。

この記事では、ご利用の IoT Edge デバイス上で Blob service を実行する IoT Edge コンテナーに Azure Blob Storage をデプロイするための手順を示します。

> [!NOTE]
> ビデオで使用されている "自動階層化" および "自動期限終了" という用語は、"階層化" および "Time-to-Live" に置き換えられています。

概要紹介のビデオを見る
> [!VIDEO https://www.youtube.com/embed/wkprcfVidyM]

## <a name="prerequisites"></a>前提条件

Azure IoT Edge デバイス:

- [Linux デバイス](quickstart-linux.md) または [Windows デバイス](quickstart.md)のクイック スタートに記載された手順に従って、開発マシンまたは仮想マシンを IoT Edge デバイスとして使用できます。

- IoT Edge モジュールの Azure Blob Storage では、次のデバイスの構成がサポートされます。

  | オペレーティング システム | アーキテクチャ |
  | ---------------- | ----- | ----- |
  | Ubuntu Server 16.04 | AMD64 |
  | Ubuntu Server 18.04 | AMD64 |
  | Windows 10 IoT Core (10 月の更新プログラム) | AMD64 |
  | Windows 10 IoT Enterprise (10 月の更新プログラム) | AMD64 |
  | Windows Server 2019 | AMD64 |
  | Raspbian-stretch | ARM32 |

クラウド リソース:

Azure の Standard レベルの [IoT Hub](../iot-hub/iot-hub-create-through-portal.md)。

## <a name="tiering-and-time-to-live-properties"></a>階層化および Time-to-Live のプロパティ

必要なプロパティを使用して、階層化と Time-to-Live のプロパティを設定します。 これらは、デプロイ中に設定したり、モジュール ツインを編集することで、再デプロイすることなく後で変更したりできます。 値が確実に正しく反映されるよう、`reported configuration` と `configurationValidation` の "モジュール ツイン" をチェックすることをお勧めします。

### <a name="tiering-properties"></a>階層化のプロパティ

この設定の名前は `tieringSettings` です

| フィールド | 指定できる値 | 説明 |
| ----- | ----- | ---- |
| tieringOn | true、false | 既定では `false` に設定されていて、有効にする場合は `true` に設定します|
| backlogPolicy | NewestFirst、OldestFirst | データを Azure にコピーする順序を選択できます。 既定では `OldestFirst` に設定されています。 順序は Blob の最終更新時刻によって決定されます |
| remoteStorageConnectionString |  | `"DefaultEndpointsProtocol=https;AccountName=<your Azure Storage Account Name>;AccountKey=<your Azure Storage Account Key>;EndpointSuffix=<your end point suffix>"` は、データのアップロード先の Azure Storage アカウントを選択するための接続文字列です。 `Azure Storage Account Name`、`Azure Storage Account Key`、`End point suffix` を指定します。 データのアップロード先の Azure の適切な EndpointSuffix を追加します。これはグローバル Azure、政府機関向け Azure、および Microsoft Azure Stack ごとに異なります。 |
| tieredContainers | `"<source container name1>": {"target": "<target container name>"}`,<br><br> `"<source container name1>": {"target": "%h-%d-%m-%c"}`, <br><br> `"<source container name1>": {"target": "%d-%c"}` | Azure にアップロードするコンテナーの名前を指定できます。 このモジュールでは、ソースとターゲットの両方のコンテナー名を指定できます。 ターゲット コンテナー名を指定しない場合、コンテナー名は自動的に `<IoTHubName>-<IotEdgeDeviceName>-<ModuleName>-<ContainerName>` として割り当てられます。 ターゲット コンテナー名のテンプレート文字列を作成して、使用可能な値の列をチェックアウトできます。 <br>* %h -> IoT Hub 名 (3 ～ 50 文字)。 <br>* %d -> IoT デバイス ID (1 ～ 129 文字)。 <br>* %m -> モジュール名 (1 ～ 64 文字)。 <br>* %c -> ソース コンテナー名 (3 ～ 63 文字)。 <br><br>コンテナー名の最大サイズは 63 文字です。ターゲット コンテナー名を自動的に割り当てる場合は、コンテナーのサイズが 63 文字を超えると、各セクション (IoTHubName、IotEdgeDeviceName、ModuleName、ContainerName) が 15 文字まで削除されます。 |

### <a name="time-to-live-properties"></a>Time-to-Live のプロパティ

この設定の名前は `ttlSettings` です

| フィールド | 指定できる値 | 説明 |
| ----- | ----- | ---- |
| ttlOn | true、false | 既定では `false` に設定されていて、有効にする場合は `true` に設定します|
| timeToLiveInMinutes | `<minutes>` | TTL を分単位で指定します。 TTL が期限切れになると、モジュールが自動的にローカル ストレージから BLOB を削除します |

## <a name="configure-log-files"></a>ログ ファイルを構成する

お使いのモジュールのログ ファイルの構成については、こちらの[運用環境のベスト プラクティス](https://docs.microsoft.com/azure/iot-edge/production-checklist#set-up-logs-and-diagnostics)をご覧ください。

## <a name="connect-to-your-blob-storage-module"></a>ご自分の BLOB ストレージ モジュールに接続する

ご自分のモジュールに対して構成したアカウント名とアカウントキーを使用して、ご利用の IoT Edge デバイス上の BLOB ストレージにアクセスできます。

作成する任意のストレージ要求に対する BLOB エンドポイントとして、ご利用の IoT Edge デバイスを指定します。 構成した IoT Edge デバイス情報とアカウント名を使用して、[明示的なストレージ エンドポイントへの接続文字列を作成](../storage/common/storage-configure-connection-string.md#create-a-connection-string-for-an-explicit-storage-endpoint)できます。

- Azure Blob Storage on IoT Edge モジュールが実行されているデバイスにデプロイされているモジュールの場合、BLOB エンドポイントは `http://<module name>:11002/<account name>` になります。
- Azure Blob Storage on IoT Edge モジュールが実行されているデバイスとは別のデバイスにデプロイされているモジュールの場合、BLOB エンドポイントは、設定に応じて次のいずれかになります。
  - `http://<device IP >:11002/<account name>`
  - `http://<IoT Edge device hostname>:11002/<account name>`
  - `http://<fully qualified domain name>:11002/<account name>`

## <a name="azure-blob-storage-quickstart-samples"></a>Azure Blob Storage のクイックスタートのサンプル

Azure Blob Storage ドキュメントには、複数の言語でサンプル コードを提供するクイック スタートが含まれます。 ご利用の Blob Storage モジュールに接続するように BLOB エンドポイントを変更して、これらのサンプルを実行し、IoT Edge の Azure Blob Storage をテストできます。

次のクイック スタートでは IoT Edge からもサポートされる言語を使用するため、BLOB ストレージ モジュールと共に IoT Edge モジュールとして言語をデプロイできます。

- [.NET](../storage/blobs/storage-quickstart-blobs-dotnet.md)
- [Java](../storage/blobs/storage-quickstart-blobs-java.md)
- [Python](../storage/blobs/storage-quickstart-blobs-python.md)
- [Node.js](../storage/blobs/storage-quickstart-blobs-nodejs.md)

## <a name="connect-to-your-local-storage-with-azure-storage-explorer"></a>Azure Storage Explorer を使用してお使いのローカル ストレージに接続する

**Azure Storage Explorer** を使用して、ご自身のローカル ストレージ アカウントに接続できます。 これは、[Azure Storage Explorer バージョン 1.5.0](https://github.com/Microsoft/AzureStorageExplorer/releases/tag/v1.5.0) でのみ使用できます。

> [!NOTE]
> 次の手順 (ローカル ストレージ アカウントに接続を追加したり、ローカル ストレージ アカウントでコンテナーを作成するなど) を実行中にエラーが発生する場合があります。 無視して更新してください。

1. Azure ストレージ エクスプローラーをダウンロードしてインストールします

1. 接続文字列を使用して Azure Storage に接続します

1. 接続文字列 (`DefaultEndpointsProtocol=http;BlobEndpoint=http://<host device name>:11002/<your local account name>;AccountName=<your local account name>;AccountKey=<your local account key>;`) を指定します

1. 接続する手順を実行します。

1. ご自分のローカル ストレージ アカウント内にコンテナーを作成します

1. ファイルをブロック BLOB としてアップロードを開始します。
   > [!NOTE]
   > このモジュールでは、ページ BLOB はサポートされていません。

1. データをアップロードする Azure ストレージ アカウントの接続を選択することができます。 これにより、1 つのビューにローカル ストレージ アカウントと Azure ストレージ アカウントの両方を表示できます。

## <a name="supported-storage-operations"></a>サポートされるストレージ操作

IoT Edge 上の BLOB ストレージ モジュールでは同じ Azure Storage SDK が使用され、ブロック BLOB エンドポイント用の 2017-04-17 バージョンの Azure Storage API と一貫性があります。 今後のリリースは、顧客のニーズに依存します。

すべての Azure Blob Storage の操作が、IoT Edge の Azure Blob Storage でサポートされるわけではないため、このセクションでは、それぞれの状態の一覧を示します。

### <a name="account"></a>Account

サポート対象:

- コンテナーの一覧表示

サポート外:

- Blob service プロパティの取得と設定
- BLOB 要求のプレフライト
- Blob service の統計情報の取得
- アカウント情報の取得

### <a name="containers"></a>Containers

サポート対象:

- コンテナーの作成と削除
- コンテナーのプロパティとメタデータの取得
- BLOB を一覧表示する
- コンテナー ACL の取得と設定
- コンテナー メタデータの設定

サポート外:

- コンテナーのリース

### <a name="blobs"></a>BLOB

サポート対象:

- BLOB の配置、取得、削除
- BLOB プロパティの取得と設定
- BLOB メタデータの取得と設定

サポート外:

- BLOB のリリース
- BLOB のスナップショット
- BLOB のコピーとコピーの中止
- BLOB の削除の取り消し
- BLOB レベルの設定

### <a name="block-blobs"></a>ブロック blob

サポート対象:

- ブロックの配置
- ブロック一覧の配置と取得

サポート外:

- URL からブロックの配置

## <a name="feedback"></a>フィードバック

お客様のフィードバックは、このモジュールとその機能を便利で使いやすいものにするために、Microsoft にとって重要です。 Microsoft では、今後の改良の参考とするために、皆様からのフィードバックをお待ちしています。

absiotfeedback@microsoft.com までお寄せください

## <a name="next-steps"></a>次の手順

[IoT Edge のAzure Blob Storage デプロイ](how-to-deploy-blob.md)の詳細を確認する
