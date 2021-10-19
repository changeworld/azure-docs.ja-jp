---
title: Azure portal に IoT コネクタをデプロイする - Azure Healthcare API
description: この記事では、IoT コネクタを Azure portal にデプロイする方法について学習します。
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: quickstart
ms.date: 10/13/2021
ms.author: jasteppe
ms.openlocfilehash: 064c904b33317d72adbef771353a98a947438252
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2021
ms.locfileid: "130003214"
---
# <a name="deploy-iot-connector-in-the-azure-portal"></a>デバイスに IoT コネクタをデプロイAzure portal

> [!IMPORTANT]
> Azure Healthcare APIs は現在プレビュー段階です。 ベータ版、プレビュー版、または一般提供としてまだリリースされていない Azure の機能に適用されるその他の法律条項については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」に記載されています。

このクイックスタートでは、IoT コネクタをデプロイする方法について、Azure portal。 IoT コネクタを構成すると、デバイス メッセージ用の Azure Event Hub を使用して、モノのインターネット (IoT) から 高速ヘルスケア相互運用性リソース (FHIR&#174;) サービスにデータを取り込むできます。

## <a name="prerequisites"></a>前提条件

Azure Healthcare API で IoT コネクタ インスタンスを作成する手順を開始する前に、次の前提条件を満たしていることを確認することが重要です。

* [Azure アカウント](https://azure.microsoft.com/free/search/?OCID=AID2100131_SEM_c4b0772dc7df1f075552174a854fd4bc:G:s&ef_id=c4b0772dc7df1f075552174a854fd4bc:G:s&msclkid=c4b0772dc7df1f075552174a854fd4bc)
* [リソース グループがリソース グループにデプロイAzure portal](../../azure-resource-manager/management/manage-resource-groups-portal.md)
* [Event Hubsにデプロイされている名前空間とイベント ハブAzure portal](../../event-hubs/event-hubs-create.md)
* [Azure Healthcare API にデプロイされたワークスペース](../workspace-overview.md)  
* [Azure Healthcare API にデプロイされた FHIR サービス](../fhir/fhir-portal-quickstart.md) 


## <a name="deploy-iot-connector"></a>IoT コネクタをデプロイする 

1. [] ボックス [Azure portal](https://portal.azure.com)サインインし、[検索] バー フィールドに Healthcare API ワークスペースのリソース **名を** 入力します。
 
   ![検索バー フィールドにワークスペース リソース名を入力するスクリーンショット。](media/select-workspace-resource-group.png#lightbox)

2. **[IoT コネクタ] ブレードを選択** します。

   ![[IoT コネクタ] ブレードのスクリーンショット。](media/iot-connector-blade.png#lightbox)

3. 次に、[ **IoT コネクタの追加 ] を選択します**。

   ![IoT コネクタの追加のスクリーンショット。](media/add-iot-connector.png#lightbox)

## <a name="configure-iot-connector-to-ingest-data"></a>データを取り込む IoT コネクタを構成する

[基本] **タブの** [インスタンスの詳細] の下に必要なフィールド **を入力します**。

![IoT 構成インスタンスの詳細のスクリーンショット。](media/basics-instance-details.png#lightbox)

1. **IoT コネクタ名 を入力します**。

   IoT **コネクタ名は** 、IoT コネクタの表示名です。 アプリケーションの一意の名前を入力IoT Connector。 例として、 という名前を付けできます `healthdemo-iot` 。

2. イベント ハブ名 **を入力します**。

   イベント ハブ名は、デプロイEvent Hubs **インスタンス** の名前です。 

   詳細については、「クイック スタート: Azure Event Hubs[を使用してイベント ハブを作成する」を参照Azure portal。](../../event-hubs/event-hubs-create.md#create-an-event-hubs-namespace)

3. コンシューマー グループ **を入力します**。

   コンシューマー グループ名は、検索バーを使用して、デプロイした Event Hubs インスタンスに移動し、[コンシューマー グループ] ブレード **を選択することで検索** されます。

   ![コンシューマー グループ名のスクリーンショット。](media/consumer-group-name.png#lightbox)

   コンシューマー グループの詳細については、「コンシューマー グループの[機能と用語」をAzure Event Hubs。](../../event-hubs/event-hubs-features.md?WT.mc_id=Portal-Microsoft_Healthcare_APIs#event-consumers)

4. 完全修飾名前空間 の **名前を入力します**。

    完全修飾 **名前空間は、** お **使Event Hubsの** [概要] ページにある **ホスト名** です。

    ![完全修飾名前空間のスクリーンショット。](media/event-hub-hostname.png#lightbox)  

    名前空間の詳細についてはEvent Hubs [ドキュメントの「](../../event-hubs/event-hubs-features.md?WT.mc_id=Portal-Microsoft_Healthcare_APIs#namespace) 機能と用語」の「名前空間Azure Event Hubsしてください。

5. [次 **へ: デバイス マッピング] を選択します**。 
  
## <a name="configure-device-mapping-properties"></a>デバイス マッピングのプロパティを構成する

> [!TIP]
> IoMT コネクタ データ マッパーは、デバイスの入力データを正規化するマッピング構成を視覚化し、それを FHIR リソースに変換するオープン ソース ツールです。 開発者は、このツールを使用して、デバイスと FHIR 変換先マッピングを編集およびテストし、データをエクスポートして Azure portal の IoT コネクタにアップロードできます。 このツールは、開発者がデバイスの Device と FHIR の変換先マッピング構成を理解するのにも役立ちます。
>
> 詳細については、オープン ソースのドキュメントを参照してください。
>
> [IoMT コネクタ データ マッパー](https://github.com/microsoft/iomt-fhir/tree/master/tools/data-mapper)
>
> [デバイス コンテンツ マッピング](https://github.com/microsoft/iomt-fhir/blob/master/docs/Configuration.md#device-content-mapping)

1. [デバイス マッピング **] タブで** 、IoT コネクタに関連付けられているデバイス マッピング JSON コードを入力します。

   ![デバイス マッピングの構成のスクリーンショット。](media/configure-device-mapping.png#lightbox)

2. [ **次へ: 宛先>** を選択して、IoT コネクタに関連付けられている宛先プロパティを構成します。

## <a name="configure-fhir-destination-mapping-properties"></a>FHIR 変換先マッピングのプロパティを構成する

[宛先 **] タブ** で、IoT コネクタに関連付けられている宛先プロパティを入力します。

   ![[宛先プロパティの構成] のスクリーンショット。](media/configure-destination-properties.png#lightbox)

1. FHIR サービスの Azure リソース ID **を入力します**。

   **FHIR サーバー** 名 **(FHIR** サービスとも呼ばれる) は、検索バーを使用して、デプロイした FHIR サービスに移動し、[プロパティ] ブレードを選択することで **配置** されます。 リソース ID 文字列を **コピーして FHIR** Server テキスト **フィールドに貼り** 付けます。

    ![Enter FHIR サーバー名のスクリーンショット。](media/fhir-service-resource-id.png#lightbox) 

2. [宛先名] **を入力します**。

   [ **宛先名]** は、変換先の表示名です。 宛先の一意の名前を入力します。 例として、 という名前を付けできます `iotmedicdevice` 。

3. [解決 **の種類]** **で [作成** ] または [参照 **] を選択します**。

    > [!NOTE]
    > IoT コネクタの宛先が FHIR サービスに有効な監視リソースを作成するには、デバイス リソースと患者リソースが FHIR Server に存在する必要があります。そのため、データを作成したデバイスと、データの測定が行った患者を適切に参照できます。 IoT コネクタは、デバイスと患者のリソースを解決するために使用できる 2 つのモードがあります。

   **作成**

     IoT コネクタの宛先は、イベント ハブ メッセージに含まれるデバイス識別子を使用して、FHIR Server からデバイス リソースを取得します。 また、イベント ハブ メッセージに含まれる患者識別子を使用して、FHIR サーバーから患者リソースを取得することもできます。 いずれかのリソースが見つからない場合は、イベント ハブ メッセージに含まれる識別子を含む新しいリソース (デバイス、患者、または両方) が作成されます。 [作成] オプション **を使用** すると、デバイス ID と患者識別子の両方をデバイス マッピングで構成できます。 言い換えると、IoT Connectorが作成モードの場合、デバイスと患者のリソースを FHIR Server に追加せずに正常に機能できます。 

   **Lookup**

     IoT コネクタの宛先は、イベント ハブ メッセージに含まれるデバイス識別子を使用して、FHIR Server からデバイス リソースを取得します。 デバイス リソースが見つからない場合、エラーが発生し、データは処理されません。 **Lookup が** 正常に機能するには、イベント ハブ メッセージに含まれるデバイス識別子と一致する識別子を持つデバイスリソースが存在する必要があります。また、デバイス リソースにも存在する患者リソースへの参照が必要です。 つまり、IoT コネクタの宛先がルックアップ モードの場合、データを処理する前に、デバイスと患者のリソースを FHIR Server に追加する必要があります。

   詳細については、オープン ソースのドキュメント [「FHIR 変換先マッピング」を参照してください](https://github.com/microsoft/iomt-fhir/blob/master/docs/Configuration.md#fhir-mapping)。

4. [ **変換先マッピング]** で、コード エディター内に JSON コードを入力します。

   Mapper ツールの詳細については、「IoMT コネクタ データ マッパー ツール [」を参照してください](https://github.com/microsoft/iomt-fhir/tree/master/tools/data-mapper)。

5. [確認と作成 **] を選択するか**、[次へ: タグ] を **選択** >を構成する場合は [タグ] を選択します。  

## <a name="optional-configure-tags"></a>(省略可能)タグの構成

タグは、リソースの分類に使用される名前と値のペアです。 タグの詳細については、「タグを使用 [して Azure リソースと管理階層を整理する」を参照してください](../../azure-resource-manager/management/tag-resources.md)。

[タグ **] タブ** で、IoT コネクタに関連付けられているタグ プロパティを入力します。

   ![タグのプロパティのスクリーンショット。](media/tag-properties.png#lightbox)
 
1. **[名前]** を入力します。
2. 値 を入力 **します**。
3. **[Review + create]\(レビュー + 作成\)** を選択します。

   次の図に **示すように** 、検証の成功メッセージが表示されます。 

   ![検証成功メッセージのスクリーンショット。](media/iot-connector-validation-success.png#lightbox) 

   > [!NOTE]
   > IoT コネクタが検証しなかった場合は、検証エラー メッセージを確認し、問題のトラブルシューティングを行います。 構成した各 IoT コネクタ タブのプロパティを確認してください。

4. 次に、 **[作成]** を選択します。

   新しくデプロイされた IoT コネクタは、Azure リソース グループ内に表示されます。

   ![[Azure 最近使用したリソース] の一覧に一覧表示されているデプロイされた IoT コネクタのスクリーンショット。](media/azure-resources-iot-connector-deployed.png#lightbox)  

    IoT コネクタがデプロイされたので、Event Hub と FHIR サービスにアクセスするためのアクセス許可を割り当てる手順について説明します。 

## <a name="granting-iot-connector-access"></a>IoT コネクタへのアクセスを許可する

IoT コネクタが正常に動作するには、Event Hub および FHIR サービスへのアクセス許可が付与されている必要があります。 

### <a name="accessing-the-iot-connector-from-the-event-hub"></a>イベント ハブから IoT コネクタにアクセスする

1. **[Azure リソース グループ] の一** 覧で、名前空間 の名前 **Event Hubs選択します**。

2. [アクセス制御 **(IAM)] ブレードを選択し** 、[+ 追加] **を選択します**。   

   ![名前空間のアクセス制御Event Hubsスクリーンショット。](media/access-control-blade-add.png#lightbox)

3. **[ロールの割り当ての追加]** を選択します。

   ![ロールの割り当ての追加のスクリーンショット。](media/event-hub-add-role-assignment.png#lightbox)
 
4. [ロール] **を選択** し、[データ受信者 **] Azure Event Hubs選択します**。

   ![ロールの割り当て必須フィールドの追加のスクリーンショット。](media/event-hub-add-role-assignment-fields.png#lightbox)

   データAzure Event Hubsロールを使用すると、このロールが割り当てられている IoT コネクタで、このイベント ハブからデータを受信できます。

   アプリケーション ロールの詳細については、「Authentication [& Authorization for the Healthcare API (プレビュー)」を参照してください](.././authentication-authorization.md)。

5. [ **アクセス権を に割り当** てる] を選択し、既定のオプションで [ユーザー、グループ、またはサービス プリンシパル] **を選択した状態のままにします**。

6. [選択 **] フィールド** に、IoT コネクタのセキュリティ プリンシパルを入力します。  

   `<your workspace name>/iotconnectors/<your IoT connector name>`
 
   IoT コネクタをデプロイすると、マネージド ID が作成されます。 マネージド識別名は、ワークスペース名、リソースの種類 (IoT コネクタ)、および IoT コネクタの名前を連結した名前です。

7. **[保存]** を選択します。

   ロールの割り当てがイベント ハブに正常に追加されると、通知に緑色のチェック マークが表示され、"ロールの割り当ての追加" というテキストが表示されます。  このメッセージは、IoT コネクタがイベント ハブから読み取り可能な状態を示しています。

   ![追加されたロールの割り当てメッセージのスクリーンショット。](media/event-hub-added-role-assignment.png#lightbox)

リソースへのアクセスを作成する方法の詳細については、「Event Hubs を使用してアクセスを[承認する」をAzure Active Directory。](../../event-hubs/authorize-access-azure-active-directory.md)  

### <a name="accessing-the-iot-connector-from-the-fhir-service"></a>FHIR サービスから IoT コネクタにアクセスする

1. **[Azure リソース グループ] の一覧** で **、FHIR** サービスの名前を選択します。
 
2. [アクセス制御 **(IAM)] ブレードを選択し** 、[+ 追加] **を選択します**。 

3. **[ロールの割り当ての追加]** を選択します。

  ![FHIR サービスのロールの割り当てを追加するスクリーンショット。](media/fhir-service-add-role-assignment.png#lightbox)

4. [ロール] **を選択** し **、[FHIR データ ライター] を選択します**。

   FHIR データ ライター ロールは、IoT コネクタが機能するために使用する読み取りおよび書き込みアクセスを提供します。 IoT コネクタは別のリソースとしてデプロイされます。そのため、FHIR サービスは IoT コネクタから要求を受信します。 FHIR サービスが要求を行っているユーザーを知らない場合、または割り当てられたロールが割り当てられていない場合は、未承認として要求を拒否します。

   アプリケーション ロールの詳細については、「Authentication [& Authorization for the Healthcare API (プレビュー)」を参照してください](.././authentication-authorization.md)。

5. [選択 **] フィールド** に、IoT コネクタのセキュリティ プリンシパルを入力します。  

    `<your workspace name>/iotconnectors/<your IoT connector name>`

6. **[保存]** を選択します。

   ![FHIR サービスにロールの割り当てメッセージが追加されたスクリーンショット。](media/fhir-service-added-role-assignment.png#lightbox)

   FHIR サービスにロールを割り当てる方法の詳細については、「FHIR サービス用に Azure RBAC を構成 [する」を参照してください](../fhir/configure-azure-rbac-for-fhir.md)。

## <a name="next-steps"></a>次の手順

この記事では、IoT コネクタを Azure portal にデプロイする方法について学習しました。 IoT コネクタの概要については、以下を参照してください。

>[!div class="nextstepaction"]
>[IoT コネクタの概要](iot-connector-overview.md)

(FHIR&#174;) は HL7 の商標であり [、HL7](https://hl7.org/fhir/) の許可を得て使用されます。