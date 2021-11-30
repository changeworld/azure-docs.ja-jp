---
title: IoT コネクタの概要-Azure の医療 Api
description: このドキュメントでは、Azure の医療 Api で IoT コネクタの使用を開始する方法について説明します。
author: stevewohl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: quickstart
ms.date: 11/24/2021
ms.author: zxue
ms.openlocfilehash: 65080a0d884d6388f713ea80f4447f4c8d9fba7b
ms.sourcegitcommit: dcf3424d7149fceaea0340eb0657baa2c27882a5
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/30/2021
ms.locfileid: "133273179"
---
# <a name="get-started-with-the-iot-connector"></a>IOT コネクタを使用したはじめに

この記事では、 [Azure の医療 api](../healthcare-apis-overview.md)で IoT コネクタを使用するための基本的な手順について説明します。 

前提条件として、Azure サブスクリプションが必要であり、Azure リソースグループを作成して Azure リソースをデプロイするための適切なアクセス許可が付与されている必要があります。 すべての手順を実行するか、既存の環境がある場合はスキップすることができます。 また、すべての手順を組み合わせて、PowerShell、Azure CLI、REST API スクリプトで完了することもできます。

[![DICOM でのはじめに](media/get-started-with-iot.png)](media/get-started-with-iot.png#lightbox)

## <a name="create-a-workspace-in-your-azure-subscription"></a>Azure サブスクリプションでワークスペースを作成する

[Azure portal](../healthcare-apis-quickstart.md)から、または PowerShell、Azure CLI、Rest API] を使用して、ワークスペースを作成できます。 スクリプトは、 [医療 api サンプル](https://github.com/microsoft/healthcare-apis-samples/tree/main/src/scripts)から入手できます。

> [!NOTE]
> 各 Azure サブスクリプションで作成できるワークスペースの数と IoT コネクタインスタンスの数には制限があります。

## <a name="create-the-fhir-service-and-an-event-hub"></a>FHIR サービスとイベントハブの作成

IoT コネクタは、Azure Event Hub および FHIR サービスと連携して動作します。 新しい [Fhir サービス](../fhir/get-started-with-fhir.md) を作成することも、同じワークスペースまたは別のワークスペースにある既存のサービスを使用することもできます。 同様に、新しいイベントハブを作成することも、既存の [イベントハブ](../../event-hubs/event-hubs-create.md) を使用することもできます。


## <a name="create-a-iot-connector-in-the-workspace"></a>ワークスペースでの IoT コネクタの作成

IoT コネクタは、 [Azure portal](deploy-iot-connector-in-azure.md) から、または PowerShell、Azure CLI、または REST API を使用して作成できます。 スクリプトは、 [医療 api サンプル](https://github.com/microsoft/healthcare-apis-samples/tree/main/src/scripts)から入手できます。

必要に応じて、ワークスペースで [Fhir サービス](../fhir/fhir-portal-quickstart.md) と [DICOM サービス](../dicom/deploy-dicom-services-in-azure.md) を作成できます。

## <a name="assign-roles-to-allow-iot-to-access-event-hub"></a>IoT が Event Hub にアクセスできるようにロールを割り当てる

仕様により、IoT コネクタは、システム管理の id を使用して、指定されたイベントハブからデータを取得します。 [イベントハブ](../../healthcare-apis/iot/deploy-iot-connector-in-azure.md#granting-iot-connector-access)から IoT コネクタにロールを割り当てる方法の詳細については、「」を参照してください。

## <a name="assign-roles-to-allow-iot-to-access-fhir-service"></a>IoT が FHIR サービスにアクセスできるようにロールを割り当てる

IoT コネクタは、システムによって管理される id を使用して、データを FHIR ストアに永続化します。 [Fhir サービス](../../healthcare-apis/iot/deploy-iot-connector-in-azure.md#accessing-the-iot-connector-from-the-fhir-service)から IoT コネクタにロールを割り当てる方法の詳細については、こちらを参照してください。

## <a name="sending-data-to-the-iot-connector"></a>IoT コネクタへのデータの送信

IoT コネクタに関連付けられているイベントハブにデータを送信できます。 FHIR ストアにデータが表示されない場合は、IoT コネクタのマッピングとロールの割り当てを確認します。

## <a name="iot-connector-mappings-data-flow-ml-power-bi-and-teams-notifications"></a>IoT コネクタのマッピング、データフロー、ML、Power BI、および Teams 通知

iot コネクタのマッピング、データフロー、machine learning サービス、Power BI、Teams 通知の詳細については、 [iot コネクタ](iot-connector-overview.md)のドキュメントを参照してください。

## <a name="next-steps"></a>次のステップ

この記事では、IoT コネクタの使用を開始するための基本的な手順について説明しました。 ワークスペースへの IoT コネクタのデプロイの詳細については、「」を参照してください。

>[!div class="nextstepaction"]
>[Azure portal に IoT コネクタをデプロイする](deploy-iot-connector-in-azure.md)