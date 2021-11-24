---
title: Azure portal での IoT コネクタのデプロイ-Azure の医療 Api
description: この記事では、Azure portal に IoT コネクタをデプロイする方法について説明します。
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: quickstart
ms.date: 11/10/2021
ms.author: jasteppe
ms.custom: mode-portal
ms.openlocfilehash: 8323d2026bc45c3c357ad0055cc7da37c5a345b8
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/24/2021
ms.locfileid: "133061596"
---
# <a name="deploy-iot-connector-in-the-azure-portal"></a>Azure portal に IoT コネクタをデプロイする

> [!IMPORTANT]
> Azure Healthcare APIs は現在プレビュー段階です。 ベータ版、プレビュー版、または一般提供としてまだリリースされていない Azure の機能に適用されるその他の法律条項については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」に記載されています。

このクイックスタートでは、Azure portal に IoT コネクタをデプロイする方法について説明します。 IoT コネクタを構成すると、デバイスメッセージ用の Azure Event Hub を使用して、モノのインターネット (IoT) から高速医療相互運用性リソース (FHIR&#174;) サービスにデータを取り込むことができます。

## <a name="prerequisites"></a>必須コンポーネント

Azure の医療 Api で IoT コネクタインスタンスを作成する手順を開始する前に、次の前提条件を完了しておくことが重要です。

* [Azure アカウント](https://azure.microsoft.com/free/search/?OCID=AID2100131_SEM_c4b0772dc7df1f075552174a854fd4bc:G:s&ef_id=c4b0772dc7df1f075552174a854fd4bc:G:s&msclkid=c4b0772dc7df1f075552174a854fd4bc)
* [Azure portal にデプロイされたリソースグループ](../../azure-resource-manager/management/manage-resource-groups-portal.md)
* [Azure portal にデプロイされた Event Hubs 名前空間とイベントハブ](../../event-hubs/event-hubs-create.md)
* [Azure の医療 Api にデプロイされたワークスペース](../healthcare-apis-quickstart.md)  
* [Azure の医療 Api にデプロイされた FHIR サービス](../fhir/fhir-portal-quickstart.md) 

## <a name="deploy-iot-connector"></a>IoT コネクタのデプロイ 

1. [Azure portal](https://portal.azure.com)にサインインし、[**検索** バー] フィールドに、医療 api ワークスペースのリソース名を入力します。
 
   ![[検索バー] フィールドにワークスペースリソース名を入力したスクリーンショット。](media/select-workspace-resource-group.png#lightbox)

2. [ **IoT コネクタのデプロイ**] を選択します。

   ![IoT コネクタブレードのスクリーンショット。](media/iot-connector-blade.png#lightbox)

3. 次に、[ **IoT コネクタの追加**] を選択します。

   ![IoT コネクタの追加のスクリーンショット。](media/add-iot-connector.png#lightbox)

## <a name="configure-iot-connector-to-ingest-data"></a>データを取り込むように IoT コネクタを構成する

[ **基本** ] タブで、[ **インスタンスの詳細**] の下の必須フィールドに入力します。

![IoT 構成インスタンスの詳細のスクリーンショット。](media/basics-instance-details.png#lightbox)

1. **IoT コネクタの名前** を入力します。

   **Iot コネクタ名** は、iot コネクタのフレンドリ名です。 IoT Connector の一意の名前を入力します。 たとえば、という名前を付け `healthdemo-iot` ます。

2. **イベントハブ名** を入力します。

   イベントハブ名は、デプロイした **Event Hubs インスタンス** の名前です。 

   Azure Event Hubs の詳細については、「 [クイックスタート: Azure portal を使用してイベントハブを作成](../../event-hubs/event-hubs-create.md#create-an-event-hubs-namespace)する」を参照してください。

3. **コンシューマーグループ** を入力します。

   コンシューマーグループ名を検索するには、 **検索** バーを使用して、デプロイした Event Hubs インスタンスにアクセスし、[  **コンシューマーグループ** ] ブレードを選択します。

   ![コンシューマーグループ名のスクリーンショット。](media/consumer-group-name.png#lightbox)

   コンシューマーグループの詳細については、「 [Azure Event Hubs の機能と用語](../../event-hubs/event-hubs-features.md?WT.mc_id=Portal-Microsoft_Healthcare_APIs#event-consumers)」を参照してください。

4. **完全修飾名前空間** の名前を入力します。

    **完全修飾名前空間** は、Event Hubs 名前空間の [**概要**] ページに配置されている **ホスト名** です。

    ![完全修飾名前空間のスクリーンショット。](media/event-hub-hostname.png#lightbox)  

    Event Hubs 名前空間の詳細については、「Azure Event Hubs の機能と用語」ドキュメントの「 [名前空間](../../event-hubs/event-hubs-features.md?WT.mc_id=Portal-Microsoft_Healthcare_APIs#namespace) 」を参照してください。

5. [ **次へ]: [デバイスマッピング**] を選択します。 
  
## <a name="configure-device-mapping-properties"></a>デバイスマッピングのプロパティを構成する

> [!TIP]
> IoMT コネクタデータマッパーは、デバイスの入力データを正規化するためのマッピング構成を視覚化し、それを FHIR リソースに変換するためのオープンソースツールです。 開発者はこのツールを使用して、デバイスと FHIR の宛先マッピングを編集およびテストしたり、データをエクスポートして Azure portal の IoT コネクタにアップロードしたりすることができます。 また、このツールを使用すると、開発者はデバイスのデバイスと FHIR の送信先のマッピング構成を理解することができます。
>
> 詳細については、オープンソースのドキュメントを参照してください。
>
> [IoMT コネクタデータマッパー](https://github.com/microsoft/iomt-fhir/tree/master/tools/data-mapper)
>
> [デバイスコンテンツのマッピング](https://github.com/microsoft/iomt-fhir/blob/master/docs/Configuration.md#device-content-mapping)

1. [ **デバイスマッピング** ] タブで、IoT コネクタに関連付けられているデバイスマッピング JSON コードを入力します。

   ![デバイスマッピングの構成のスクリーンショット。](media/configure-device-mapping.png#lightbox)

2. [ **次へ** ] を選択して、IoT コネクタに関連付けられている接続先のプロパティを構成 >ます。

## <a name="configure-fhir-destination-mapping-properties"></a>FHIR 変換先マッピングのプロパティを構成する

[ **宛先** ] タブで、IoT コネクタに関連付けられている変換先プロパティを入力します。

   ![変換先プロパティの構成のスクリーンショット。](media/configure-destination-properties.png#lightbox)

1. **Fhir サービス** の AZURE リソース ID を入力します。

   **Fhir サーバー** 名 ( **fhir サービス** とも呼ばれます) を検索するには、**検索** バーを使用して、デプロイした fhir サービスにアクセスし、[**プロパティ**] ブレードを選択します。 **リソース ID** 文字列をコピーし、 **Fhir Server** テキストフィールドに貼り付けます。

    ![Enter FHIR サーバー名のスクリーンショット。](media/fhir-service-resource-id.png#lightbox) 

2. 宛先の **名前** を入力します。

   コピー先の **名前** は、変換先のフレンドリ名です。 宛先の一意の名前を入力します。 たとえば、という名前を付け `iotmedicdevice` ます。

3. **解決の種類** として [**作成**] または [**参照**] を選択します。

    > [!NOTE]
    > IoT コネクタの移行先で、FHIR サービスで有効な監視リソースを作成するには、デバイスリソースと患者リソースが FHIR サーバーに存在して **いる必要があり** ます。これにより、データを作成したデバイスとデータの測定元の患者を監視で正しく参照できるようになります。 IoT コネクタでは、デバイスと患者のリソースを解決するために使用できる2つのモードがあります。

   **作成**

     IoT コネクタの宛先は、イベントハブメッセージに含まれているデバイス識別子を使用して、FHIR サーバーからデバイスリソースを取得しようとします。 また、イベントハブメッセージに含まれる患者の識別子を使用して、FHIR サーバーから患者のリソースを取得しようとします。 いずれかのリソースが見つからない場合は、イベントハブメッセージに含まれている識別子だけを含む新しいリソースが作成されます (デバイス、患者、またはその両方)。 [ **作成** ] オプションを使用する場合、デバイス id と患者 id の両方をデバイスマッピングで構成できます。 つまり、IoT Connector の宛先が **作成** モードのときは、デバイスや患者のリソースを fhir サーバーに追加すること **なく** 通常どおり機能できます。

   **Lookup**

     IoT コネクタの宛先は、イベントハブメッセージに含まれているデバイス識別子を使用して、FHIR サーバーからデバイスリソースを取得しようとします。 デバイスリソースが見つからない場合、エラーが発生し、データは処理されません。 **Lookup** を正しく機能させるには、イベントハブメッセージに含まれるデバイス id と一致する識別子を持つデバイスリソースが存在し、デバイスリソースにも存在する患者リソースへの参照 **が含まれている必要****が** あります。 言い換えると、IoT コネクタの宛先が参照モードの場合、データを処理する前に、デバイスと患者のリソースを FHIR サーバーに追加する **必要があり** ます。

   詳細については、オープンソースのドキュメント「 [Fhir 変換先のマッピング](https://github.com/microsoft/iomt-fhir/blob/master/docs/Configuration.md#fhir-mapping)」を参照してください。

4. [ **送信先マッピング**] で、コードエディター内に JSON コードを入力します。

   マッパーツールの詳細については、「 [IoMT Connector Data マッパー tool](https://github.com/microsoft/iomt-fhir/tree/master/tools/data-mapper)」を参照してください。

5. [ **レビュー + 作成**] を選択するか、[ **次へ: タグ** ] を選択してタグを構成することができ >ます。  

## <a name="optional-configure-tags"></a>Optionalタグを構成する

タグは、リソースの分類に使用される名前と値のペアです。 タグの詳細については、「 [タグを使用した Azure リソースと管理階層の整理」を](../../azure-resource-manager/management/tag-resources.md)参照してください。

[ **タグ** ] タブで、IoT コネクタに関連付けられているタグのプロパティを入力します。

   ![タグのプロパティのスクリーンショット。](media/tag-properties.png#lightbox)
 
1. **[名前]** を入力します。
2. **値** を入力してください。
3. **[Review + create]\(レビュー + 作成\)** を選択します。

   次の図に示すような **検証成功** メッセージが表示されます。 

   ![検証成功メッセージのスクリーンショット。](media/iot-connector-validation-success.png#lightbox) 

   > [!NOTE]
   > IoT コネクタが検証されなかった場合は、検証エラーメッセージを確認し、問題のトラブルシューティングを行います。 構成した各 IoT コネクタタブのプロパティを確認することをお勧めします。

4. 次に、 **[作成]** を選択します。

   新しくデプロイされた IoT コネクタは、Azure リソースグループのページ内に表示されます。

   ![Azure 最近使用したリソースの一覧に表示されているデプロイ済み IoT コネクタのスクリーンショット。](media/azure-resources-iot-connector-deployed.png#lightbox)  

    IoT コネクタがデプロイされたので、イベントハブおよび FHIR サービスにアクセスするためのアクセス許可を割り当てる手順について説明します。 

## <a name="granting-iot-connector-access"></a>IoT コネクタへのアクセスを許可する

IoT コネクタが正常に動作するようにするには、Event Hub および FHIR サービスへのアクセス許可が付与されている必要があります。 

### <a name="accessing-the-iot-connector-from-the-event-hub"></a>Event Hub から IoT コネクタへのアクセス

1. [ **Azure リソースグループ** ] ボックスの一覧で、 **Event Hubs 名前空間** の名前を選択します。

2. [ **アクセス制御 (IAM)** ] ブレードを選択し、[ **+ 追加**] を選択します。   

   ![Event Hubs 名前空間のアクセス制御のスクリーンショット。](media/access-control-blade-add.png#lightbox)

3. **[ロールの割り当ての追加]** を選択します。

   ![ロールの割り当ての追加のスクリーンショット。](media/event-hub-add-role-assignment.png#lightbox)
 
4. **ロール** を選択し、[ **Azure Event Hubs データレシーバー**] を選択します。

   ![[ロールの割り当てを追加する必要があります。](media/event-hub-add-role-assignment-fields.png#lightbox)

   Azure Event Hubs データ受信者ロールを使用すると、このロールが割り当てられている IoT コネクタは、このイベントハブからデータを受信できます。

   アプリケーションロールの詳細については、「 [医療 api (プレビュー) の認証 & 承認](.././authentication-authorization.md)」を参照してください。

5. [ **アクセスの割り当て先**] を選択し、既定のオプション [ **ユーザー、グループ、またはサービスプリンシパル**] をそのまま使用します。

6. **[選択**] フィールドに、IoT コネクタのセキュリティプリンシパルを入力します。  

   `<your workspace name>/iotconnectors/<your IoT connector name>`
 
   IoT コネクタをデプロイすると、管理対象 id が作成されます。 管理された識別名は、ワークスペース名、リソースの種類 (IoT コネクタ)、および IoT コネクタの名前を連結したものです。

7. **[保存]** を選択します。

   ロールの割り当てがイベントハブに正常に追加されると、"ロールの割り当てを追加" というテキストを含む緑色のチェックマークが表示されます。  このメッセージは、IoT コネクタがイベントハブから読み取ることができるようになったことを示します。

   ![追加されたロールの割り当てメッセージのスクリーンショット。](media/event-hub-added-role-assignment.png#lightbox)

Event Hubs リソースへの作成アクセスの詳細については、「 [Azure Active Directory を使用したアクセスの承認](../../event-hubs/authorize-access-azure-active-directory.md)」を参照してください。  

### <a name="accessing-the-iot-connector-from-the-fhir-service"></a>FHIR サービスから IoT コネクタへのアクセス

1. [ **Azure リソースグループ] ボックスの一覧** で、  **fhir サービス** の名前を選択します。
 
2. [ **アクセス制御 (IAM)** ] ブレードを選択し、[ **+ 追加**] を選択します。 

3. **[ロールの割り当ての追加]** を選択します。

  ![FHIR サービスのロール割り当ての追加のスクリーンショット。](media/fhir-service-add-role-assignment.png#lightbox)

4. **ロール** を選択し、[ **Fhir データライター**] を選択します。

   FHIR データライターロールは、IoT コネクタが機能するために使用する読み取りおよび書き込みアクセス権を提供します。 IoT コネクタは個別のリソースとしてデプロイされるため、FHIR サービスは IoT コネクタから要求を受信します。 FHIR サービスが要求を行っているユーザーを認識しない場合、または割り当てられたロールがない場合は、要求を承認されていないとして拒否します。

   アプリケーションロールの詳細については、「 [医療 api (プレビュー) の認証 & 承認](.././authentication-authorization.md)」を参照してください。

5. **[選択**] フィールドに、IoT コネクタのセキュリティプリンシパルを入力します。  

    `<your workspace name>/iotconnectors/<your IoT connector name>`

6. **[保存]** を選択します。

   ![FHIR サービスによって追加されたロールの割り当てメッセージのスクリーンショット。](media/fhir-service-added-role-assignment.png#lightbox)

   FHIR サービスにロールを割り当てる方法の詳細については、「 [Configure AZURE RBAC](.././configure-azure-rbac.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

この記事では、Azure portal に IoT コネクタをデプロイする方法を学習しました。 IoT コネクタの概要については、「」を参照してください。

>[!div class="nextstepaction"]
>[IoT コネクタの概要](iot-connector-overview.md)

(FHIR&#174;) [HL7](https://hl7.org/fhir/) の登録商標であり、HL7 のアクセス許可と共に使用されます。
