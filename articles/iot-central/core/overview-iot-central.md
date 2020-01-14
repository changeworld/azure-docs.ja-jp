---
title: Azure IoT Central とは | Microsoft Docs
description: Azure IoT Central は、IoT ソリューションの作成を簡単にする IoT アプリケーション プラットフォームです。 この記事では、Azure IoT Central の機能について概要を紹介します。
author: dominicbetts
ms.author: dobett
ms.date: 08/26/2019
ms.topic: overview
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: timlt
ms.openlocfilehash: a1aa2f12f62a95ac14750c821079df2bac46e8ac
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/25/2019
ms.locfileid: "75434966"
---
<!---
Purpose of an Overview article: 
1. To give a TECHNICAL overview of a service/product: What is it? Why should I use it? It's a "learn" topic that describes key benefits and our competitive advantage. It's not a "do" topic.
2. To help audiences who are new to service but who may be familiar with related concepts. 
3. To compare the service to another service/product that has some similar functionality, ex. SQL Database / SQL Data Warehouse, if appropriate. This info can be in a short list or table. 
-->

# <a name="what-is-azure-iot-central"></a>Azure IoT Central とは

[!INCLUDE [iot-central-original-pnp](../../../includes/iot-central-original-pnp-note.md)]

IoT Central は、エンタープライズ レベルの IoT ソリューションの開発、管理、および保守に関連する負担とコストを削減する IoT アプリ プラットフォームです。 Azure IoT Central を使用して構築することを選択すると、複雑で絶えず進化する IoT インフラストラクチャをただ維持し、更新するだけでなく、IoT データによってビジネスを変革することに時間、予算、エネルギーを集中させる機会が得られます。

使いやすいインターフェイスにより、簡単にデバイスの状態を監視し、ルールを作成し、ライフ サイクル全体を通して何百万ものデバイスとそのデータを管理することができます。 さらに、IoT インテリジェンスを基幹業務アプリケーションまで拡張することで、デバイスの分析情報に従って行動できるようになります。

この記事では、Azure IoT Central の要点を取り上げます。

- プロジェクトに関連する代表的なペルソナ。
- アプリケーションを作成する方法。
- デバイスをアプリケーションに接続する方法。
- アプリケーションを管理する方法。

## <a name="personas"></a>ペルソナ

この Azure IoT Central ドキュメントでは、Azure IoT Central アプリケーションと対話する 4 種類のペルソナについて言及しています。

- "_ビルダー_": アプリケーションに接続するデバイスの種類を定義したり、オペレーター向けにアプリケーションをカスタマイズしたりする役割を担います。
- "_オペレーター_": アプリケーションに接続されたデバイスを管理します。
- "_管理者_":アプリケーション内での[ユーザー ロールとアクセス許可](howto-administer.md)の管理などのタスクを担当します。
- "_デバイス開発者_": アプリケーションに接続されたデバイス上で実行するコードを作成します。

## <a name="create-your-azure-iot-central-application"></a>Azure IoT Central アプリケーションの作成

クラウドでホストされる自社のカスタム IoT ソリューションは、ビルダーが Azure IoT Central を使用して作成します。 通常、カスタム IoT ソリューションは次の要素から成ります。

- クラウドベースのアプリケーション。このアプリケーションによってデバイスからテレメトリを受信し、それらのデバイスの管理が可能となります。
- カスタム コードを実行する複数のデバイス。これらのデバイスが、クラウドベースのアプリケーションに接続されます。

新しい Azure IoT Central アプリケーションは、ブラウザーからすばやくデプロイし、具体的な要件に合わせてカスタマイズすることができます。 ビルダーは、自社アプリケーションに接続するデバイス用に、Web ベースのツールを使って "_デバイス テンプレート_" を作成します。 デバイス テンプレートは、特定の種類のデバイスについて、その特性と動作を定義するブループリントです。次に示したのは、その例です。

- 送信するテレメトリ。
- オペレーターによる変更が可能なビジネス プロパティ。
- デバイスによって設定され、かつアプリケーションでは読み取り専用となるデバイス プロパティ。
- アプリケーションが反応するしきい値。
- デバイスの動作を決める設定。

デバイス テンプレートとアプリケーションは、Azure IoT Central から自動的に生成されるシミュレーション データを使って、すぐにテストすることができます。

また、ビルダーは、日常的にアプリケーションを使うオペレーター向けに、Azure IoT Central アプリケーションの UI をカスタマイズすることもできます。 ビルダーが行うことのできるカスタマイズの例を次に示します。

- プロパティと設定のレイアウトをデバイス テンプレートで定義する。
- オペレーターによる分析情報の検出と問題の解決を迅速化するカスタム ダッシュボードを構成する。
- 接続されているデバイスから時系列データを探すカスタム分析を構成する。

## <a name="connect-your-devices"></a>デバイスの接続

アプリケーションに接続できるデバイスの種類がビルダーによって定義されたら、そのデバイス上で実行するコードをデバイス開発者が作成します。 デバイス開発者は、Microsoft のオープンソース [Azure IoT SDK](https://github.com/Azure/azure-iot-sdks) を使用してデバイス コードを作成します。 Azure IoT Central アプリケーションに自社のデバイスを接続するというお客様のニーズを満たすために、これらの SDK は多様な言語、プラットフォーム、プロトコルに対応しています。 これらの SDK を利用することで、次のようなデバイス機能が容易に実装できます。

- 安全な接続を作成する。
- テレメトリを送信する。
- ステータスを報告する。
- 構成の更新を受信する。

詳細については、ブログ記事「[Benefits of using the Azure IoT SDKs, and pitfalls to avoid if you don't (Azure IoT SDK を使う利点と使わなかった場合に回避すべきリスク)](https://azure.microsoft.com/blog/benefits-of-using-the-azure-iot-sdks-in-your-azure-iot-solution/)」を参照してください。

## <a name="manage-your-application"></a>アプリケーションの管理

Azure IoT Central アプリケーションは、完全に Microsoft によってホストされるため、アプリケーションの管理オーバーヘッドが軽減されます。

Azure IoT Central ソリューションのデバイスは、オペレーターが Azure IoT Central アプリケーションを使用して管理します。 オペレーターは、次のようなタスクを実行します。

- アプリケーションに接続されたデバイスを監視する。
- デバイスの問題をトラブルシューティングして修復する。
- 新しいデバイスをプロビジョニングする。

ビルダーは、接続デバイスからのデータ ストリーミングに対して作用するカスタム ルールやカスタム アクションを定義できます。 それらのルールをオペレーターがデバイス レベルで有効または無効にすることで、アプリケーション内のタスクを制御したり自動化したりすることができます。

アプリケーションへのアクセスは、管理者が[ユーザー ロールとアクセス許可](howto-administer.md)を使って管理します。

## <a name="next-steps"></a>次のステップ

これで Azure IoT Central の概要は終了です。推奨される次の手順は以下のとおりです。

- [IoT ソリューションを作成するための Azure のテクノロジとサービス](../../iot-fundamentals/iot-services-and-technologies.md)を把握する。
- [Azure IoT Central の UI](overview-iot-central-tour.md) に慣れる。
- 実際に [Azure IoT Central アプリケーションを作成](quick-deploy-iot-central.md)してみる。
- 一連のチュートリアルに従って、以下の作業方法を確認する。
  - [ビルダーとしてデバイス テンプレートを作成する](tutorial-define-device-type.md)
  - [ビルダーとして、ソリューションを自動化するためのルールを追加する](tutorial-configure-rules.md)
  - [ビルダーとして、オペレーター向けにアプリケーションをカスタマイズする](tutorial-customize-operator.md)
  - [オペレーターとしてデバイスを監視する](tutorial-monitor-devices.md)
  - [オペレーターとして、実デバイスをソリューションに追加する](tutorial-add-device.md)
  - [デバイス開発者として、デバイス向けのコードを作成する](tutorial-add-device.md#prepare-the-client-code)
