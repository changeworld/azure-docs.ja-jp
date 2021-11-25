---
title: Azure の医療 Api 内での FHIR サービスのデプロイ
description: この記事では、Azure portal で FHIR サービスをデプロイする方法について説明します。
author: stevewohl
ms.service: healthcare-apis
ms.topic: quickstart
ms.date: 09/10/2021
ms.author: ginle
ms.custom: mode-other
ms.openlocfilehash: 659fb82e35cd25622158d05a88d85a16e736c770
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/24/2021
ms.locfileid: "133069707"
---
# <a name="deploy-a-fhir-service-within-azure-healthcare-apis---using-portal"></a>Azure の医療 Api 内での FHIR サービスのデプロイ-ポータルの使用

> [!IMPORTANT]
> Azure Healthcare APIs は現在プレビュー段階です。 ベータ版、プレビュー版、または一般提供としてまだリリースされていない Azure の機能に適用されるその他の法律条項については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」に記載されています。

この記事では、Azure portal を使用して、Azure の医療 Api (FHIR サービスと呼ばれる) 内に FHIR サービスをデプロイする方法について説明します。

## <a name="prerequisite"></a>前提条件

作業を開始する前に、Azure の医療 Api をデプロイしておく必要があります。 Azure の医療 Api のデプロイの詳細については、「 [Deploy workspace in the Azure portal](../healthcare-apis-quickstart.md)」を参照してください。

## <a name="create-a-new-fhir-service"></a>新しい FHIR サービスを作成する

ワークスペースで、[ **Deploy FHIR Services**] を選択します。

[![FHIR サービス ](media/fhir-service/deploy-fhir-services.png) のデプロイ ](media/fhir-service/deploy-fhir-services.png#lightbox)

[ **+ ADD FHIR Service**] を選択します。

[![FHIR サービス ](media/fhir-service/add-fhir-service.png) の追加 ](media/fhir-service/add-fhir-service.png#lightbox)

FHIR サービスの **アカウント名** を入力します。 **Fhir バージョン**(**STU3** または **R4**) を選択し、[**レビュー + 作成**] を選択します。

[![FHIR サービス ](media/fhir-service/create-fhir-service.png) の作成 ](media/fhir-service/create-fhir-service.png#lightbox)

[ **作成**] を選択する前に、FHIR サービスの **基本** と **その他の設定** のプロパティを確認します。 戻って変更を加える必要がある場合は、[ **前** へ] を選択します。 **検証成功** メッセージが表示されていることを確認します。 

[![FHIR サービス ](media/fhir-service/validation-fhir-service.png) の検証 ](media/fhir-service/validation-fhir-service.png#lightbox)

## <a name="additional-settings-optional"></a>追加の設定 (オプション)

[ **追加設定** ] タブを選択して、認証設定を表示することもできます。 Azure API for FHIR の既定の構成では、**データ プレーン ロールの割り当てに Azure RBAC が使用されます**。 このモードで構成されている場合、fhir サービスの "Authority" は、サブスクリプションの Azure Active Directory テナントに設定されます。

[![追加設定 FHIR サービス ](media/fhir-service/additional-settings-tab.png) ](media/fhir-service/additional-settings-tab.png#lightbox)

[ **許可されたオブジェクト id** ] を入力するためのボックスが淡色表示になっていることに注意してください。これは、この場合、Azure RBAC を使用してロールの割り当てを構成するためです。

外部またはセカンダリの Azure Active Directory テナントを使用するように FHIR サービスを構成する場合は、オーソリティを変更し、サーバーへのアクセスを許可するユーザーおよびグループのオブジェクト ID を入力します。

## <a name="fetch-fhir-api-capability-statement"></a>FHIR API の機能ステートメントをフェッチする

新しい FHIR API アカウントがプロビジョニングされていることを検証するには、を参照して機能ステートメントをフェッチし `https://<WORKSPACE NAME>-<ACCOUNT-NAME>.fhir.azurehealthcareapis.com/metadata` ます。

## <a name="next-steps"></a>次のステップ

>[!div class="nextstepaction"]
>[Postman を使用して FHIR サービスにアクセスする](../use-postman.md)
