---
title: Azure IoT Central でのソリューションの構築 | Microsoft Docs
description: Azure IoT Central は、IoT ソリューションの作成を簡単にする IoT アプリケーション プラットフォームです。 この記事では、IoT Central を使用した統合ソリューションの構築の概要について説明します。
author: dominicbetts
ms.author: dobett
ms.date: 02/11/2021
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: 5ceb6950cb5ed581d2efea9a375fee0bf4008952
ms.sourcegitcommit: 5d605bb65ad2933e03b605e794cbf7cb3d1145f6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2021
ms.locfileid: "122597855"
---
# <a name="iot-central-solution-builder-guide"></a>IoT Central ソリューション ビルダーのガイド

IoT Central アプリケーションを使用すると、数百万台ものデバイスを、そのライフ サイクル全体にわたって監視および管理することができます。 このガイドは、IoT Central を使用して統合ソリューションを構築するソリューション ビルダーを対象としています。 IoT Central アプリケーションを使用すると、デバイスの管理、デバイス テレメトリの分析、および他のバックエンド サービスとの統合を行うことができます。

ソリューション ビルダー:

- IoT Central の Web UI でダッシュボードとビューを構成します。
- 組み込みのルールと分析ツールを使用して、接続されているデバイスからビジネス分析情報を取得します。
- データのエクスポートとルールの機能を使用して、IoT Central を他のバックエンド サービスと統合します。

## <a name="configure-dashboards-and-views"></a>ダッシュボードとビューを構成する

IoT Central アプリケーションでは、オペレーターがアプリケーションを表示および操作するために使用する 1 つ以上のダッシュボードを利用できます。 ソリューション ビルダーは、既定のダッシュボードをカスタマイズしたり、特殊なダッシュボードを作成したりできます。

- カスタマイズされたダッシュボードの例については、「[特定業界に焦点を合わせたテンプレート](concepts-app-templates.md#industry-focused-templates)」を参照してください。
- ダッシュボードの詳細については、「[複数のダッシュボードの作成と管理](howto-manage-dashboards.md)」と「[アプリケーション ダッシュボードの構成](howto-manage-dashboards.md)」を参照してください。

デバイスが IoT Central に接続されると、デバイスは、そのデバイスの種類用のデバイス テンプレートに関連付けられます。 デバイス テンプレートには、オペレーターが個々のデバイスを管理するために使用するカスタマイズ可能なビューがあります。 ソリューション開発者は、デバイスの種類で使用可能なビューを作成およびカスタマイズできます。 詳細については、「[ビューの追加](howto-set-up-template.md#views)」を参照してください。

## <a name="use-built-in-rules-and-analytics"></a>組み込みのルールと分析を使用する

ソリューション開発者は、IoT Central アプリケーションに、カスタマイズ可能なアクションを実行するルールを追加できます。 ルールにより、デバイスからのデータに基づいて条件が評価され、アクションを実行するタイミングが決定されます。 詳細については、次を参照してください。

- [チュートリアル:Azure IoT Central アプリケーションで規則を作成して通知を設定する](tutorial-create-telemetry-rules.md)
- [ルールを構成する](howto-configure-rules.md)

IoT Central には、オペレーターが、接続されているデバイスからのデータを分析するために使用できる組み込みの分析機能があります。 詳細については、「[分析を使用してデバイス データを分析する方法](howto-create-analytics.md)」を参照してください。

## <a name="integrate-with-other-services"></a>他のサービスとの統合

ソリューション ビルダーは、IoT Central のデータのエクスポートとルールの機能を使用して他のサービスと統合できます。 詳細については、以下をご覧ください。

- [データ エクスポートを使用してクラウドの宛先に IoT データをエクスポートする](howto-export-data.md)
- [IoT Central に対するデータの変換](howto-transform-data.md)
- [ワークフローを使用して Azure IoT Central アプリケーションを他のクラウド サービスと統合する](howto-configure-rules-advanced.md)
- [Stream Analytics、Azure Functions、SendGrid を使用してカスタム ルールで Azure IoT Central を拡張する](howto-create-custom-rules.md)
- [Azure Databricks を使用したカスタム分析で Azure IoT Central を拡張する](howto-create-custom-analytics.md)

## <a name="apis"></a>API

IoT Central API を使用すると、IoT ソリューション内の他のサービスとの深い統合を構築できます。 使用可能な API は、*データ プレーン API*  または *コントロール プレーン API* として分類されます。

データ プレーン API を使用して、IoT Central アプリケーションのエンティティと機能にアクセスします。 たとえば、デバイス、デバイス テンプレート、ユーザー、ロールの管理などです。 IoT Central REST API の操作は *データ プレーン* 操作です。 詳細については、[「ユーザーとロールを 管理するためにIoT Central REST APIを使用する方法」](howto-manage-users-roles-with-rest-api.md)を参照してください。

*コントロール プレーン* は、Azure サブスクリプション内の IoT Central 関連リソースを管理するために使用します。 コントロール プレーン操作には、Azure CLI テンプレートと Resource Manager テンプレートを使用できます。 たとえば Azure CLI を使ってIoT Central アプリケーションを作成することができます。 詳細については、[「Azure CLI から IoT Central を管理する」](howto-manage-iot-central-from-cli.md)を参照してください。

## <a name="next-steps"></a>次のステップ

IoT Central の詳細な使用方法については、次の手順として、クイックスタートの「[Azure IoT Central アプリケーションの作成](./quick-deploy-iot-central.md)」から始めることをお勧めします。
