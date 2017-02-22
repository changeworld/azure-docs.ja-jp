---
title: "Azure Logic Apps 統合アカウント メタデータ | Microsoft Docs"
description: "統合アカウント メタデータの概要"
author: padmavc
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: bb7d9432-b697-44db-aa88-bd16ddfad23f
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/21/2016
ms.author: padmavc
translationtype: Human Translation
ms.sourcegitcommit: e5d567800d00b41ec0782216b442f437a2500928
ms.openlocfilehash: 41edd713d85790793341e100f77300f999ac8c73


---
# <a name="azure-logic-apps-integration-account-metadata"></a>Azure Logic Apps 統合アカウント メタデータ

## <a name="overview"></a>概要

統合アカウントに追加されるパートナー、契約、スキーマ、マップは、キーと値のペアでメタデータを格納します。 カスタム メタデータを定義することができます。これは、ランタイム時に取得できます。  現時点で、アーティファクトには UI でメタデータを作成する機能がありません。作成するには、REST API を使用できます。  パートナー、契約、およびスキーマには **[EDIT as JSON (JSON として編集)]** があり、メタデータ情報のキー入力が可能です。  ロジック アプリでは、**統合アカウントのアーティファクトの参照**がメタデータ情報の取得に役立ちます。

## <a name="how-to-store-metadata"></a>メタデータを格納する方法

1. [統合アカウント](logic-apps-enterprise-integration-create-integration-account.md)を作成します。   

2. 統合アカウントに[パートナー](logic-apps-enterprise-integration-partners.md#how-to-create-a-partner)、[契約](logic-apps-enterprise-integration-agreements.md#how-to-create-agreements)、または[スキーマ](logic-apps-enterprise-integration-schemas.md)を追加します。

3. パートナー、契約、またはスキーマを選択します。 **[EDIT as JSON (JSON として編集)]** を選択し、メタデータの詳細を入力します。    
![メタデータの入力](media/logic-apps-enterprise-integration-metadata/image1.png)  

## <a name="call-integration-account-artifact-lookup-from-a-logic-app"></a>ロジック アプリから**統合アカウントのアーティファクトの参照**を呼び出す

1. [ロジック アプリ](logic-apps-create-a-logic-app.md)を作成します。

2. ロジック アプリを統合アカウントと[リンク](logic-apps-enterprise-integration-create-integration-account.md#link-an-integration-account-to-a-logic-app)します。    

3. たとえば、**統合アカウントのアーティファクトの参照**を検索する前に *Request* または *HTTP* を使用して、トリガーを作成します。  **統合アカウントのアーティファクトの参照**を探す場合は、**integration** を検索します。  
![参照の検索](media/logic-apps-enterprise-integration-metadata/image2.png)

3. **[Integration Account Artifact LookUp (統合アカウントのアーティファクトの参照)]** を選択します。  

4. **アーティファクトの種類**を選択し、**アーティファクトの名前**を指定します。  
![参照の検索](media/logic-apps-enterprise-integration-metadata/image3.png)

## <a name="an-example-to-retrieve-partner-metadata"></a>パートナーのメタデータを取得する例

1. パートナーのメタデータには、ルーティング URL の詳細情報が含まれています。    
![参照の検索](media/logic-apps-enterprise-integration-metadata/image6.png)

2. ロジック アプリで**統合アカウントのアーティファクトの参照**と**HTTP** を構成します。  
![参照の検索](media/logic-apps-enterprise-integration-metadata/image4.png)

3. URI を取得するコード ビューは、次のようになります。    
![参照の検索](media/logic-apps-enterprise-integration-metadata/image5.png)


## <a name="next-steps"></a>次のステップ
* [契約についての詳細情報](logic-apps-enterprise-integration-agreements.md "Enterprise Integration の契約についての詳細情報")  



<!--HONumber=Feb17_HO1-->


