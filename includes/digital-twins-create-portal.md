---
title: インクルード ファイル
description: インクルード ファイル
services: digital-twins
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
ms.topic: include
ms.date: 01/07/2020
ms.custom: include file
ms.openlocfilehash: 9ccdc7a438d1dade534d39dc97a39a3bdae37dc4
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/26/2020
ms.locfileid: "75895431"
---
1. [Azure portal](https://portal.azure.com) にサインインする

1. ホームのサイドバーを選択してから、 **[+ リソースの作成]** を選択します。 

   [![ホームのサイドバーを展開し、[+ リソースの作成] を選択する](./media/create-digital-twins-portal/azure-portal-create-a-resource.png)](./media/create-digital-twins-portal/azure-portal-create-a-resource.png#lightbox)

1. **Digital Twins** を検索し、 **[Digital Twins]** を選択します。 

   [![新しい Digital Twins インスタンスを作成するための選択](./media/create-digital-twins-portal/azure-portal-create-digital-twins.png)](./media/create-digital-twins-portal/azure-portal-create-digital-twins.png#lightbox)

   あるいは、 **[モノのインターネット]** を選択してから、 **[Digital Twins (プレビュー)]** を選択します。

1. **[作成]** を選択してデプロイ プロセスを開始します。

   [![リソースのデプロイを作成して確認する](./media/create-digital-twins-portal/azure-create-and-confirm-resource.png)](./media/create-digital-twins-portal/azure-create-and-confirm-resource.png#lightbox)

1. **[Digital Twins]** ウィンドウで、次の情報を入力します。
   * **[リソース名]** : Digital Twins インスタンスの一意の名前を作成します。
   * **[サブスクリプション]** : この Digital Twins インスタンスを作成するために使用するサブスクリプションを選択します。 
   * **[リソース グループ]** : Digital Twins インスタンスの[リソース グループ](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups)を選択または作成します。
   * **[場所]** : 使用しているデバイスに最も近い場所を選択します。

     [![情報が入力されている [Digital Twins] ウィンドウ](./media/create-digital-twins-portal/create-digital-twins-param.png)](./media/create-digital-twins-portal/create-digital-twins-param.png#lightbox)

1. Digital Twins の情報を確認してから、 **[作成]** を選択します。 Digital Twins インスタンスが作成されるまでに数分かかる場合があります。 **[通知]** ウィンドウで進行状況を監視できます。

1. Digital Twins インスタンスの **[概要]** ウィンドウを開きます。 **[管理 API]** の下のリンクに注目します。 **[管理 API]** の URL の形式は次のとおりです。 
   
   ```URL
   https://yourDigitalTwinsName.yourLocation.azuresmartspaces.net/management/swagger
   ```
   
   この URL では、インスタンスに適用される Azure Digital Twins REST API のドキュメントが表示されます。 この API ドキュメントを読んで使用する方法については、「[How to use Azure Digital Twins Swagger (Azure Digital Twins Swagger の使用方法)](../articles/digital-twins/how-to-use-swagger.md)」を参照してください。 **[管理 API]** の URL をコピーし、次の形式に変更します。 
    
   ```URL
   https://yourDigitalTwinsName.yourLocation.azuresmartspaces.net/management/api/v1.0/
   ```
    
   アプリケーションは、変更された URL をベース URL として使用してインスタンスにアクセスします。 この変更した URL を一時ファイルにコピーします。 これは次のセクションで必要になります。

   [![管理 API の概要](./media/create-digital-twins-portal/digital-twins-management-api.png)](./media/create-digital-twins-portal/digital-twins-management-api.png#lightbox)