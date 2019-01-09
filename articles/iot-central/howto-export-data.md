---
title: Azure IoT Central でデータをエクスポートする | Microsoft Docs
description: Azure IoT Central アプリケーションからデータをエクスポートする方法
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 12/07/2018
ms.topic: conceptual
ms.service: iot-central
manager: peterpr
ms.openlocfilehash: cba0bad2e81ffddedfc4ca04e82e17e4286b389b
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/12/2018
ms.locfileid: "53312121"
---
# <a name="export-your-data-in-azure-iot-central"></a>Azure IoT Central でデータをエクスポートする

*このトピックでは、管理者に適用されます。*

この記事では、データを自身の **Azure Blob Storage**、**Azure Event Hubs**、および **Azure Service Bus** のインスタンスにエクスポートする Azure IoT Central の継続的データ エクスポート機能の使用方法について説明します。 ウォーム パスとコールド パスの分析のため、**測定**、**デバイス**、および**デバイス テンプレート**を自身が指定した場所にエクスポートすることができます。 Microsoft Power BI で長期的な傾向分析を実行するために BLOB ストレージにデータをエクスポートしたり、Azure Logic Apps や Azure Functions でほぼリアルタイムでデータを変換および拡大するために、Event Hubs および Service Bus にデータをエクスポートしたりすることができます。

> [!Note]
> 連続データ エクスポートを有効にすると、その時点以降のデータのみが取得されます。 現在は、連続データ エクスポートがオフになっていたときのデータを取得することはできません。 より多くの履歴データを保持するには、連続データ エクスポートを早い段階で有効にしてください。

## <a name="prerequisites"></a>前提条件

- ご利用の IoT Central アプリケーションの管理者であること

## <a name="export-to-blob-storage"></a>Blob Storage へのエクスポート

測定、デバイス、およびデバイス テンプレートのデータは、1 分間に 1 回、ご自身のストレージ アカウントにエクスポートされます。各ファイルには、前回ファイルをエクスポートしてからの変更のバッチが含まれています。 エクスポートされたデータは、[Apache AVRO](https://avro.apache.org/docs/current/index.html) 形式になります。

詳細については、[BLOB ストレージへのエクスポート](howto-export-data-blob-storage.md)に関するページを参照してください。

## <a name="export-to-event-hubs-and-service-bus"></a>Event Hubs と Service Bus へのエクスポート

測定、デバイス、およびデバイス テンプレートのデータは、ご使用のイベント ハブ、Service Bus キュー、またはトピックのいずれかにエクスポートされます。 エクスポートされた測定データは、ほぼリアルタイムで受信されます。このデータには、その測定値だけでなく、デバイスが IoT Central に送信したメッセージ全体が含まれています。 エクスポートされたデバイス データは、1 分回に 1 回、バッチで受信されます。このデータには、すべてのデバイスのプロパティと設定の変更が含まれています。エクスポートされたデバイス テンプレートには、すべてのデバイス テンプレートに対する変更が含まれています。


詳細については、[Event Hubs と Service Bus へのエクスポート](howto-export-data-event-hubs-service-bus.md)に関するページを参照してください。

## <a name="set-up-export-destination"></a>エクスポート先の設定

エクスポート先となる既存の Storage/Event Hubs/Service Bus がない場合は、次の手順に従います。

### <a name="create-storage-account"></a>ストレージ アカウントの作成

1. [Azure portal で新しいストレージ アカウント](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM)を作成します。 詳細については、[Azure Storage のドキュメント](https://aka.ms/blobdocscreatestorageaccount)を参照してください。
2. アカウントの種類として、**[汎用]** または **[BLOB ストレージ]** を選択します。
3. サブスクリプションを選択します。 

    > [!Note] 
    > これで、ご使用の従量課金制 IoT Central アプリケーションのサブスクリプションとは**異なる**別のサブスクリプションにデータをエクスポートできます。 この場合、接続文字列を使用して接続します。

4. ご自分のストレージ アカウントでコンテナーを作成します。 ストレージ アカウントに移動します。 **[Blob service]** で **[BLOB の参照]** を選択します。 上部の **[+ コンテナー]** を選択して、新しいコンテナーを作成します。

### <a name="create-event-hubs-namespace"></a>Event Hubs 名前空間の作成

1. [Azure portal で新しい Event Hubs 名前空間](https://ms.portal.azure.com/#create/Microsoft.EventHub)を作成します。 詳細については、[Azure Event Hubs のドキュメント](https://docs.microsoft.com/azure/event-hubs/event-hubs-create)を参照してください。
2. サブスクリプションを選択します。 

    > [!Note] 
    > これで、ご使用の従量課金制 IoT Central アプリケーションのサブスクリプションとは**異なる**別のサブスクリプションにデータをエクスポートできます。 この場合、接続文字列を使用して接続します。
3. Event Hubs 名前空間にイベント ハブを作成します。 名前空間に移動し、上部の **[+ イベント ハブ]** を選択して、イベント ハブ インスタンスを作成します。

### <a name="create-service-bus-namespace"></a>Service Bus 名前空間の作成

1. [Azure portal で新しい Service Bus 名前空間](https://ms.portal.azure.com/#create/Microsoft.ServiceBus.1.0.5)を作成します。 詳細については、[Azure Service Bus のドキュメント](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-create-namespace-portal)を参照してください。
2. サブスクリプションを選択します。 

    > [!Note] 
    > これで、ご使用の従量課金制 IoT Central アプリケーションのサブスクリプションとは**異なる**別のサブスクリプションにデータをエクスポートできます。 この場合、接続文字列を使用して接続します。

3. Service Bus 名前空間に移動し、上部の **[+ キュー]** または **[+ トピック]** を選択して、エクスポート先のキューまたはトピックを作成します。

## <a name="set-up-continuous-data-export"></a>連続データ エクスポートを設定する

これでエクスポート データのエクスポート先となる Storage/Event Hubs/Service Bus ができました。次の手順に従って、継続的データ エクスポートを設定します。 

1. ご使用の IoT Central アプリケーションにサインインします。

2. 左側のメニューで、**[継続的データ エクスポート]** をクリックします。

    > [!Note]
    > 左側のメニューに [継続的データ エクスポート] が表示されない場合は、そのアプリの管理者ではありません。 データ エクスポートの設定について、管理者に問い合わせてください。

    ![新しい cde イベント ハブの作成](media/howto-export-data/export_menu.PNG)

3. 右上の **[+ 新規]** ボタンをクリックします。 エクスポート先として、**Azure Blob Storage**、**Azure Event Hubs**、または **Azure Service Bus** のいずれかを選択します。 

    > [!NOTE] 
    > アプリごとのエクスポートの最大数は 5 です。 

    ![新しい継続的データ エクスポートの作成](media/howto-export-data/export_new.PNG)

4. ドロップダウン リスト ボックスで、**Storage Account/Event Hubs 名前空間/Service Bus 名前空間**を選択します。 リスト内の最後のオプション (**[Enter a connection string]\(接続文字列を入力する\)**) を選択することもできます。 

    > [!NOTE] 
    > ご使用の **IoT Central アプリと同じサブスクリプション**の Storage Account/Event Hubs 名前空間/Service Bus 名前空間のみが表示されます。 このサブスクリプションとは異なる場所にエクスポートする場合は、**[Enter a connection string]\(接続文字列を入力する\)** を選択して、手順 5 に進みます。

    > [!NOTE] 
    > 7 日間の試用版アプリの場合、継続的データ エクスポートを構成する唯一の方法は、接続文字列を使用することです。 7 日間の試用版アプリに関連付けられた Azure サブスクリプションがないのはこのためです。

    ![新しい cde イベント ハブの作成](media/howto-export-data/export_create.PNG)

5. (省略可能) **[Enter a connection string]\(接続文字列を入力する\)** を選択すると、接続文字列を貼り付けるための新しいボックスが表示されます。 次の接続文字列を取得するには:
    - ストレージ アカウント。Azure portal で [ストレージ アカウント] に移動します。
        - **[設定]** で **[アクセス キー]** をクリックします。
        - key1 接続文字列または key2 接続文字列のいずれかをコピーします。
    - Event Hubs や Service Bus。Azure portal で名前空間に移動します。
        - **[設定]** で **[共有アクセス ポリシー]** をクリックします。
        - 既定の **RootManageSharedAccessKey** を選択するか、新しいポリシーを作成します。
        - プライマリまたはセカンダリの接続文字列をコピーします。
 
6. ドロップダウン リスト ボックスから、コンテナー/イベント ハブ/キューまたはトピックを選択します。

7. **[Data to export]\(エクスポートするデータ\)** で、エクスポートするデータの種類を **[オン]** に設定して指定します。

6. 継続的データ エクスポートを有効にするには、**[データのエクスポート]** が **[オン]** になっていることを確認します。 **[保存]** を選択します。

  ![連続データ エクスポートを構成する](media/howto-export-data/export_list.PNG)

7. 数分後に、選択したエクスポート先にデータが表示されます。

## <a name="next-steps"></a>次の手順

データをエクスポートする方法がわかったら、次の手順に進みます。

> [!div class="nextstepaction"]
> [Azure Blob Storage にデータをエクスポートする](howto-export-data-blob-storage.md)

> [!div class="nextstepaction"]
> [Azure Event Hubs および Azure Service Bus にデータをエクスポートする](howto-export-data-event-hubs-service-bus.md)

> [!div class="nextstepaction"]
> [Power BI でデータを視覚化する方法](howto-connect-powerbi.md)
