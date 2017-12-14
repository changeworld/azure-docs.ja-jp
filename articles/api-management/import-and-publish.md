---
title: "Azure API Management での最初の API のインポートと発行 | Microsoft Docs"
description: "API Management で最初の API をインポートおよび発行する方法を説明します。"
services: api-management
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.custom: mvc
ms.topic: tutorial
ms.date: 11/15/2017
ms.author: apimpm
ms.openlocfilehash: cd6ceaf5f8cdcfbde5d0d2bebb4b89488d0122e9
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/04/2017
---
# <a name="import-and-publish-your-first-api"></a>最初の API のインポートと発行 

このチュートリアルでは、http://conferenceapi.azurewebsites.net?format=json に存在する "OpenAPI の仕様" バックエンド API をインポートする方法を示します。 このバックエンド API は Microsoft によって提供され、Azure でホストされています。 

バックエンド API が API Management (APIM) にインポートされると、APIM API はバックエンド API のファサードになります。 バックエンド API のインポート時に、ソース API と APIM API は同一です。 APIM を使用すると、バックエンド API に触れることなく必要に応じてファサードをカスタマイズできます。 詳しくは、「[API を変換および保護する](transform-api.md)」をご覧ください。 

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * 最初の API のインポート
> * Azure Portal での API のテスト
> * 開発者ポータルでの API のテスト

![新しい API](./media/api-management-get-started/created-api.png)

## <a name="prerequisites"></a>前提条件

[Azure API Management インスタンスの作成](get-started-create-service-instance.md)に関するクイックスタートを完了します。

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="create-api"> </a>バックエンド API のインポートと発行

このセクションでは、OpenAPI の仕様のバックエンド API をインポートおよび発行する方法を示します。
 
1. **[API MANAGEMENT]** の下から **[API]** を選択します。
2. 一覧から **[OpenAPI の仕様]** を選択します。

    ![API の作成](./media/api-management-get-started/create-api.png)

    API の値は、作成時に、または後で **[設定]** タブに移動して設定できます。  

    |設定|値|説明|
    |---|---|---|
    |**OpenAPI の仕様**|http://conferenceapi.azurewebsites.net?format=json|API を実装しているサービスを参照します。 要求は、API Management によってこのアドレスに転送されます。|
    |**表示名**|"*Demo Conference API (デモ会議 API)*"|サービスの URL を入力した後で Tab キーを押すと、APIM は json の内容に基づいてこのフィールドに入力します。 <br/>この名前は開発者ポータルに表示されます。|
    |**名前**|*demo-conference-api*|API の一意の名前を指定します。 <br/>サービスの URL を入力した後で Tab キーを押すと、APIM は json の内容に基づいてこのフィールドに入力します。|
    |**説明**|API に関する任意の説明を指定します。|サービスの URL を入力した後で Tab キーを押すと、APIM は json の内容に基づいてこのフィールドに入力します。|
    |**API URL サフィックス**|*conference*|サフィックスは、API Management サービスのベース URL に付加されます。 API Management では API がサフィックスによって識別されるため、サフィックスは、特定の発行者のすべての API で一意である必要があります。|
    |**URL スキーム**|*HTTPS*|API へのアクセスに使用できるプロトコルを決定します。 |
    |**成果物**|*無制限*| API を成果物に関連付けることで API を発行します。 必要に応じてこの新しい API を成果物に追加するには、成果物名を入力します。 この手順を複数回繰り返して、API を複数の成果物に追加できます。<br/>成果物は、1 つまたは複数の API の関連付けです。 複数の API を含めて、開発者ポータルを通じてそれらを開発者に提供できます。 開発者は、まず成果物をサブスクライブして API へのアクセス権を取得する必要があります。 サブスクライブすると、その成果物の API に適したサブスクリプション キーを受け取ります。 APIM インスタンスを作成した場合は、既に管理者になっているので、既定ですべての成果物をサブスクライブしています。<br/> 各 API Management インスタンスは、**スターター**および**無制限**という 2 つのサンプル成果物を既定で備えています。 |
3. **[作成]**を選択します。

## <a name="test-the-new-apim-api-in-the-azure-portal"></a>Azure Portal での新しい APIM API のテスト

Azure Portal には、API の操作を見てテストするための便利な環境が用意されており、操作を直接呼び出すことができます。  
1. 前の手順で作成した API を選びます。
2. **[テスト]** タブをクリックします。

    ![API のテスト](./media/api-management-get-started/test-api.png)
3. **[GetSpeakers]** をクリックします。

    ページにはクエリ パラメーターのフィールドが表示されますが、この場合は何もありません。 ページには、ヘッダーのフィールドも表示されます。 この API に関連付けられている成果物のサブスクリプション キーの場合、ヘッダーの 1 つは "Ocp-Apim-Subscription-Key" です。 APIM インスタンスを作成した場合は、既に管理者になっているので、キーは自動的に入力されます。 
4. **[送信]** をクリックします。

    バックエンドは "**200 OK**" といくつかのデータで応答します。

## <a name="call-operation"></a>開発者ポータルから操作を呼び出す

操作を**開発者ポータル**から呼び出して API をテストすることもできます。 

1. "バックエンド API のインポートと発行" の手順で作成した API を選びます。
2. **[開発者ポータル]** をクリックします。

    ![開発者ポータルでのテスト](./media/api-management-get-started/developer-portal.png)

    "開発者ポータル" サイトが開きます。
3. **[API]** を選択します。
4. **[Demo Conference API]\(デモ会議 API)\** を選択します。
5. **[GetSpeakers]** をクリックします。
    
    ページにはクエリ パラメーターのフィールドが表示されますが、この場合は何もありません。 ページには、ヘッダーのフィールドも表示されます。 この API に関連付けられている成果物のサブスクリプション キーの場合、ヘッダーの 1 つは "Ocp-Apim-Subscription-Key" です。 APIM インスタンスを作成した場合は、既に管理者になっているので、キーは自動的に入力されます。
6. **[テスト]** をクリックします。
7. **[送信]** をクリックします。
    
    操作を呼び出すと、**応答のステータス**、**応答ヘッダー**、**応答内容**が開発者ポータルに表示されます。

## <a name="next-steps"> </a>次のステップ

このチュートリアルで学習した内容は次のとおりです。

> [!div class="checklist"]
> * 最初の API のインポート
> * Azure Portal での API のテスト
> * 開発者ポータルでの API のテスト

次のチュートリアルに進みます。

> [!div class="nextstepaction"]
> [成果物を作成して発行する](api-management-howto-add-products.md)