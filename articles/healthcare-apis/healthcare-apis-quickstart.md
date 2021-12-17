---
title: Azure portal - Azure Healthcare API にワークスペースをデプロイする
description: このドキュメントでは、ワークスペースをワークスペースにデプロイする方法について説明Azure portal。
author: stevewohl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: quickstart
ms.date: 07/12/2021
ms.author: ginle
ms.custom: mode-portal
ms.openlocfilehash: 89be6d8bab9ef544ffa62a85f0b87afb15fab013
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/24/2021
ms.locfileid: "133061575"
---
# <a name="deploy-healthcare-apis-preview-workspace-using-azure-portal"></a>アプリケーションを使用して医療 API (プレビュー) ワークスペースをデプロイAzure portal

> [!IMPORTANT]
> Azure Healthcare APIs は現在プレビュー段階です。 ベータ版、プレビュー版、または一般提供としてまだリリースされていない Azure の機能に適用されるその他の法律条項については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」に記載されています。

この記事では、Azure Healthcare API をデプロイしてワークスペースを作成する方法について、次の記事Azure portal。 ワークスペースは、FHIR サービス、DICOM、® サービス、IoT コネクタなど、すべての医療 API サービスの一元化された論理コンテナーです。 基になるデータセットとサービス間で共有される特定の構成設定を、必要に応じて整理および管理できます。


## <a name="prerequisite"></a>前提条件

ワークスペースをワークスペースに作成する前Azure portalアカウント サブスクリプションが必要です。 Azure サブスクリプションをお持ちでない場合は、「無料の Azure アカウントを今すぐ [作成する」を参照してください](https://azure.microsoft.com/free/search/?OCID=AID2100131_SEM_c4b0772dc7df1f075552174a854fd4bc:G:s&ef_id=c4b0772dc7df1f075552174a854fd4bc:G:s&msclkid=c4b0772dc7df1f075552174a854fd4bc)。

## <a name="create-new-azure-service"></a>新しい Azure サービスを作成する

Azure Portal で、 **[リソースの作成]** を選択します。

[![リソースの作成 ](media/create-resource.png) ](media/create-resource.png#lightbox)

## <a name="search-for-azure-healthcare-apis"></a>Azure Healthcare API を検索する

検索ボックスに **、「Azure Healthcare API」と入力します**。

[![医療 API を検索する ](media/search-for-healthcare-apis.png) ](media/search-for-healthcare-apis.png#lightbox)

## <a name="create-azure-healthcare-api-account"></a>Azure Healthcare API アカウントを作成する

[作成 **] を** 選択して、新しい Azure Healthcare API アカウントを作成します。

   [![ワークスペースの作成プレビュー ](media/create-workspace-preview.png) ](media/create-workspace-preview.png#lightbox)

## <a name="enter-subscription-and-instance-details"></a>サブスクリプションとインスタンスの詳細を入力する

1. ドロップダウン リスト **から [サブスクリプション** ] **と [リソース** グループ] を選択するか、[新しい の作成 **] を選択します**。

   [![ワークスペースの新規作成 ](media/create-healthcare-api-workspace-new.png) ](media/create-healthcare-api-workspace-new.png#lightbox)

2. ワークスペースの **[名前** ] を入力し、[リージョン] を選択 **します**。 名前は 3 ~ 24 文字の英数字で、すべて小文字にする必要があります。 名前に無効な文字であるハイフン "-" を使用しないでください。 リージョンと可用性ゾーンの詳細については、「Azure のリージョンと [Availability Zones」を参照してください](../availability-zones/az-overview.md)。

3. (**省略可能**)[次 **へ: タグ] を選択>。** [名前] **と [値]** **を入力し**、[次 **へ: 確認および作成] を選択します**。 

   [![タグ ](media/tags-new.png) ](media/tags-new.png#lightbox)

   タグは、リソースの分類に使用される名前と値のペアです。 タグの詳細については、「タグを使用 [して Azure リソースと管理階層を整理する」を参照してください](.././azure-resource-manager/management/tag-resources.md)。

4. **［作成］** を選択します

[![ワークスペースの用語 ](media/workspace-terms.png) ](media/workspace-terms.png)


   **省略** 可能: 新しく作成 **したワークスペースを自動化する [テンプレート** のダウンロード] を選択できます。


## <a name="next-steps"></a>次の手順

ワークスペースが作成されたので、次の作業を行えます。

* FHIR サービスをデプロイする
* DICOM サービスをデプロイする
* アプリケーションをデプロイIoT Connector FHIR サービスにデータを取り込む。
* 変換 API と識別取り出し API を使用して、データを異なる形式に変換し、セカンダリで使用します。


[![さまざまなサービスをデプロイする ](media/healthcare-apis-deploy-services.png) ](media/healthcare-apis-deploy-services.png)

>[!div class="nextstepaction"]
>[ワークスペースの概要](workspace-overview.md)
