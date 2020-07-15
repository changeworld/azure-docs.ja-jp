---
title: クイック スタート:Azure portal を使用して Azure API for FHIR をデプロイする
description: このクイックスタートでは、Azure portal を使用して Azure API for FHIR をデプロイし、設定を構成する方法について説明します。
services: healthcare-apis
author: hansenms
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: quickstart
ms.date: 02/07/2019
ms.author: mihansen
ms.openlocfilehash: e729597e9d83c4e6096fe52b577b052d94ca4799
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/16/2020
ms.locfileid: "84820021"
---
# <a name="quickstart-deploy-azure-api-for-fhir-using-azure-portal"></a>クイック スタート:Azure portal を使用して Azure API for FHIR をデプロイする

このクイックスタートでは、Azure portal を使用して Azure API for FHIR をデプロイする方法について説明します。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="create-new-resource"></a>新しいリソースの作成

[Azure portal](https://portal.azure.com) を開いて **[リソースの作成]** をクリックします。

![リソースの作成](media/quickstart-paas-portal/portal-create-resource.png)

## <a name="search-for-azure-api-for-fhir"></a>Azure API for FHIR を検索する

Azure API for FHIR は、検索ボックスに「FHIR」と入力すると見つかります。

![医療 API を検索する](media/quickstart-paas-portal/portal-search-healthcare-apis.png)

## <a name="create-azure-api-for-fhir-account"></a>Azure API for FHIR アカウントを作成する

新しい Azure API for FHIR アカウントを作成するには、 **[作成]** を選択します。

![Azure API for FHIR アカウントを作成する](media/quickstart-paas-portal/portal-create-healthcare-apis.png)

## <a name="enter-account-details"></a>アカウントの詳細を入力する

既存のリソース グループを選択するか、リソース グループを新たに作成し、アカウントの名前を選択して、最後に **[確認と作成]** をクリックします。

![新しい医療 API の詳細](media/quickstart-paas-portal/portal-new-healthcareapi-details.png)

作成の確認を行い、FHIR API のデプロイを待ちます。

## <a name="additional-settings"></a>追加設定

**[次へ: 追加設定]** をクリックして、機関と対象ユーザー、さらに、この Azure API for FHIR へのアクセスを許可する ID オブジェクト ID を構成し、必要に応じて SMART on FHIR を有効にして、データベース スループットを構成します。

- **機関:** サービスの認証機関としてログインしているテナントとは別の Azure AD テナントを指定できます。
- **Audience:** 対象ユーザーは、FHIR サーバーの URL に設定することをお勧めします (これが既定の設定となります)。 設定はここで変更できます。 トークンの対象となる受信者は、対象ユーザーによって識別されます。 このコンテキストでは、FHIR API 自体を表す値に設定する必要があります。
- **許可されたオブジェクト ID:** この Azure API for FHIR へのアクセスを許可する ID オブジェクト ID を指定できます。 ユーザーとサービス プリンシパルのオブジェクト ID を見つける方法について詳しくは、攻略ガイド「[ID オブジェクト ID を見つける](find-identity-object-ids.md)」を参照してください。  
- **SMART on FHIR プロキシ:** SMART on FHIR プロキシを有効にすることができます。 SMART on FHIR プロキシを構成する方法の詳細については、[Azure API for FHIR の SMART on FHIR プロキシ](https://docs.microsoft.com/azure/healthcare-apis/use-smart-on-fhir-proxy)に関するチュートリアルを参照してください。  
- **プロビジョニング スループット (RU/秒):** ここには、Azure API for FHIR の基になるデータベースのスループット設定を指定できます。 この設定は、後から [データベース] ブレードで変更できます。 詳細については、[データベース設定の構成](configure-database.md)に関するページを参照してください。


![許可されたオブジェクト ID を構成する](media/quickstart-paas-portal/configure-audience.png)

## <a name="fetch-fhir-api-capability-statement"></a>FHIR API の機能ステートメントをフェッチする

新しい FHIR API アカウントがプロビジョニングされていることを確認するには、ブラウザーに `https://<ACCOUNT-NAME>.azurehealthcareapis.com/metadata` を指定して機能ステートメントをフェッチします。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

リソース グループ、Azure API for FHIR、およびすべての関連リソースは、不要になったら削除できます。 そのためには、Azure API for FHIR アカウントがあるリソース グループを選択し、 **[リソース グループの削除]** を選択して、削除するリソース グループの名前を確認します。

## <a name="next-steps"></a>次のステップ

このクイックスタート ガイドでは、ご利用のサブスクリプションに Azure API for FHIR をデプロイしました。 Azure API for FHIR に対してその他の設定を行うには、追加設定の攻略ガイドに進んでください。

>[!div class="nextstepaction"]
>[Azure API for FHIR の追加設定](azure-api-for-fhir-additional-settings.md)
