---
title: Azure Event Grid を使用した Auth0 パートナー トピック
description: Azure Event Grid を使用して Auth0 から Azure サービスにイベントを送信します。
services: event-grid
author: banisadr
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/18/2020
ms.author: babanisa
ms.openlocfilehash: e193b272460fe97f599adff414fcac6e80648104
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/21/2020
ms.locfileid: "83738372"
---
# <a name="auth0-partner-topics"></a>Auth0 パートナー トピック
![Auth0 ロゴ](./media/auth0-overview/auth0-logo.png)

Auth0 は、アプリケーション ビルダー向けの ID プラットフォームであり、アプリケーションをセキュリティで保護するために必要なビルディング ブロックを開発者や企業に提供します。

Auth0 パートナー トピックでは、Auth0 のシステムによって発行されるイベントを使用して、多くのタスクを実行できます。 認証後に有意義な方法でユーザーと関わったり、セキュリティ タスクとインフラストラクチャ タスクを自動化したりします。

統合により、信頼性の高い Auth0 ログ イベントを Azure にストリーミングできます。 Azure では、任意の Azure リソースでイベントを使用できます。 この統合により、イベントへの対応、分析情報の取得、セキュリティの問題の監視、他の強力なデータ パイプラインとのやり取りが可能になります。

Auth0 と Azure を使用する組織では、この統合により、スタック全体でデータをシームレスに統合できます。 
 
## <a name="available-event-types"></a>使用可能なイベントの種類
使用可能な Auth0 イベントの種類とその説明の一覧は、[こちら](https://auth0.com/docs/logs/references/log-event-type-codes)で入手できます。

## <a name="use-cases"></a>ユース ケース

### <a name="engage-with-your-users"></a>ユーザーと関わる
離反を減らし、ユーザーを保持するには、強力なユーザー エクスペリエンスを提供することが非常に重要です。 Azure Functions および Logic Apps で Auth0 イベントを使用して、よりカスタマイズされたアプリケーション エクスペリエンスを提供します。 

### <a name="understand-user-behavior"></a>ユーザーの行動を理解する
ユーザーが製品にアクセスするタイミング、サインインしている場所、ユーザーが使用しているデバイスを理解します。 これらのシグナルを追跡することで、最も重要な製品領域の理解を深めます。 これらのシグナルは、サポートすべきブラウザーとデバイス、アプリをローカライズする言語、トラフィックがピークに達する時間帯の特定に役立ちます。 

### <a name="manage-user-data"></a>ユーザー データを管理する
ユーザー アクションの維持と監査は、セキュリティを維持し、業界の規制に従ううえで非常に重要です。 欧州連合の一般データ保護規則 (GDPR) などのプライバシー法に従うために、ユーザー データを編集、削除、またはエクスポートする機能がますます重要になっています。

### <a name="secure-your-application"></a>アプリケーションをセキュリティで保護する
分散システムを保護する場合、セキュリティ監視手順とインシデント対応手順を組み合わせることが重要です。 そのため、すべてのデータを 1 か所に保持し、スタック全体を監視することが重要となります。 

## <a name="next-steps"></a>次のステップ

- [パートナー トピックの概要](partner-topics-overview.md)
- [Auth0 パートナー トピックの使用方法](auth0-how-to.md)
- [Auth0 のドキュメント](https://auth0.com/docs/azure-tutorial)
- [Event Grid パートナーになる](partner-onboarding-overview.md)

