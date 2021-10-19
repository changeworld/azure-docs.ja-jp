---
title: Azure Healthcare API 内に FHIR サービスをデプロイする
description: この記事では、ユーザーが FHIR サービスをデプロイする方法についてAzure portal。
author: stevewohl
ms.service: healthcare-apis
ms.topic: quickstart
ms.date: 09/10/2021
ms.author: ginle
ms.openlocfilehash: 5f65355ab77547c8587cd228378868b56b193c9b
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2021
ms.locfileid: "124782527"
---
# <a name="deploy-a-fhir-service-within-azure-healthcare-apis---using-portal"></a>Azure Healthcare API 内に FHIR サービスをデプロイする - ポータルを使用する

> [!IMPORTANT]
> Azure Healthcare APIs は現在プレビュー段階です。 ベータ版、プレビュー版、または一般提供としてまだリリースされていない Azure の機能に適用されるその他の法律条項については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」に記載されています。

この記事では、Azure Healthcare API (ここでは FHIR サービスと呼ばれる) 内に、Azure portal を使用して FHIR サービスをデプロイする方法について説明します。

## <a name="prerequisite"></a>前提条件

開始する前に、Azure Healthcare API を既にデプロイしている必要があります。 Azure Healthcare API のデプロイの詳細については、「Azure Healthcare API のデプロイ」を参照[Azure portal。](../healthcare-apis-quickstart.md)

## <a name="create-a-new-fhir-service"></a>新しい FHIR サービスを作成する

ワークスペースから **[Deploy FHIR Services]を選択します**。

[![FHIR サービスをデプロイする ](media/fhir-service/deploy-fhir-services.png) ](media/fhir-service/deploy-fhir-services.png#lightbox)

[+ **Add FHIR Service]を選択します**。

[![FHIR サービスを追加する ](media/fhir-service/add-fhir-service.png) ](media/fhir-service/add-fhir-service.png#lightbox)

FHIR **サービスのアカウント** 名を入力します。 **FHIR** バージョン (**STU3** または **R4**) を選択し、 [確認および作成 **] を選択します**。

[![FHIR サービスを作成する ](media/fhir-service/create-fhir-service.png) ](media/fhir-service/create-fhir-service.png#lightbox)

[作成] **を選択する** 前に、FHIR **サービスの** [基本] **と** [追加設定] のプロパティを確認します。 戻って変更を加える必要がある場合は、 [前へ] を **選択します**。 [検証の成功 **] メッセージが** 表示されます。 

[![FHIR サービスを検証する ](media/fhir-service/validation-fhir-service.png) ](media/fhir-service/validation-fhir-service.png#lightbox)

## <a name="additional-settings-optional"></a>追加の設定 (オプション)

[追加設定] タブ **を選択して** 、認証設定を表示することもできます。 Azure API for FHIR の既定の構成では、**データ プレーン ロールの割り当てに Azure RBAC が使用されます**。 このモードで構成されている場合、FHIR サービスの "機関" は、サブスクリプションの Azure Active Directory テナントに設定されます。

[![追加設定 FHIR サービス ](media/fhir-service/additional-settings-tab.png) ](media/fhir-service/additional-settings-tab.png#lightbox)

[許可されたオブジェクトの **ID] を** 入力するボックスがグレー表示されているのに注意してください。これは、この場合のロールの割り当てを構成するために Azure RBAC を使用する理由です。

外部またはセカンダリの Azure Active Directory テナントを使用するように FHIR サービスを構成する場合は、オーソリティを変更し、サーバーへのアクセスを許可するユーザーおよびグループのオブジェクト ID を入力します。

## <a name="fetch-fhir-api-capability-statement"></a>FHIR API の機能ステートメントをフェッチする

新しい FHIR API アカウントがプロビジョニングされているのを検証するには、 を参照して機能ステートメントをフェッチします `https://<WORKSPACE NAME>-<ACCOUNT-NAME>.fhir.azurehealthcareapis.com/metadata` 。

## <a name="next-steps"></a>次の手順

>[!div class="nextstepaction"]
>[Postman を使用して FHIR サービスにアクセスする](../use-postman.md)

