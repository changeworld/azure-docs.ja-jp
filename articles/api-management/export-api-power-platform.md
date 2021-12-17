---
title: Azure API Management から Microsoft Power Platform に API をエクスポートする | Microsoft Docs
description: API Management から API をカスタム コネクタとして Microsoft Power Platform 内の Power Apps と Power Automate にエクスポートする方法について説明します。
services: api-management
author: dlepow
ms.service: api-management
ms.topic: how-to
ms.date: 07/27/2021
ms.author: danlep
ms.openlocfilehash: fb27e1a96152885446da2e77e0901680affe3569
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/15/2021
ms.locfileid: "132494845"
---
# <a name="export-apis-from-azure-api-management-to-the-power-platform"></a>Azure API Management から Power Platform に API をエクスポートする 

Microsoft [Power Platform](https://powerplatform.microsoft.com) を使用する市民開発者が、プロの開発者によって開発されて Azure にデプロイされたビジネス機能を必要とするケースは少なくありません。 [Azure API Management](https://aka.ms/apimrocks) では、プロの開発者がそのバックエンド サービスを API として発行し、それらを市民開発者が検出して利用できるよう、カスタム コネクタとして簡単に Power Platform ([Power Apps](/powerapps/powerapps-overview) と [Power Automate](/power-automate/getting-started)) にエクスポートすることができます。 

この記事では、Azure portal 内で、API Management の API に対してカスタムの Power Platform コネクタを作成する手順を説明します。 この機能により、市民開発者は、Power Platform を使用して、API Management によって管理される内部および外部 API に基づくアプリを作成し、配布することができます。

## <a name="prerequisites"></a>前提条件

+ 次のクイック スタートを完了すること:[Azure API Management インスタンスを作成する](get-started-create-service-instance.md)
+ Power Platform にエクスポートしたい API が API Management インスタンスに存在することを確認する
+ Power Apps または Power Automate [環境](/powerapps/powerapps-overview#power-apps-for-admins)があることを確認する 

## <a name="create-a-custom-connector-to-an-api"></a>API へのカスタム コネクタを作成する

1. Azure portal で API Management サービスに移動します。
1. メニューの **[API]** で、 **[Power Platform]** を選択します。
1. **[コネクタの作成]** を選択します。
1. **[コネクタの作成]** ウィンドウで、以下を行います。
    1. Power Platform に発行する API を選択します。
    1. API の発行先の Power Platform 環境を選択します。 
    1. 表示名を入力します。これが、カスタム コネクタの名前として使用されます。  
    1. (省略可) API が [OAuth 2.0 サーバーによって保護](api-management-howto-protect-backend-with-aad.md)されている場合は、 **[クライアント ID]** 、 **[クライアント シークレット]** 、 **[認証 URL]** 、 **[トークン URL]** 、 **[URL の更新]** などの詳細を指定します。  
1. **［作成］** を選択します 

    :::image type="content" source="media/export-api-power-platform/create-custom-connector.png" alt-text="API Management 内で API へのカスタム コネクタを作成する":::

コネクタが作成されたら、[Power Apps](https://make.powerapps.com) または [Power Automate](https://flow.microsoft.com) 環境に移動します。 **[データ] > [カスタム コネクタ]** の下に API が一覧表示されます。

:::image type="content" source="media/export-api-power-platform/custom-connector-power-app.png" alt-text="Power Platform 内のカスタム コネクタ":::

> [!NOTE]
> PowerApps テスト コンソールから API を呼び出すには、API Management インスタンスで [CORS ポリシー](api-management-cross-domain-policies.md#CORS)に呼び出し元として "https://flow.microsoft.com" URL を追加する必要があります。

## <a name="next-steps"></a>次のステップ

* [Power Platform について理解を深める](https://powerplatform.microsoft.com/)
* [カスタム コネクタの作成と使用について確認する](/connectors/custom-connectors/)
* [チュートリアルに従って API Management の一般的なタスクを確認する](./import-and-publish.md)
