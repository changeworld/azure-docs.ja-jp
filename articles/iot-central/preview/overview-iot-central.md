---
title: Azure IoT Central とは | Microsoft Docs
description: Azure IoT Central は、IoT ソリューションの作成を簡素化し、IoT の管理、運用、開発の負担とコストを削減するのに役立つ IoT アプリケーション プラットフォームです。 この記事では、Azure IoT Central の機能について概要を紹介します。
author: dominicbetts
ms.author: dobett
ms.date: 12/10/2019
ms.topic: overview
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: timlt
ms.openlocfilehash: 52504fb8333b286407b3f2df8f962da59b80ac53
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/25/2019
ms.locfileid: "75434847"
---
# <a name="what-is-azure-iot-central-preview-features"></a>Azure IoT Central (プレビュー機能) とは?

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

> [!WARNING]
> Azure IoT Central の [IoT プラグ アンド プレイ](../../iot-pnp/overview-iot-plug-and-play.md)機能は、現在パブリック プレビューの段階にあります。 運用環境のワークロードに、IoT プラグ アンド プレイ対応の IoT Central [アプリケーション テンプレート](../core/concepts-app-templates.md?toc=/azure/iot-central/preview/toc.json&bc=/azure/iot-central/preview/breadcrumb/toc.json)を使用しないでください。 運用環境向けには、現在一般公開されている[アプリケーション テンプレート](../core/concepts-app-templates.md?toc=/azure/iot-central/preview/toc.json&bc=/azure/iot-central/preview/breadcrumb/toc.json)から作成された IoT Central アプリケーションを使用してください。

IoT Central は、エンタープライズ レベルの IoT ソリューションの開発、管理、および保守の負担とコストを削減する IoT アプリケーション プラットフォームです。 IoT Central を使用して構築することを選択すると、複雑で絶えず進化する IoT インフラストラクチャをただ維持し、更新するだけでなく、IoT データによってビジネスを変革することに時間、予算、エネルギーを集中させる機会が得られます。

Web UI では、デバイスの状態を監視し、ルールを作成し、ライフ サイクル全体を通して何百万ものデバイスとそのデータを管理することができます。 さらに、IoT インテリジェンスを基幹業務アプリケーションまで拡張することで、デバイスの分析情報に従って行動できるようになります。

この記事では、IoT Central に関して以下の概要を説明します。

- プロジェクトに関連する代表的なペルソナ。
- アプリケーションを作成する方法。
- デバイスをアプリケーションに接続する方法。
- アプリケーションを管理する方法。
- IoT Central での Azure IoT Edge 機能。
- Azure IoT Edge ランタイム搭載デバイスをアプリケーションに接続する方法。

## <a name="known-issues"></a>既知の問題

> [!Note]
> これらの既知の問題は、IoT Central プレビューのアプリケーションにのみ該当します。

- 継続的データ エクスポートで Avro 形式がサポートされていない (互換性がない)。
- GeoJSON は現在サポートされていない。
- マップ タイルは現在サポートされていない。
- ジョブでは複合型がサポートされていない。
- 配列スキーマの種類はサポートされていない。
- C デバイス SDK と Node.js のデバイス SDK、サービス SDK のみがサポートされている。
- 米国とヨーロッパの場所でのみ使用可能です。
- デバイス機能モデルには、すべてのインターフェイスが同じファイル内にインラインで定義されている必要があります。

## <a name="personas"></a>ペルソナ

この IoT Central ドキュメントでは、IoT Central アプリケーションと対話する 4 種類のペルソナに言及しています。

- "_ソリューション ビルダー_": アプリケーションに接続するデバイスの種類を定義したり、オペレーター向けにアプリケーションをカスタマイズしたりする役割を担います。
- "_オペレーター_": アプリケーションに接続されたデバイスを管理します。
- "_管理者_": 管理タスクを担います (アプリケーション内での[ユーザー ロールとアクセス許可](howto-administer.md)の管理など)。
- "_デバイス開発者_": アプリケーションに接続されるデバイスまたは IoT Edge モジュールで実行するコードを作成します。

## <a name="create-your-iot-central-application"></a>IoT Central アプリケーションを作成する

クラウドでホストされる自社のカスタム IoT ソリューションは、ソリューション ビルダーが IoT Central を使用して作成します。 通常、カスタム IoT ソリューションは次の要素から成ります。

- クラウドベースのアプリケーション。このアプリケーションによってデバイスからテレメトリを受信し、それらのデバイスの管理が可能となります。
- カスタム コードを実行する複数のデバイス。これらのデバイスが、クラウドベースのアプリケーションに接続されます。

新しい IoT Central アプリケーションは、ブラウザーからすばやくデプロイし、具体的な要件に合わせてカスタマイズすることができます。 ソリューション ビルダーは、自社アプリケーションに接続するデバイス用に、Web ベースのツールを使って "_デバイス テンプレート_" を作成します。 デバイス テンプレートは、特定の種類のデバイスについて、その特性と動作を定義するブループリントです。次に示したのは、その例です。

- 送信するテレメトリ。
- オペレーターによる変更が可能なビジネス プロパティ。
- デバイスによって設定され、かつアプリケーションでは読み取り専用となるデバイス プロパティ。
- オペレーターが設定する、デバイスの動作を決めるプロパティ。

このデバイス テンプレートには、次のものが含まれています。

- "_デバイス機能モデル_": デバイスから送信されるテレメトリやデバイスからレポートされるプロパティなど、デバイスが実装する必要がある機能を記述します
- クラウド プロパティ: これらはデバイスには格納されません。
- カスタマイズ、ダッシュボード、およびフォーム: ご利用の IoT Central アプリケーションの一部を成します。

### <a name="create-device-templates"></a>デバイス テンプレートを作成する

[IoT プラグ アンド プレイ](../../iot-pnp/overview-iot-plug-and-play.md)を使用すれば、埋め込みデバイス コードを記述せずに、IoT Central でデバイスを統合できます。 IoT プラグ アンド プレイの中核となるのは、デバイスの機能を記述するデバイス機能モデル スキーマです。 IoT Central プレビュー アプリケーション内のデバイス テンプレートでは、これらの IoT プラグ アンド プレイ デバイス機能モデルを使用しています。

ソリューション ビルダーには、デバイス テンプレート作成のためのオプションがいくつか用意されています。

- IoT Central でデバイス テンプレートを設計してから、そのデバイス機能モデルをデバイス コードに実装します。
- [Azure Certified for IoT デバイス カタログ](https://aka.ms/iotdevcat)からデバイス機能モデルをインポートし、次に、IoT Central アプリケーションで必要とされるクラウド プロパティ、カスタマイズ、およびダッシュボードを追加します。
- Visual Studio Code を使用してデバイス機能モデルを作成します。 モデルからデバイス コードを実装し、デバイスを IoT Central アプリケーションに接続します。 IoT Central によって、リポジトリからデバイス機能モデルが検索され、シンプルなデバイス テンプレートが自動的に作成されます。
- Visual Studio Code を使用してデバイス機能モデルを作成します。 ご利用のデバイス コードをモデルから実装します。 ご利用の IoT Central アプリケーションにデバイス機能モデルを手動でインポートしてから、IoT Central アプリケーションに必要なクラウド プロパティ、カスタマイズ、およびダッシュボードを追加します。

ソリューション ビルダーは、IoT Central を使用して、デバイス テンプレートを検証するためのテスト デバイス用コードを生成できます。

### <a name="customize-the-ui"></a>UI のカスタマイズ

ソリューション ビルダーは、アプリケーションの使用を日常的に担当するオペレーターに向けて、IoT Central アプリケーションの UI をカスタマイズすることもできます。 ソリューション ビルダーが行うことのできるカスタマイズの例を次に示します。

- プロパティと設定のレイアウトをデバイス テンプレートで定義する。
- オペレーターによる分析情報の検出と問題の解決を迅速化するカスタム ダッシュボードを構成する。
- 接続されているデバイスから時系列データを探すカスタム分析を構成する。

## <a name="connect-your-devices"></a>デバイスの接続

アプリケーションに接続できるデバイスの種類がビルダーによって定義されたら、そのデバイス上で実行するコードをデバイス開発者が作成します。 デバイス開発者は、Microsoft のオープンソース [Azure IoT SDK](https://github.com/Azure/azure-iot-sdks) を使用してデバイス コードを作成します。 IoT Central アプリケーションに自社のデバイスを接続するというお客様のニーズを満たすために、これらの SDK は多様な言語、プラットフォーム、プロトコルに対応しています。 これらの SDK を利用することで、次のようなデバイス機能が容易に実装できます。

- 安全な接続を作成する。
- テレメトリを送信する。
- ステータスを報告する。
- 構成の更新を受信する。

詳細については、ブログ記事「[Benefits of using the Azure IoT SDKs, and pitfalls to avoid if you don't (Azure IoT SDK を使う利点と使わなかった場合に回避すべきリスク)](https://azure.microsoft.com/blog/benefits-of-using-the-azure-iot-sdks-in-your-azure-iot-solution/)」を参照してください。

### <a name="azure-iot-edge-devices"></a>Azure IoT Edge デバイス

[Azure IoT SDK](https://github.com/Azure/azure-iot-sdks) を使用して作成されたデバイスと同様に、[Azure IoT Edge デバイス](../../iot-edge/about-iot-edge.md)を IoT Central アプリケーションに接続することもできます。 Azure IoT Edge を使用すると、IoT Central によって管理されている IoT デバイスで、クラウド インテリジェンスやカスタム ロジックを直接実行できます。 IoT Edge ランタイムによって以下が可能となります。

- デバイスにワークロードをインストールし、更新する。
- デバイス上の Azure IoT Edge のセキュリティ標準を維持する。
- IoT Edge モジュールの実行状態を絶えず確保する。
- モジュールの正常性をクラウドにレポートしてリモート監視を可能にする。
- ダウンストリームのリーフ デバイスと IoT Edge デバイス間、IoT Edge デバイス上のモジュール間、IoT Edge デバイスとクラウド間の通信を管理する。

詳細については、[Azure IoT Edge デバイスと IoT Central](./concepts-architecture.md#azure-iot-edge-devices) に関するページを参照してください。

## <a name="manage-your-application"></a>アプリケーションの管理

IoT Central アプリケーションは、Microsoft によって完全にホストされるため、アプリケーションの管理オーバーヘッドが軽減されます。

IoT Central ソリューションのデバイスは、オペレーターが IoT Central アプリケーションを使用して管理します。 オペレーターは、次のようなタスクを実行します。

- アプリケーションに接続されたデバイスを監視する。
- デバイスの問題をトラブルシューティングして修復する。
- 新しいデバイスをプロビジョニングする。

ソリューション ビルダーは、接続デバイスからのデータ ストリーミングに対して作用するカスタム ルールやカスタム アクションを定義できます。 それらのルールをオペレーターがデバイス レベルで有効または無効にすることで、アプリケーション内のタスクを制御したり自動化したりすることができます。

アプリケーションへのアクセスは、管理者が[ユーザー ロールとアクセス許可](howto-administer.md)を使って管理します。

## <a name="quotas"></a>Quotas (クォータ)

各 Azure サブスクリプションには既定のクォータが設けられており、IoT ソリューションの範囲に影響する可能性があります。 現在、IoT Central では、サブスクリプションでデプロイできるアプリケーションの数が 10 に制限されています。 この制限を引き上げる必要がある場合は、[Microsoft サポート](https://azure.microsoft.com/support/options/)にお問い合わせください。

## <a name="next-steps"></a>次のステップ

これで IoT Central の概要を把握できました。推奨される次の手順は以下のとおりです。

- [IoT ソリューションを作成するための Azure のテクノロジとサービス](../../iot-fundamentals/iot-services-and-technologies.md)を把握する。
- [Azure IoT Central の UI](overview-iot-central-tour.md) に慣れる。
- 実際に [Azure IoT Central アプリケーションを作成](quick-deploy-iot-central.md)してみる。
- [IoT プラグ アンド プレイ](../../iot-pnp/overview-iot-plug-and-play.md)について詳細を学習する
- [Azure IoT Edge デバイス テンプレートを作成する](./tutorial-define-edge-device-type.md)方法について学習する
