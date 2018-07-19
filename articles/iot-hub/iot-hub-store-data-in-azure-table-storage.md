---
title: IoT Hub メッセージを Azure データ ストレージに保存する | Microsoft Docs
description: IoT Hub のメッセージ ルーティング機能を使用して、IoT ハブ メッセージを Azure Blob Storage に保存します。 IoT Hub メッセージには、IoT デバイスから送信されたセンサー データなどの情報が含まれます。
author: rangv
manager: ''
keywords: IoT データ ストレージ, IoT センサー データ ストレージ
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 04/11/2018
ms.author: rangv
ms.openlocfilehash: 84355453a5cb8d8f42abdcbde5432651c9c035b0
ms.sourcegitcommit: 0b4da003fc0063c6232f795d6b67fa8101695b61
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/05/2018
ms.locfileid: "37856292"
---
# <a name="save-iot-hub-messages-that-contain-sensor-data-to-your-azure-blob-storage"></a>センサー データを含む IoT ハブ メッセージを Azure Blob Storage に保存する

![エンド ツー エンド ダイアグラム](./media/iot-hub-store-data-in-azure-table-storage/1_route-to-storage.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

## <a name="what-you-learn"></a>学習内容

Azure ストレージ アカウントと Azure 関数アプリを作成し、IoT ハブ メッセージを Azure Blob Storage に保存する方法について学習します。

## <a name="what-you-do"></a>作業内容

- Azure ストレージ アカウントを作成します。
- ストレージにメッセージをルーティングするために IoT ハブを設定します。

## <a name="what-you-need"></a>必要なもの

- 次の要件に対応するための[デバイスのセットアップ](iot-hub-raspberry-pi-kit-node-get-started.md):
  - 有効な Azure サブスクリプション
  - サブスクリプションの IoT Hub 
  - Azure IoT ハブにメッセージを送信する実行中のアプリケーション

## <a name="create-an-azure-storage-account"></a>Azure のストレージ アカウントの作成

1. [Azure Portal](https://portal.azure.com/) では、**[リソースの作成]** > **[ストレージ]** > **[ストレージ アカウント]** をクリックします。

2. ストレージ アカウントに必要な情報を入力します。

   ![Azure ポータルでのストレージ アカウントの作成](./media/iot-hub-store-data-in-azure-table-storage/1_azure-portal-create-storage-account.png)

   * **[名前]**: ストレージ アカウントの名前。 名前はグローバルに一意である必要があります。

   * **アカウントの種類**: `Storage (general purpose v1)` を選択します。

   * **場所**: IoT Hub で使用するのと同じ場所を選択します。

   * **レプリケーション**: `Locally-redundant storage (LRS)` を選択します。

   * **パフォーマンス**: `Standard` を選択します。

   * **安全な転送が必須**: `Disabled` を選択します。

   * **[サブスクリプション]**: Azure サブスクリプションを選択します。

   * **[リソース グループ]**: IoT ハブと同じリソース グループを使用します。

   * **[ダッシュボードにピン留めする]**: ダッシュボードから IoT Hub に簡単にアクセスできるようにするには、このオプションをオンにします。

3. **Create** をクリックしてください。

## <a name="prepare-your-iot-hub-to-route-messages-to-storage"></a>ストレージにメッセージをルーティングするための IoT ハブの準備

IoT Hub は、BLOB としての Azure Storage へのメッセージのルーティングをネイティブにサポートします。 Azure IoT Hub カスタム エンドポイントの詳細については、「[IoT Hub の組み込みエンドポイントの一覧](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-endpoints#custom-endpoints)」を参照してください。

### <a name="add-storage-as-a-custom-endpoint"></a>ストレージをカスタム エンドポイントとして追加する

1. Azure Portal で、IoT ハブに移動します。 

2. **[エンドポイント]** > **[追加]** の順にクリックします。 

3. エンドポイントの名前を指定し、エンドポイントの種類として **[Azure ストレージ コンテナー]** を選択します。 

4. ピッカーを使用して、前のセクションで作成したストレージ アカウントを選択します。 ストレージ コンテナーを作成してそれを選択し、**[OK]** をクリックします。

   ![IoT Hub でのカスタムエンドポイントの作成](./media/iot-hub-store-data-in-azure-table-storage/2_custom-storage-endpoint.png)

### <a name="add-a-route-to-route-data-to-storage"></a>ストレージにデータをルーティングするためのルートを追加する

1. **[ルート]** > **[追加]** の順にクリックして、ルートの名前を入力します。 

2. データ ソースとして **[デバイス メッセージ]** を選択し、ルートのエンドポイントとして作成したストレージ エンドポイントを選択します。 

3. クエリ文字列として「`true`」を入力してから **[保存]** をクリックします。

   ![IoT Hub でのルートの作成](./media/iot-hub-store-data-in-azure-table-storage/3_create-route.png)
  
### <a name="add-a-route-for-hot-path-telemetry-optional"></a>ホット パス テレメトリのルートを追加する (省略可能)

既定では、IoT Hub は、他のルートに一致しないメッセージはすべて組み込みのエンドポイントにルーティングします。 すべてのテレメトリ メッセージはメッセージをストレージにルーティングする規則に一致しているため、組み込みのエンドポイントに書き込まれるメッセージ用に、別のルートを追加する必要があります。 複数のエンドポイントにメッセージをルーティングする場合、追加の料金は発生しません。

> [!NOTE]
> テレメトリ メッセージで追加の処理を実行しない場合は、この手順をスキップできます。

1. [ルート] ペインの **[追加]** をクリックして、ルートの名前を入力します。 

2. データ ソースとして **[デバイス メッセージ]** を、エンドポイントとして **[イベント]** を選択します。 

3. クエリ文字列として「`true`」を入力してから **[保存]** をクリックします。

  ![IoT Hub でのホット パス ルートの作成](./media/iot-hub-store-data-in-azure-table-storage/4_hot-path-route.png)

## <a name="verify-your-message-in-your-storage-container"></a>ストレージ コンテナー内のメッセージの確認

1. デバイスでサンプル アプリケーションを実行し、メッセージを IoT ハブに送信します。

2. [Azure ストレージ エクスプローラーをダウンロードしてインストールします](http://storageexplorer.com/)。

3. ストレージ エクスプローラーを開いて **[Azure アカウントの追加]** > **[サインイン]** をクリックし、Azure アカウントにサインインします。

4. Azure サブスクリプション > **[ストレージ アカウント]** > ご使用のストレージ アカウント > **[BLOB コンテナー]** > ご使用のコンテナーの順にクリックします。

   デバイスから IoT ハブに送信されたメッセージが、BLOB コンテナーに記録されたことがわかります。

## <a name="clean-up-resources"></a>リソースのクリーンアップ 

このチュートリアルでは、ストレージ アカウントを追加し、ストレージ アカウントに書き込まれる IoT Hub からのメッセージのルーティングを追加しました。 作成したリソースをクリーンアップするには、ルーティング情報を削除して、ストレージ アカウントを削除します。 

1. [Azure Portal](https://portal.azure.com) にログインします。

2. **[リソース グループ]** をクリックして、使用したリソース グループを選びます。 グループ内のリソースの一覧が表示されます。 

   > [!NOTE]
   > リソース グループ内のすべてのリソースを削除する場合は、**[削除]** をクリックして、リソース グループを削除し、画面の指示に従います。 これにより、そのリソース グループ内のすべてが削除されるので、リソースのクリーンアップが完了し、次のセクションにスキップできます。

3. このチュートリアルで使用した IoT Hub をクリックします。 

4. [IoT Hub] ウィンドウで、**[ルート]** をクリックします。 追加したルーティング規則の横にあるチェック ボックスをオンにし、**[削除]** をクリックします。 そのルートを削除することを確認するように要求されたら、**[はい]** をクリックします。

   ![ルーティング規則の削除](./media/iot-hub-store-data-in-azure-table-storage/cleanup-remove-routing.png)

   ルーティング ウィンドウを閉じます。 [リソース グループ] ウィンドウに戻ります。

5. IoT Hub をもう一度クリックします。 

6. [IoT Hub] ウィンドウで、**[エンドポイント]** をクリックします。 ストレージ コンテナー用に追加したエンドポイントの横にあるチェック ボックスをクリックし、**[削除]** をクリックします。 選択したエンドポイントを削除することを確認するように要求されたら、**[はい]** をクリックします。

    ![エンドポイントの削除](./media/iot-hub-store-data-in-azure-table-storage/cleanup-remove-endpoint.png)

    [エンドポイント] ウィンドウを閉じます。 [リソース グループ] ウィンドウに戻ります。 

7.  このチュートリアル用に設定したストレージ アカウントをクリックします。 

8.  [ストレージ アカウント] ウィンドウで、**[削除]** をクリックして、ストレージ アカウントを削除します。 **[ストレージ アカウントの削除]** ウィンドウが表示されます。

   ![ストレージ アカウントの削除](./media/iot-hub-store-data-in-azure-table-storage/cleanup-remove-storageaccount.png)

8.  ストレージ アカウント名を入力し、ウィンドウの下部にある **[削除]** をクリックします。 

## <a name="next-steps"></a>次の手順

Azure ストレージ アカウントを作成し、IoT Hub からこのストレージ アカウント内の BLOB コンテナーにメッセージをルーティングしました。

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
