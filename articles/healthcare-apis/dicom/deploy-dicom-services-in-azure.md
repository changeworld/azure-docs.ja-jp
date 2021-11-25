---
title: Azure portal-Azure の医療 Api を使用して DICOM サービスをデプロイする
description: この記事では、Azure portal に DICOM サービスをデプロイする方法について説明します。
author: stevewohl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: quickstart
ms.date: 08/04/2021
ms.author: aersoy
ms.custom: mode-portal
ms.openlocfilehash: fefaf7fc2daced95ffde90834ad38131ecfc6b94
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/24/2021
ms.locfileid: "133041689"
---
# <a name="deploy-dicom-service-using-the-azure-portal"></a>Azure portal を使用して DICOM サービスをデプロイする

> [!IMPORTANT]
> Azure Healthcare APIs は現在プレビュー段階です。 ベータ版、プレビュー版、または一般提供としてまだリリースされていない Azure の機能に適用されるその他の法律条項については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」に記載されています。

このクイックスタートでは、Azure portal を使用して、DICOM サービスをデプロイする方法について説明します。

デプロイが完了したら、Azure portal を使用して、新しく作成された DICOM サービスに移動し、サービス URL などの詳細を確認できます。 DICOM サービスにアクセスするためのサービス URL は次のようになり ```https://<workspacename-dicomservicename>.dicom.azurehealthcareapis.com``` ます。 要求を行うときは、url の一部としてバージョンを指定してください。 詳細については、 [「DICOM サービスの API バージョン管理」ドキュメント](api-versioning-dicom-service.md)を参照してください。

## <a name="prerequisite"></a>前提条件

DICOM サービスをデプロイするには、Azure portal にワークスペースが作成されている必要があります。 ワークスペースの作成の詳細については、「 **Deploy workspace in the Azure portal**」を参照してください。

## <a name="deploying-dicom-service"></a>DICOM サービスの展開

1. Azure portal の [ **リソースグループ** ] ページで、[ **医療 Api] ワークスペース** の名前を選択します。

   [![[ワークスペースリソースグループ] を選択します。 ](media/select-workspace-resource-group.png) ](media/select-workspace-resource-group.png#lightbox)

2. [ **DICOM サービスのデプロイ**] を選択します。

   [![dicom サービスを展開します。 ](media/workspace-deploy-dicom-services.png) ](media/workspace-deploy-dicom-services.png#lightbox)


3. [ **DICOM サービスの追加**] を選択します。

   [![dicom サービスを追加します。 ](media/add-dicom-service.png) ](media/add-dicom-service.png#lightbox)


4. DICOM サービスの名前を入力し、[ **レビュー + 作成**] を選択します。 

    [![dicom サービス名。 ](media/enter-dicom-service-name.png) ](media/enter-dicom-service-name.png#lightbox)


   (**省略可能**)[ **次へ: タグ >**] を選択します。

    タグは、リソースを分類するために使用される名前と値のペアです。 タグの詳細については、「 [タグを使用した Azure リソースと管理階層の整理」を](../../azure-resource-manager/management/tag-resources.md)参照してください。

5. 緑の検証チェックマークが表示されたら、[ **作成** ] を選択して、DICOM サービスをデプロイします。

6. デプロイプロセスが完了したら、[ **リソースにアクセス**] を選択します。  

   [![dicom はリソースにアクセスします。 ](media/go-to-resource.png) ](media/go-to-resource.png#lightbox)



   新しくデプロイされた DICOM サービスの結果を次に示します。

   [![dicom が展開を終了しました。 ](media/results-deployed-dicom-service.png) ](media/results-deployed-dicom-service.png#lightbox)



## <a name="next-steps"></a>次のステップ

>[!div class="nextstepaction"]
>[DICOM サービスの概要](dicom-services-overview.md)
