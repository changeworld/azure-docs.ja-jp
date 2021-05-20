---
title: IoT Central アプリケーションを作成する | Microsoft Docs
description: この記事では、Azure IoT Central サイト、Azure portal、およびコマンドライン環境から IoT Central アプリケーションを作成するためのオプションについて説明します。
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 05/11/2021
ms.topic: how-to
ms.openlocfilehash: 10791fac984a5df4a7fba95217f00ef6a2114b86
ms.sourcegitcommit: b35c7f3e7f0e30d337db382abb7c11a69723997e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/10/2021
ms.locfileid: "109689336"
---
# <a name="create-an-iot-central-application"></a>IoT Central アプリケーションを作成する

IoT Central アプリケーションを作成するには、いくつかの方法があります。 手動によるアプローチが必要な場合は、GUI ベースの方法のいずれかを使用できます。また、プロセスの自動化が必要な場合は、CLI またはプログラムによる方法のいずれかを使用できます。

どちらの方法を選択しても、構成オプションは同じであり、プロセスは通常、完了までに 1 分もかかりません。

[!INCLUDE [Warning About Access Required](../../../includes/iot-central-warning-contribitorrequireaccess.md)]

## <a name="options"></a>オプション

このセクションでは、IoT Central アプリケーションを作成するときに使用できるオプションについて説明します。 選択した方法に応じて、フォーム上で、またはコマンドライン パラメーターとしてオプションを指定することが必要になる場合があります。

### <a name="pricing-plans"></a>価格プラン

*無料* プランを使用すると、IoT Central アプリケーションを作成して 7 日間お試しいただけます。 無料プランは次のような内容です。

- Azure サブスクリプションは不要です。
- [Azure IoT Central](https://aka.ms/iotcentral) サイトでのみ作成および管理できます。
- 最大 5 台のデバイスを接続できます。
- アプリケーションを継続したい場合は、Standard プランにアップグレードできます。

*Standard* プランは次のような内容です。

- Azure サブスクリプションが必要です。 Azure サブスクリプションに、少なくとも **共同作成者** のアクセス権を持っている必要があります。 ご自身でサブスクリプションを作成した場合は、ご自分が自動的に十分なアクセス権を持つ管理者になります。 詳細については、「[Azure ロールベースのアクセス制御とは](../../role-based-access-control/overview.md)」を参照してください。
- いずれかの使用可能な方法を使用して IoT Central アプリケーションを作成および管理できます。
- 必要な数のデバイスを接続できます。 デバイスごとに課金されます。 詳細については、「[Azure IoT Central の価格](/pricing/details/iot-central/)」を参照してください。
- 無料プランにダウングレードすることはできませんが、他の Standard プランにアップグレードしたり、ダウングレードしたりすることはできます。

以下の表に、3 つの Standard プランの違いをまとめています。

| プラン名 | 無料デバイス | メッセージ/月 | 使用事例 |
| --------- | ------------ | -------------- | -------- |
| S0        | 2            | 400            | 1 日に数件のメッセージ |
| S1        | 2            | 5,000          | 1 時間に数件のメッセージ |
| S2        | 2            | 30,000         | 数分ごとにメッセージ |

詳細については、「[IoT Central アプリケーションでの請求書の管理](howto-view-bill.md)」を参照してください。

### <a name="application-name"></a>アプリケーション名

選択した _アプリケーション名_ は、IoT Central アプリケーション内のすべてのページのタイトル バーに表示されます。 また、[Azure IoT Central](https://aka.ms/iotcentral) サイトの **[マイ アプリ]** ページのアプリケーションのタイルにも表示されます。

選択した _サブドメイン_ によって、アプリケーションが一意に識別されます。 サブドメインは、アプリケーションへのアクセスに使用する URL の一部です。 IoT Central アプリケーションの URL は、`https://yoursubdomain.azureiotcentral.com` のようになります。

### <a name="application-template-id"></a>アプリケーション テンプレート ID

選択したアプリケーション テンプレートによって、ダッシュボードやデバイス テンプレートなど、アプリケーションの初期コンテンツが確定します。 カスタム アプリケーションの場合、テンプレート ID として `iotc-pnp-preview` を使用してください。

カスタムおよび業界ごとに的を絞ったアプリケーション テンプレートの詳細については、「[アプリケーション テンプレートとは](concepts-app-templates.md)」を参照してください。

### <a name="billing-information"></a>課金情報

いずれかの Standard プランを選択した場合は、課金情報を指定する必要があります。

- 使用している Azure サブスクリプション。
- 使用しているサブスクリプションを含むディレクトリ。
- アプリケーションをホストする場所。 IoT Central では場所として Azure の地域を使用します: 米国、ヨーロッパ、アジア太平洋、オーストラリア、英国、または日本。

## <a name="azure-iot-central-site"></a>Azure IoT Central サイト

IoT Central アプリケーションの作成を開始する最も簡単な方法は、[Azure IoT Central](https://aka.ms/iotcentral) サイトで行うことです。

[[ビルド]](https://apps.azureiotcentral.com/build) を使用して、使用するアプリケーション テンプレートを選択できます。

:::image type="content" source="media/howto-create-iot-central-application/choose-template.png" alt-text="アプリケーション テンプレートを選択できる [ビルド] ページのスクリーンショット。":::

**[アプリの作成]** を選択すると、アプリケーションを作成するために必要な情報をテンプレートから指定できます。

:::image type="content" source="media/howto-create-iot-central-application/create-application.png" alt-text="IoT Central の [アプリケーションの作成] ページを示すスクリーンショット。":::

**[マイ アプリ**] ページには、アクセスできるすべての IoT Central アプリケーションが一覧表示されます。 この一覧には、作成したアプリケーションと、アクセスが許可されているアプリケーションが含まれます。

> [!TIP]
> Azure IoT Central サイトで Standard 価格プランを使用して作成するすべてのアプリケーションでは、サブスクリプションの **IOTC** リソース グループが使用されます。 次のセクションで説明するアプローチを利用すると、使用するリソース グループを選択することができます。

## <a name="other-approaches"></a>その他のアプローチ

また、次のアプローチを使用して IoT Central アプリケーションを作成することもできます。

- [Azure portal から IoT Central アプリケーションを作成する](howto-manage-iot-central-from-portal.md#create-iot-central-applications)
- [Azure CLI を使用して IoT Central アプリケーションを作成する](howto-manage-iot-central-from-cli.md#create-an-application)
- [PowerShell を使用して IoT Central アプリケーションを作成する](howto-manage-iot-central-from-powershell.md#create-an-application)
- [IoT Central アプリケーションのプログラムを作成する](howto-manage-iot-central-programmatically.md)

## <a name="next-steps"></a>次のステップ

ここでは、Azure CLI から Azure IoT Central アプリケーションを管理する方法について説明しました。推奨される次の手順は以下のとおりです。

> [!div class="nextstepaction"]
> [アプリケーションを管理する](howto-administer.md)
