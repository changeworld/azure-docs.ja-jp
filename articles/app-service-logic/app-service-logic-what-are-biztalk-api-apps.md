---
title: "コネクタと BizTalk API Apps とは"
description: "API Apps、コネクタ、および BizTalk API Apps について説明します。"
services: logic-apps
documentationcenter: 
author: MandiOhlinger
manager: erikre
editor: 
ms.assetid: 1295ea9c-30e0-4b5c-931e-51aefae2d4fe
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/01/2016
ms.author: mandia
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: c89b0714a5c6e606cf8517a40be62cc856d5959c


---
# <a name="what-are-connectors-and-biztalk-api-apps"></a>コネクタと BizTalk API Apps とは
[!INCLUDE [app-service-logic-version-message](../../includes/app-service-logic-version-message.md)]

*コネクタ* は、接続に焦点を絞った API アプリの一種です。 他の API アプリと同様に、コネクタは Web アプリ、モバイル アプリ、およびロジック アプリから使用されます。 コネクタは、既存のサービスとの接続を容易にし、認証の管理、監視、分析などに役立ちます。

開発者は独自の API アプリを作成し、個人的にデプロイできます。 将来的には、開発者は独自に作成した API アプリを Marketplace で共有して収益化できます。 

![API Apps Marketplace](./media/app-service-logic-what-are-biztalk-api-apps/Marketplace.png)

開発者がソリューションを構築しやすいように、Azure チームによって、一般的なシナリオに対応するコネクタがいくつか Marketplace に追加されています。 さらに、対応範囲を複雑で高度な統合シナリオにまで広げるために、Premium と BizTalk の機能も提供されています。

さまざまなサービス レベルが利用できます。 どのレベルにもすべてのコネクタと API アプリが含まれ、その全機能を使用できます。  

[App Service の料金](https://azure.microsoft.com/pricing/details/app-service/) に関するページでは、これらのサービス レベルについて説明し、各レベルに含まれる機能を表にして示しています。 次のセクションでは、BizTalk API アプリとコネクタのさまざまなカテゴリについて説明します。

## <a name="hybrid-connectors"></a>ハイブリッド コネクタ
ハイブリッド コネクタによって、企業と [DB2](app-service-logic-connector-db2.md)、[Informix](app-service-logic-connector-informix.md)、WebSphere MQ の接続まで、対応範囲が広がります。 

その他のリソース: [企業間コネクタと API Apps](app-service-logic-b2b-connectors.md)  
[B2B プロセスを作成する](app-service-logic-create-a-b2b-process.md)  
[取引先契約を作成する](app-service-logic-create-a-trading-partner-agreement.md)  
[B2B メッセージを追跡する](app-service-logic-track-b2b-messages.md)  

## <a name="rules"></a>ルール
ビジネス ルールには、ビジネス プロセスを制御するポリシーと意思決定がカプセル化されます。 通常、ルールは動的で、ビジネス プランや規則などのさまざまな理由により、時間の経過と共に変化します。 [BizTalk ルール](app-service-logic-use-biztalk-rules.md) では、アプリケーション コードからこれらのポリシーを分離して、より簡単かつ迅速にプロセスを変更できるようにします。

## <a name="connector-and-api-app-list"></a>コネクタと API のアプリの一覧
Standard コネクタ、BizTalk EAI、Premium コネクタなどの各カテゴリに含まれるコネクタと API Apps の完全な一覧については、 [コネクタと API Apps の一覧](app-service-logic-connectors-list.md) を参照してください。




<!--HONumber=Nov16_HO2-->


