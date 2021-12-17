---
title: Azure IoT Central とは | Microsoft Docs
description: Azure IoT Central は、IoT ソリューションの作成を簡素化し、IoT の管理、運用、開発の負担とコストを削減するのに役立つ IoT アプリケーション プラットフォームです。 この記事では、Azure IoT Central の機能について概要を紹介します。
author: dominicbetts
ms.author: dobett
ms.date: 11/05/2021
ms.topic: overview
ms.service: iot-central
services: iot-central
ms.custom: mvc, contperf-fy21q2, contperf-fy22q1, contperf-fy22q2
ms.openlocfilehash: 2362bfae3d06a217b24f2297ea00ac6a7e3732c5
ms.sourcegitcommit: 1a0fe16ad7befc51c6a8dc5ea1fe9987f33611a1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2021
ms.locfileid: "131866698"
---
# <a name="what-is-azure-iot-central"></a>Azure IoT Central とは

IoT Central は、エンタープライズ レベルの IoT ソリューションの開発、管理、および保守の負担とコストを削減する IoT アプリケーション プラットフォームです。 IoT Central を使用して構築することを選択すると、複雑で絶えず進化する IoT インフラストラクチャをただ維持し、更新するだけでなく、IoT データによってビジネスを変革することに時間、予算、エネルギーを集中させる機会が得られます。

Web UI を使用すると、すばやくデバイスを接続し、デバイスの状態を監視し、ルールを作成し、ライフ サイクル全体を通して何百万ものデバイスとそのデータを管理することができます。 さらに、IoT インテリジェンスを基幹業務アプリケーションまで拡張することで、デバイスの分析情報に従って行動できるようになります。

この記事では、IoT Central に関して以下の概要を説明します。

- アプリケーションを作成する方法。
- デバイスをアプリケーションに接続する方法。
- アプリケーションを他のサービスと統合する方法。
- アプリケーションを管理する方法。
- プロジェクトに関連付けられる一般的なユーザー ロール。
- 価格オプション。

## <a name="create-your-iot-central-application"></a>IoT Central アプリケーションを作成する

[新しい IoT Central アプリケーションをすばやくデプロイし](quick-deploy-iot-central.md)、具体的な要件に合わせてカスタマイズします。 汎用の "_アプリケーション テンプレート_"、または各業界にフォーカスした、いずれかのアプリケーション テンプレートを使用して開始します。

- [小売](../retail/overview-iot-central-retail.md)
- [エネルギー](../energy/overview-iot-central-energy.md)
- [政府](../government/overview-iot-central-government.md)
- [医療](../healthcare/overview-iot-central-healthcare.md)。

最初のアプリケーションを作成する方法のチュートリアルについては、[新しいアプリケーションの作成](quick-deploy-iot-central.md)に関するクイックスタートを参照してください。

## <a name="connect-devices"></a>デバイスの接続

アプリケーションを作成したら、次はまず、デバイスを作成して接続します。 IoT Central に接続されるすべてのデバイスでは、"_デバイス テンプレート_" を使用します。 デバイス テンプレートは、特定の種類のデバイスについて、その特性と動作を定義するブループリントです。次に示したのは、その例です。

- 送信するテレメトリ。 例としては、温度や湿度があります。 テレメトリはストリーミング データです。
- オペレーターによる変更が可能なビジネス プロパティ。 例としては、顧客の住所や前回の点検日があります。
- デバイスによって設定され、かつアプリケーションでは読み取り専用となるデバイス プロパティ。 例としては、バルブの状態 (開または閉) があります。
- オペレーターが設定する、デバイスの動作を決めるプロパティ。 例としては、デバイスの目標温度があります。
- オペレーターが呼び出してデバイスに対して実行されるコマンド。 たとえば、リモートからデバイスを再起動するコマンドがあります。

すべての[デバイス テンプレート](howto-set-up-template.md)には、次のものが含まれています。

- デバイスで実装する必要がある機能を記述する "_デバイス モデル_"。 デバイス機能の例を次に示します。

  - IoT Central にストリーム配信されるテレメトリ。
  - IoT Central に状態をレポートする際に使用される読み取り専用プロパティ。
  - デバイスの状態を設定するために IoT Central から送信される書き込み可能なプロパティ。
  - IoT Central から呼び出されるコマンド。

- クラウド プロパティ: これらはデバイスには格納されません。
- ご利用の Azure IoT Central アプリケーションの一部を成すカスタマイズ、フォーム、デバイス ビュー。

デバイス テンプレートを作成する場合、いくつかのオプションがあります。

- IoT Central でデバイス テンプレートを設計し、そのデバイス モデルをデバイス コードに実装します。
- Visual Studio Code を使用してデバイス モデルを作成し、モデルをリポジトリに発行します。 モデルからデバイス コードを実装し、デバイスを IoT Central アプリケーションに接続します。 IoT Central によって、リポジトリからデバイス モデルが検索され、単純なデバイス テンプレートが自動的に作成されます。
- Visual Studio Code を使用してデバイス モデルを作成します。 ご利用のデバイス コードをモデルから実装します。 ご利用の Azure IoT Central アプリケーションにデバイス モデルを手動でインポートし、Azure IoT Central アプリケーションに必要なクラウド プロパティ、カスタマイズ、ビューを追加します。

### <a name="customize-the-ui"></a>UI のカスタマイズ

アプリケーションを日常的に使用するオペレーターのために、IoT Central アプリケーションの UI をカスタマイズします。 次のようなカスタマイズを行うことができます。

- オペレーターによる分析情報の検出と問題の解決を迅速化するカスタム ダッシュボードを構成する。
- 接続されているデバイスから時系列データを探すカスタム分析を構成する。
- プロパティと設定のレイアウトをデバイス テンプレートで定義する。

## <a name="manage-your-devices"></a>デバイスを管理する

IoT Central アプリケーションを使用して、IoT Central ソリューションの[デバイスを管理します](howto-manage-devices-individually.md)。 オペレーターは、次のようなタスクを実行します。

- アプリケーションに接続されたデバイスを監視する。
- デバイスの問題をトラブルシューティングして修復する。
- 新しいデバイスをプロビジョニングする。

接続デバイスからのデータ ストリーミングに対して作用する[カスタム ルールやカスタム アクションを定義](howto-configure-rules.md)できます。 それらのルールをオペレーターがデバイス レベルで有効または無効にすることで、アプリケーション内のタスクを制御したり自動化したりすることができます。

大規模な運用を目的とした IoT ソリューションと同様に、デバイス管理には構造化されたアプローチが必要です。 デバイスをクラウドに接続するだけでは十分ではありません。デバイスを常に正常な接続状態に保つ必要があります。 次の IoT Central 機能を使用して、アプリケーション ライフ サイクル全体を通じてデバイスを管理します。

### <a name="dashboards"></a>ダッシュボード

アプリケーション テンプレートのあらかじめ構築されたダッシュボードを利用して開始するか、オペレーターのニーズに合わせた独自のダッシュボードを作成します。 ダッシュボードは、アプリケーション内のすべてのユーザーと共有することも、非公開に保つこともできます。

### <a name="rules-and-actions"></a>ルールとアクション

デバイスの状態とテレメトリに基づいて[カスタム規則](tutorial-create-telemetry-rules.md)を作成することで、注意を要するデバイスを迅速に特定します。 適切なユーザーに通知するアクションを構成し、適切なタイミングで是正措置が講じられるようにします。

### <a name="jobs"></a>ジョブ

[ジョブ](howto-manage-devices-in-bulk.md)を使用すると、プロパティを設定したりコマンドを呼び出したりして、デバイスに単独または一括の更新を実行できます。

## <a name="integrate-with-other-services"></a>他のサービスとの統合

IoT Central ではアプリケーション プラットフォームとして、IoT データを、アクションにつながる結果をもたらすビジネス分析情報に変換できます。 IoT Central を基幹業務アプリケーションに統合する方法の例としては、[規則](./tutorial-create-telemetry-rules.md)、[データのエクスポート](./howto-export-data.md)、[パブリック REST API](/learn/modules/manage-iot-central-apps-with-rest-api/) があります。

![IoT Central で IoT データを変換する方法](media/overview-iot-central/transform.png)

カスタムの分析パイプラインを構築してデバイスからのテレメトリを処理し、結果を格納することにより、工場現場における機械の効率性の傾向を判断したり、将来の電力使用量を予測したりするなど、ビジネス分析情報を生成できます。 IoT Central アプリケーションでデータのエクスポートを構成して、テレメトリ、デバイス プロパティの変更、デバイス テンプレートの変更を他のサービスにエクスポートします。このサービスでは、任意のツールを使用してデータを分析、保存、および視覚化することができます。

### <a name="build-custom-iot-solutions-and-integrations-with-the-rest-apis"></a>REST API を使用してカスタムの IoT ソリューションと統合を構築する

次のような IoT ソリューションを構築します。

- デバイスをリモートで設定および制御できるモバイル コンパニオン アプリ。
- 既存の基幹業務アプリケーションが IoT デバイスやデータと対話できるようにするカスタム統合。
- デバイスのモデル化、オンボード、管理、およびデータ アクセスを行うためのデバイス管理アプリケーション。

## <a name="administer-your-application"></a>アプリケーションの管理

IoT Central アプリケーションは、Microsoft によって完全にホストされるため、アプリケーションの管理オーバーヘッドが軽減されます。 アプリケーションへのアクセスは、管理者が[ユーザー ロールとアクセス許可](howto-administer.md)を使って管理します。

## <a name="pricing"></a>価格

7 日間の無料試用版を使用して IoT Central アプリケーションを作成することも、標準の料金プランを使用することもできます。

- "*無料*" のプランを使用して作成したアプリケーションは 7 日間無料で、最大 5 台のデバイスがサポートされます。 期限切れになるまでは、いつでも標準の料金プランを使用するように変換できます。
- "*標準*" のプランを使用して作成したアプリケーションは、デバイス単位で課金されます。**Standard 0**、**Standard 1**、**Standard 2** のいずれかの価格プランから選択でき、どちらも最初の 2 つのデバイスが無料となります。 [IoT Central の価格](https://aka.ms/iotcentral-pricing) についての詳細をご覧ください。

## <a name="user-roles"></a>ユーザー ロール

この IoT Central ドキュメントでは、IoT Central アプリケーションと対話する 4 種類のユーザー ロールに言及しています。

- "_ソリューション ビルダー_": [アプリケーションの作成](quick-deploy-iot-central.md)、[ルールとアクションの構成](quick-configure-rules.md)、[他のサービスとの統合の定義](quick-export-data.md)のほか、オペレーターおよびデバイス開発者向けのアプリケーションのカスタマイズを行う役割を担います。
- "_オペレーター_": アプリケーションに接続される [デバイスを管理](howto-manage-devices-individually.md)します。
- "_管理者_": 管理タスク (アプリケーション内での [ユーザー ロールとアクセス許可](howto-administer.md)の管理など) および他のサービスへの接続を保護するための [マネージド ID の構成](howto-manage-iot-central-from-portal.md#configure-a-managed-identity)を担います。
- "_デバイス開発者_": アプリケーションに接続されるデバイスまたは [IoT Edge モジュール](concepts-iot-edge.md)で [実行するコードを作成](concepts-telemetry-properties-commands.md)します。

## <a name="next-steps"></a>次のステップ

これで IoT Central の概要を把握できました。推奨される次の手順のいくつかを次に示します。

- デバイス開発者がコードの内容を詳しく知りたい場合は、次の手順として、[クライアント アプリケーションを作成して Azure IoT Central アプリケーションに接続する](./tutorial-connect-device.md)ことをお勧めします。
- [Azure IoT Central の UI](overview-iot-central-tour.md) に慣れる。
- 実際に [Azure IoT Central アプリケーションを作成](quick-deploy-iot-central.md)してみる。
