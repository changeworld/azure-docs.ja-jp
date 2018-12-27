---
title: インクルード ファイル
description: インクルード ファイル
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: include
ms.date: 09/12/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: f4a1937062f7e59cb3ef3f38610e077e8d36a3ac
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2018
ms.locfileid: "47005555"
---
### <a name="what-is-expressroute-direct"></a>ExpressRoute Direct とは何ですか?

ExpressRoute Direct では、世界中に戦略的に分散されたピアリングの場所で Microsoft のグローバル ネットワークに直接接続する機能がお客様に提供されます。 ExpressRoute Direct では、大規模なアクティブ/アクティブ接続をサポートするデュアル 100 Gbps 接続が提供されます。 

### <a name="how-do-customers-connect-to-expressroute-direct"></a>ExpressRoute Direct に接続するにはどうすればよいですか? 

お客様が ExpressRoute Direct を利用するには、ローカル通信事業者やコロケーション プロバイダーと協力して、ExpressRoute ルーターに接続する必要があります。

### <a name="what-locations-will-the-100gbps-expressroute-direct-be-available-for-public-preview"></a>100 Gbps ExpressRoute Direct をパブリック プレビューに使用できるのはどこですか? 

パブリック プレビューでこれをサポートしている ExpressRoute ピアリングの場所の数は制限されています。 使用可能なポートは動的であり、PowerShell を使用して容量を表示できます。 次の場所が含まれ、*可用性に基づいて変更される可能性があります*。

* アムステルダム
* キャンベラ
* シカゴ
* ワシントン DC
* ダラス 
* 香港特別行政区
* ロサンゼルス
* New York City
* パリ
* サンアントニオ
* シリコン バレー
* シンガポール 

### <a name="what-is-the-sla-for-expressroute-direct"></a>ExpressRoute Direct の SLA は何ですか?

ExpressRoute Direct は同じ [ExpressRoute のエンタープライズ グレード](https://azure.microsoft.com/support/legal/sla/expressroute/v1_3/)を使用します。 

### <a name="what-scenarios-should-customers-consider-with-expressroute-direct"></a>ExpressRoute Direct ではどのようなシナリオを検討する必要がありますか?  

ExpressRoute Direct では、お客様に、Microsoft グローバル バックボーンへの直接 100 Gbps ポート ペアが提供されます。 最大のメリットをお客様に提供するシナリオとしては、大量のデータ インジェスト、規制市場向けの物理的な分離、バースト シナリオ専用の容量などがあります。 

### <a name="what-is-the-billing-model-for-expressroute-direct"></a>ExpressRoute Direct の請求モデルは何ですか? 

ExpressRoute Direct では、固定金額でポート ペアに対して請求されます。 Standard 回線は追加時間なしで含まれ、Premium はわずかな追加料金があります。 送信は、ピアリングの場所のゾーンに基づいて回線ごとに課金されます。