---
title: Azure portal でのワークスペースのデプロイ-Azure の医療 Api
description: このドキュメントでは、Azure portal でワークスペースを展開する方法をユーザーに説明します。
author: stevewohl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: quickstart
ms.date: 07/12/2021
ms.author: ginle
ms.openlocfilehash: 1b77b05f5780f41a9b4c573d117b18f2493b635a
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121786114"
---
# <a name="deploy-healthcare-apis-preview-workspace-using-azure-portal"></a>Azure portal を使用した医療 Api (プレビュー) ワークスペースのデプロイ

> [!IMPORTANT]
> Azure Healthcare APIs は現在プレビュー段階です。 ベータ版、プレビュー版、または一般提供としてまだリリースされていない Azure の機能に適用されるその他の法律条項については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」に記載されています。

この記事では、Azure portal を通じて Azure の医療 Api をデプロイしてワークスペースを作成する方法について説明します。 ワークスペースは、FHIR サービス、DICOM®サービス、IoT コネクタなどのすべての医療 Api サービスのための一元的な論理コンテナーです。 これにより、基になるすべてのデータセットとサービス間で共有される特定の構成設定を、必要に応じて整理および管理できます。


## <a name="prerequisite"></a>前提条件

Azure portal でワークスペースを作成する前に、アカウントのサブスクリプションが必要です。 Azure サブスクリプションをお持ちでない場合は、「 [今すぐ無料の azure アカウントを作成](https://azure.microsoft.com/free/search/?OCID=AID2100131_SEM_c4b0772dc7df1f075552174a854fd4bc:G:s&ef_id=c4b0772dc7df1f075552174a854fd4bc:G:s&msclkid=c4b0772dc7df1f075552174a854fd4bc)する」を参照してください。

## <a name="create-new-azure-service"></a>新しい Azure サービスの作成

Azure Portal で、 **[リソースの作成]** を選択します。

[![リソース ](media/create-resource.png) の作成 ](media/create-resource.png#lightbox)

## <a name="search-for-azure-healthcare-apis"></a>Azure の医療 Api の検索

[検索] で、「 **Azure の医療 api**」と入力します。

[![医療 api ](media/search-for-healthcare-apis.png) の検索 ](media/search-for-healthcare-apis.png#lightbox)

## <a name="create-azure-healthcare-api-account"></a>Azure の医療 API アカウントを作成する

[ **作成** ] を選択して、新しい Azure 医療 api アカウントを作成します。

   [![ワークスペースプレビュー ](media/create-workspace-preview.png) の作成 ](media/create-workspace-preview.png#lightbox)

## <a name="enter-subscription-and-instance-details"></a>サブスクリプションとインスタンスの詳細を入力してください

1. ドロップダウンリストから **サブスクリプション** と **リソースグループ** を選択するか、[ **新規作成**] を選択します。

   [![ワークスペースの新規 ](media/create-healthcare-api-workspace-new.png) 作成 ](media/create-healthcare-api-workspace-new.png#lightbox)

2. ワークスペースの **名前** を入力し、 **リージョン** を選択します。 名前は 3 ~ 24 文字の英数字で、すべて小文字で指定する必要があります。 ハイフン "-" は、名前として無効な文字なので使用しないでください。 リージョンと可用性ゾーンの詳細については、「 [Azure のリージョンと Availability Zones](../availability-zones/az-overview.md)」を参照してください。

3. (**省略可能**)[ **次へ: タグ >**] を選択します。 **名前** と **値** を入力し、[次へ] を選択します。 [**レビュー + 作成**]。 

   [![タグ ](media/tags-new.png) ](media/tags-new.png#lightbox)

   タグは、リソースを分類するために使用される名前と値のペアです。 タグの詳細については、「 [タグを使用した Azure リソースと管理階層の整理」を](.././azure-resource-manager/management/tag-resources.md)参照してください。

4. **［作成］** を選択します

[![ワークスペースの使用条件 ](media/workspace-terms.png) ](media/workspace-terms.png)


   **省略可能**: [新しく作成されたワークスペースの **自動化のためのテンプレートをダウンロード** する] を選択できます。


## <a name="next-steps"></a>次の手順

ワークスペースが作成されたので、次のことができます。

* FHIR サービスのデプロイ
* DICOM サービスのデプロイ
* IoT Connector をデプロイし、データを fhir サービスに取り込みます。
* 変換と非識別 Api を使用して、データをさまざまな形式に変換し、2つ目の用途に対応します。


[![さまざまなサービス ](media/healthcare-apis-deploy-services.png) のデプロイ ](media/healthcare-apis-deploy-services.png)

>[!div class="nextstepaction"]
>[ワークスペースの概要](workspace-overview.md)

