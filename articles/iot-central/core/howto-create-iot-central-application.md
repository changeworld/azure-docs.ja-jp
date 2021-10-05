---
title: IoT Central アプリケーションを作成する | Microsoft Docs
description: この記事では、Azure IoT Central サイト、Azure portal、およびコマンドライン環境から IoT Central アプリケーションを作成するためのオプションについて説明します。
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 05/11/2021
ms.topic: how-to
ms.openlocfilehash: 70567f3cb1a80c7d23d19bf7de43c80723af0dfe
ms.sourcegitcommit: 61e7a030463debf6ea614c7ad32f7f0a680f902d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/28/2021
ms.locfileid: "129094002"
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
- 必要な数のデバイスを接続できます。 デバイスごとに課金されます。 詳細については、「[Azure IoT Central の価格](https://azure.microsoft.com/pricing/details/iot-central/)」を参照してください。
- 無料プランにダウングレードすることはできませんが、他の Standard プランにアップグレードしたり、ダウングレードしたりすることはできます。

以下の表に、3 つの Standard プランの違いをまとめています。

| プラン名 | 無料デバイス | メッセージ/月 | 使用事例 |
| --------- | ------------ | -------------- | -------- |
| S0        | 2            | 400            | 1 日に数件のメッセージ |
| S1        | 2            | 5,000          | 1 時間に数件のメッセージ |
| S2        | 2            | 30,000         | 数分ごとにメッセージ |

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
- アプリケーションをホストする場所。 IoT Central は、オーストラリア東部、米国中部、米国東部、米国東部 2、東日本、北ヨーロッパ、東南アジア、英国南部、西ヨーロッパ、米国西部の場所として Azure リージョンを使用します。

## <a name="azure-iot-central-site"></a>Azure IoT Central サイト

IoT Central アプリケーションの作成を開始する最も簡単な方法は、[Azure IoT Central](https://aka.ms/iotcentral) サイトで行うことです。

[[ビルド]](https://apps.azureiotcentral.com/build) を使用して、使用するアプリケーション テンプレートを選択できます。

:::image type="content" source="media/howto-create-iot-central-application/choose-template.png" alt-text="アプリケーション テンプレートを選択できる [ビルド] ページのスクリーンショット。":::

**[アプリの作成]** を選択すると、アプリケーションを作成するために必要な情報をテンプレートから指定できます。

:::image type="content" source="media/howto-create-iot-central-application/create-application.png" alt-text="IoT Central の [アプリケーションの作成] ページを示すスクリーンショット。":::

**[マイ アプリ**] ページには、アクセスできるすべての IoT Central アプリケーションが一覧表示されます。 この一覧には、作成したアプリケーションと、アクセスが許可されているアプリケーションが含まれます。

> [!TIP]
> Azure IoT Central サイトで Standard 価格プランを使用して作成するすべてのアプリケーションでは、サブスクリプションの **IOTC** リソース グループが使用されます。 次のセクションで説明する手法を利用して、使用するリソース グループを選択することができます。

## <a name="copy-an-application"></a>アプリケーションをコピーする

デバイス インスタンス、デバイス データ履歴、およびユーザー データ以外の任意のアプリケーションのコピーを作成できます。 このコピーでは標準の料金プランが使用され、課金されます。 無料の料金プランが使用されるアプリケーションをアプリケーションをコピーして作成することはできません。

**[コピー]** を選択します。 ダイアログ ボックスに、新しいアプリケーションの詳細を入力します。 **[コピー]** を選択して、続行することを確認します。 このフォームのフィールドの詳細については、[アプリケーションの作成](howto-create-iot-central-application.md)に関するページを参照してください。

:::image type="content" source="media/howto-create-iot-central-application/app-copy-1.png" alt-text="アプリケーション設定ページを示すスクリーンショット。":::

:::image type="content" source="media/howto-create-iot-central-application/app-copy-2.png" alt-text="[アプリケーションのコピー] 設定ページを示すスクリーンショット。":::

アプリのコピー操作が成功したら、リンクを使用して新しいアプリケーションに移動できます。

アプリケーションをコピーすると、ルールやメール アクションの定義もコピーされます。 Flow や Logic Apps などの一部のアクションは、ルール ID を介して特定のルールに関連付けられています。 ルールが別のアプリケーションにコピーされると、そのルールに独自のルール ID が設定されます。 この場合、ユーザーは新しいアクションを作成してから、新しいルールを関連付ける必要があります。 一般には、ルールとアクションを調べ、それらが新しいアプリで最新の状態になっていることを確認することをお勧めします。

> [!WARNING]
> 特定のデバイスに関する情報を表示するタイルがダッシュボードに含まれている場合、それらのタイルに、新しいアプリケーションで **要求されたリソースが見つからなかった** ことが表示されます。 デバイスに関する情報が新しいアプリケーションに表示されるように、これらのタイルを再構成する必要があります。

## <a name="create-and-use-a-custom-application-template"></a>カスタム アプリケーション テンプレートを作成して使用する

Azure IoT Central アプリケーションを作成する場合、組み込みのサンプル テンプレートを選択できます。 既存の IoT Central アプリケーションから、独自のアプリケーション テンプレートを作成することもできます。 その後、新しいアプリケーションの作成時に、独自のアプリケーション テンプレートを使用できます。

アプリケーション テンプレートの作成時に、既存のアプリケーションからの以下の項目が含まれます。

- 既定のアプリケーションのダッシュボード (ダッシュボードのレイアウトおよび定義済みのすべてのタイルを含む)。
- デバイス テンプレート (測定値、設定、プロパティ、コマンドおよびダッシュボードを含む)。
- ルール。 すべてのルール定義が含まれます。 ただし、アクション (電子メールのアクションを除く) は含まれません。
- デバイス グループ (クエリを含む)。

> [!WARNING]
> 特定のデバイスに関する情報を表示するタイルがダッシュボードに含まれている場合、それらのタイルに、新しいアプリケーションで **要求されたリソースが見つからなかった** ことが表示されます。 デバイスに関する情報が新しいアプリケーションに表示されるように、これらのタイルを再構成する必要があります。

アプリケーション テンプレート作成時に以下の項目は含まれません。

- デバイス
- ユーザー
- 継続的データ エクスポートの定義

アプリケーション テンプレートから作成されたすべてのアプリケーションに手動でこれらの項目を追加します。

既存の IoT Central アプリケーションからアプリケーション テンプレートを作成するには、次の手順に従います。

1. アプリケーションの **[管理]** セクションに移動します。
1. **[Application Template Export]\(アプリケーション テンプレートのエクスポート\)** を選択します。
1. **[Application Template Export]\(アプリケーション テンプレートのエクスポート\)** ページで、テンプレートの名前と説明を入力します。
1. **[エクスポート]** を選択して、アプリケーション テンプレートを作成します。 そのテンプレートから他のユーザーが新しいアプリケーションを作成できるようにするための **共有可能リンク** をコピーできるようになります。

:::image type="content" source="media/howto-create-iot-central-application/create-template.png" alt-text="アプリケーション テンプレートの作成方法を示すスクリーンショット。":::

:::image type="content" source="media/howto-create-iot-central-application/create-template-2.png" alt-text="アプリケーション テンプレートのエクスポート方法を示すスクリーンショット。":::

### <a name="use-an-application-template"></a>アプリケーション テンプレートを使用する

アプリケーション テンプレートを使用して新しい IoT Central アプリケーションを作成するには、あらかじめ作成された **共有可能リンク** が必要になります。 ブラウザーのアドレス バーに **共有可能リンク** を貼り付けます。 **[アプリケーションの作成]** ページが、カスタム アプリケーション テンプレートが選択された状態で表示されます。

:::image type="content" source="media/howto-create-iot-central-application/create-app.png" alt-text="テンプレートからのアプリケーションの作成方法を示すスクリーンショット。":::

料金プランを選択し、フォーム上の他のフィールドを入力します。 次に、 **[作成]** を選択して、アプリケーション テンプレートから新しい IoT Central アプリケーションを作成します。

### <a name="manage-application-templates"></a>アプリケーション テンプレートを管理する

**[Application Template Export]\(アプリケーション テンプレートのエクスポート\)** ページで、アプリケーション テンプレートの削除や更新が行えます。

アプリケーション テンプレートを削除した場合、以前に生成した共有可能リンクを使用して新しいアプリケーションを作成することができなくなります。

アプリケーション テンプレートを更新するには、 **[Application Template Export]\(アプリケーション テンプレートのエクスポート\)** ページでテンプレート名や説明を変更します。 その後、 **[エクスポート]** を再度選択します。 このアクションにより、新しい **共有可能リンク** が生成され、以前に作成した **共有可能リンク** の URL が無効になります。

## <a name="other-approaches"></a>その他のアプローチ

また、次のアプローチを使用して IoT Central アプリケーションを作成することもできます。

- [Azure portal から IoT Central アプリケーションを作成する](howto-manage-iot-central-from-portal.md#create-iot-central-applications)
- [コマンド ラインを使用して IoT Central アプリケーションを作成する](howto-manage-iot-central-from-cli.md#create-an-application)
- [IoT Central アプリケーションのプログラムを作成する](/samples/azure-samples/azure-iot-central-arm-sdk-samples/azure-iot-central-arm-sdk-samples/)

## <a name="next-steps"></a>次のステップ

ここでは、Azure CLI から Azure IoT Central アプリケーションを管理する方法について説明しました。推奨される次の手順は以下のとおりです。

> [!div class="nextstepaction"]
> [アプリケーションを管理する](howto-administer.md)
