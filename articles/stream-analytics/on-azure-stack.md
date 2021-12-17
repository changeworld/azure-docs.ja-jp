---
title: Azure Stack での Azure Stream Analytics の実行
description: Azure Stream Analytics Edge ジョブを作成し、Azure IoT Edge ランタイムを使用して Azure Stack Hub にデプロイします。
ms.service: stream-analytics
author: an-emma
ms.author: raan
ms.topic: how-to
ms.date: 03/15/2021
ms.openlocfilehash: 0e632298c616c124cdd10b7a35672661bcde2d91
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130258147"
---
# <a name="run-azure-stream-analytics-on-azure-stack"></a>Azure Stack での Azure Stream Analytics の実行

Azure Stream Analytics は Azure Stack Hub で Azure IoT Edge モジュールとして実行できます。 IoT Edge モジュールに構成を追加すると、すべての Azure Stack Hub の利用で見つかったカスタム URL が許可されるようになることで、Azure Stack Hub サブスクリプションで実行されている Azure Blob Storage、Azure Event Hubs、Azure IoT Hub と対話できるようになります。

Azure Stack で Stream Analytics を使用すると、独自のプライベートな自律クラウドでストリーム処理を行うための真のハイブリッド アーキテクチャを構築できます。 一貫性のある Azure サービスをオンプレミスで使用して、クラウドをクラウドネイティブ アプリと接続したり切断したりすることができます。 

この記事では、Azure Stack Hub サブスクリプションで IoT Hub または Event Hubs から別のイベント ハブまたは BLOB ストレージにデータをストリーム配信し、これを Stream Analytics で処理する方法について説明します。

## <a name="set-up-environments"></a>環境を設定する

Stream Analytics は Azure Stack Hub 上のハイブリッド サービスです。 これは Azure で構成されていますが Azure Stack Hub で実行できる IoT Edge モジュールです。

Azure Stack Hub または IoT Edge を初めて使用する場合は、ここでの手順に従って環境を設定します。

### <a name="prepare-the-azure-stack-hub-environment"></a>Azure Stack Hub 環境を準備する

Azure Stack Hub サブスクリプションを作成します。 詳細については、[Azure Stack Hub サブスクリプションを作成するためのチュートリアル](/azure-stack/user/azure-stack-subscribe-services/)に関するページを参照してください。

独自のサーバーで Azure Stack Hub を評価する場合は、Azure Stack Development Kit (ASDK) を使用できます。 ASDK の詳細については、[ASDK の概要](/azure-stack/asdk/)に関する記事を参照してください。

### <a name="install-the-iot-edge-runtime"></a>IoT Edge ランタイムをインストールする

Azure Stack Hub で Stream Analytics を実行するには、デバイスに IoT Edge ランタイムが必要です。また、Azure Stack Hub へのネットワーク接続があるか、Azure Stack Hub で実行されている VM である必要があります。 IoT Edge ランタイムを使用すると、Stream Analytics Edge ジョブを、Azure Stack Hub で実行されている Azure Storage および Azure Event Hubs と統合できます。 詳細については、「[Azure Stream Analytics on IoT Edge](stream-analytics-edge.md)」をご覧ください。 

IoT Edge デバイスまたは VM では、Azure Stack Hub リソースへのネットワーク アクセスを持つだけでなく、Stream Analytics モジュールを構成するために、Azure パブリック クラウド内の IoT Hub にアクセスする必要があります。 

次の記事では、デバイスまたは VM で IoT Edge ランタイムを設定する方法について説明します。

* [Windows に Azure IoT Edge ランタイムをインストールする](../iot-edge/how-to-provision-single-device-linux-on-windows-symmetric.md)
* [Debian ベースの Linux システムに Azure IoT Edge ランタイムをインストールする](../iot-edge/how-to-provision-single-device-linux-symmetric.md)


## <a name="create-an-azure-stream-analytics-edge-job"></a>Azure Stream Analytics Edge ジョブを作成する

Stream Analytics Edge ジョブは、IoT Edge デバイスにデプロイされたコンテナー内で実行されます。 これは 2 つの部分で構成されます。
* ジョブ定義の役割を担うクラウド部分: ユーザーが、クラウド内の入力、出力、クエリ、その他の設定 (順不同のイベントなど) を定義します。
* IoT デバイス上で実行されているモジュール。 Stream Analytics エンジンを含んでおり、クラウドからジョブ定義を受け取ります。

### <a name="create-a-storage-account"></a>ストレージ アカウントを作成する

Stream Analytics ジョブを作成して IoT Edge デバイスで実行する場合は、デバイスから呼び出すことができるようにジョブを格納する必要があります。 既存の Azure ストレージ アカウントを使用するか、新しいストレージ アカウントを作成することができます。
1. Azure portal で、 **[リソースの作成]**  >  **[Storage]**  >  **[ストレージ アカウント - Blob、File、Table、Queue]** の順に選択します。
1. ストレージ アカウントを作成するには、下の値を指定します。

   | フィールド | 値 |
   | --- | --- |
   | 名前 | ストレージ アカウント用に一意の名前を指定します。 |
   | 場所 | 近くの場所を選択します。|
   | サブスクリプション | IoT ハブと同じサブスクリプションを選択します。|
   | リソース グループ | [IoT Edge のクイックスタート](../iot-edge/quickstart.md)およびチュートリアルで作成するすべてのテスト リソースに、同じリソース グループを使用します。 たとえば、**IoTEdgeResources** を使用します。 |

1. 他のフィールドは既定値のままにして、 **[作成]** を選択します。


### <a name="create-a-new-job"></a>新しいジョブを作成する

1. Azure portal で、 **[リソースの作成]**  >  **[モノのインターネット (IoT)]**  >  **[Stream Analytics ジョブ]** の順に移動します。
1. ストレージ アカウントを作成するには、下の値を指定します。

   | フィールド | 値 |
   | --- | --- |
   | ジョブ名 | ジョブの名前を指定します。 たとえば、**IoTEdgeJob** とします。 |
   | サブスクリプション | IoT ハブと同じサブスクリプションを選択します。|
   | リソース グループ | [IoT Edge のクイックスタート](../iot-edge/quickstart.md)およびチュートリアルで作成するすべてのテスト リソースに、同じリソース グループを使用します。 たとえば、**IoTEdgeResources** を使用します。 |
   | 場所 | 近くの場所を選択します。 |
   | ホスト環境 | **[Edge]** を選択します。 |

1. **［作成］** を選択します

### <a name="configure-your-job"></a>ジョブを構成する

Azure portal で Stream Analytics ジョブを作成したら、入力、出力、通過するデータに対して実行するクエリを構成します。 Azure Stack Hub サブスクリプションで、IoT ハブまたはイベント ハブからの入力を手動で指定できます。

1. Azure portal で Stream Analytics ジョブに移動します。
1. **[構成]** で、 **[ストレージ アカウントの設定]** を選択し、前の手順で作成したストレージ アカウントを選択します。

   :::image type="content" source="media/on-azure-stack/storage-account-settings.png" alt-text="ジョブ ストレージ アカウントの設定を示すスクリーンショット。" lightbox="media/on-azure-stack/storage-account-settings.png":::

1. **[ジョブ トポロジ]** で、 **[入力]**  >  **[ストリーム入力の追加]** の順に選択します。
1. ドロップダウン リストから **[IoT ハブ]** 、 **[イベント ハブ]** 、または **[Edge ハブ]** を選択します。 
1. 入力が Azure Stack Hub サブスクリプションのイベント ハブまたは IoT ハブである場合は、次に示すように情報を手動で入力します。

   #### <a name="event-hub"></a>イベント ハブ

   | フィールド | 値 |
   | --- | --- |
   | 入力のエイリアス | この入力を参照するジョブのクエリで使用するわかりやすい名前。 |
   | Service Bus 名前空間 | 名前空間は、一連のメッセージング エンティティのコンテナーです。 新しいイベント ハブを作成するときは、名前空間も作成します。 たとえば `sb://<Event Hub Name>.eventhub.shanghai.azurestack.corp.microsoft.com` です。 |
   | イベント ハブ名 | 入力として使用するイベント ハブの名前。 |
   | イベント ハブ ポリシー名 | イベント ハブへのアクセスを提供する共有アクセス ポリシー。 各共有アクセス ポリシーには、名前、設定したアクセス許可、アクセス キーが含まれています。 イベント ハブの設定を手動で入力するオプションを選択しない限り、このオプションは自動的に設定されます。 |
   | イベント ハブ ポリシー キー | イベント ハブへのアクセスを認可するために使用する共有アクセス キー。 イベント ハブの設定を手動で入力するオプションを選択しない限り、このオプションは自動的に設定されます。 これはイベント ハブの設定で見つけることができます。 |
   | イベント ハブ コンシューマー グループ (省略可能) | Stream Analytics ジョブごとに個別のコンシューマー グループを使用します。 イベント ハブからデータを取り込むために使用するコンシューマー グループが、この文字列によって識別されます。 コンシューマー グループが指定されていない場合、Stream Analytics ジョブは $Default コンシューマー グループを使用します。 |
   | [パーティション数] | パーティション数はイベント ハブ内のパーティションの数です。 |

   :::image type="content" source="media/on-azure-stack/event-hub-input.png" alt-text="イベント ハブの入力を示すスクリーンショット。" lightbox="media/on-azure-stack/event-hub-input.png":::
   
   #### <a name="iot-hub"></a>IoT ハブ

   | フィールド | 値 |
   | --- | --- |
   | 入力のエイリアス | この入力を参照するジョブのクエリで使用するわかりやすい名前。 |
   | IoT Hub | 入力として使用する IoT Hub の名前。 たとえば `<IoT Hub Name>.shanghai.azurestack.corp.microsoft.com` です。 |
   | 共有アクセス ポリシー名 | IoT Hub へのアクセスを提供する共有アクセス ポリシー。 各共有アクセス ポリシーには、名前、設定したアクセス許可、アクセス キーが含まれています。 |
   | 共有アクセス ポリシー キー | IoT Hub へのアクセスを承認するために使用する共有アクセス キー。 IoT ハブの設定を手動で入力するオプションを選択しない限り、このオプションは自動的に事前設定されます。 |
   | コンシューマー グループ (省略可能) | Stream Analytics ジョブごとに異なるコンシューマー グループを使用します。 IoT ハブからのデータを取り込むために使用するコンシューマー グループです。 明示的に指定されない限り、Stream Analytics は $Default コンシューマー グループを使用します。 |
   | [パーティション数] | パーティション数はイベント ハブ内のパーティションの数です。 |

   :::image type="content" source="media/on-azure-stack/iot-hub-input.png" alt-text="IoT Hub の入力を示すスクリーンショット。" lightbox="media/on-azure-stack/iot-hub-input.png" :::

1. 他のフィールドは既定値のままにして、**[保存]** を選択します。
1. **[ジョブ トポロジ]** で **[出力]** を開き、 **[追加]** を選択します。
1. ドロップダウン リストから **[Blob ストレージ]** 、 **[イベント ハブ]** 、または **[Edge ハブ]** を選択します。
1. 出力が Azure Stack Hub サブスクリプションのイベント ハブまたは BLOB ストレージである場合は、次に示すように情報を手動で入力します。

   #### <a name="event-hub"></a>イベント ハブ

   | フィールド | 値 |
   | --- | --- |
   | 出力エイリアス | クエリの出力をこのイベント ハブに出力するためにクエリ内で使用されるフレンドリ名です。 |
   | Service Bus 名前空間 | 一連のメッセージング エンティティのコンテナーです。 新しいイベント ハブを作成したとき、Service Bus 名前空間も作成されました。 たとえば `sb://<Event Hub Name>.eventhub.shanghai.azurestack.corp.microsoft.com` です。 |
   | イベント ハブ名 | ご自分のイベント ハブ出力の名前です。 |
   | イベント ハブ ポリシー名 | 共有アクセス ポリシーです。イベント ハブの **[構成]** タブで作成できます。各共有アクセス ポリシーには、名前、設定したアクセス許可、アクセス キーが含まれています。 |
   | イベント ハブ ポリシー キー | イベント ハブの名前空間へのアクセスを認証するために使用される共有アクセス キーです。 |

   :::image type="content" source="media/on-azure-stack/event-hub-output.png" lightbox="media/on-azure-stack/event-hub-output.png" alt-text="イベント ハブの出力を示すスクリーンショット。":::
   #### <a name="blob-storage"></a>Blob Storage 

   | フィールド | 値 |
   | --- | --- |
   | 出力エイリアス | クエリの出力をこの BLOB ストレージに出力するためにクエリで使用されるわかりやすい名前です。 |
   | ストレージ アカウント | ご自分の出力を送信するストレージ アカウントの名前です。 たとえば `<Storage Account Name>.blob.shanghai.azurestack.corp.microsoft.com` です。 |
   | ストレージ アカウント キー | ストレージ アカウントに関連付けられている秘密キー。 Blob ストレージの設定を手動で入力するオプションを選択しない限り、このオプションは自動的に事前設定されます。 |

> [!NOTE]
> Parquet 形式は Azure Stack Hub の Edge ジョブではサポートされていません。 **最小** 行数と **最大** 時間については、**0** を使用するか、空白のままにします。


## <a name="deploy-stream-analytics-on-a-vm-or-device-connected-to-azure-stack"></a>Azure Stack に接続されている VM またはデバイスに Stream Analytics を展開する

1. Azure portal で IoT Hub を開きます。 **[IoT Edge]** に移動して、このデプロイの対象となるデバイスまたは VM を選択します。
1. **[モジュールの設定]**  >  **[+ 追加]** を選択し、 **[Azure Stream Analytics モジュール]** を選択します。 
1. サブスクリプションと、作成した Stream Analytics Edge ジョブを選択します。 **[保存]** を選択して、 **[次: ルート]** を選択します。

   :::image type="content" source="media/on-azure-stack/edge-modules.png" lightbox="media/on-azure-stack/edge-modules.png" alt-text="モジュールの追加を示すスクリーンショット。":::

1. **[確認および作成 >]** を選択します。
1. **[確認および作成]** ステップで、 **[作成]** を選択します。

   :::image type="content" source="media/on-azure-stack/module-content.png" lightbox="media/on-azure-stack/module-content.png" alt-text="マニフェストを示すスクリーンショット。":::

1. モジュールが一覧に追加されていることを確認します。

   :::image type="content" source="media/on-azure-stack/edge-deployment.png" lightbox="media/on-azure-stack/edge-deployment.png" alt-text="[デプロイ] ページを示すスクリーンショット。":::
## <a name="next-steps"></a>次のステップ
- [Azure Stream Analytics on IoT Edge](./stream-analytics-edge.md)
- [Stream Analytics Edge ジョブを開発する](/stream-analytics-query/stream-analytics-query-language-reference)
