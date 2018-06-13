---
title: 複数の Azure リージョンへの Azure API Management サービスのデプロイ | Microsoft Docs
description: 複数の Azure リージョンに Azure API Management サービス インスタンスをデプロイする方法について説明します。
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/30/2017
ms.author: apimpm
ms.openlocfilehash: ff0101bde54f99f99461d0f042af520b1642d0df
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/19/2018
ms.locfileid: "31586808"
---
# <a name="how-to-deploy-an-azure-api-management-service-instance-to-multiple-azure-regions"></a>複数の Azure リージョンに Azure API Management サービス インスタンスをデプロイする方法
API Management では複数リージョンのデプロイメントがサポートされており、API パブリッシャーは 1 つの API Management サービスを任意の数の Azure リージョンに分散できます。 これにより、地理的に分散した API コンシューマーによって認識される要求待ち時間が短くなり、1 つのリージョンがオフラインになった場合でもサービスの可用性を向上できます。 

API Management サービスが最初に作成されたとき、サービスには 1 つの[ユニット][unit]のみが含まれ、1 つの Azure リージョンに配置されます。このリージョンがプライマリ リージョンに指定されます。 リージョンは Azure Portal で簡単に追加できます。 各リージョンには API Management のゲートウェイ サーバーがデプロイされており、呼び出しのトラフィックは最も近いゲートウェイにルーティングされます。 リージョンがオフラインになった場合、トラフィックは自動的に次に最も近いゲートウェイにリダイレクトされます。 

> [!IMPORTANT]
> 複数リージョンのデプロイは、**[Premium][Premium]** レベルでのみ提供されます。
> 
> 

## <a name="add-region"> </a>新しいリージョンに API Management サービス インスタンスをデプロイする
> [!NOTE]
> API Management サービス インスタンスをまだ作成していない場合は、[API Management サービス インスタンスの作成][Create an API Management service instance]に関するページを参照してください。
> 
> 

Azure Portal で API Management サービス インスタンスの **[スケールと料金]** ページに移動します。 

![[スケール] タブ][api-management-scale-service]

新しいリージョンにデプロイするには、ツールバーの **[+ 地域の追加]** をクリックします。

![リージョンの追加][api-management-add-region]

ドロップダウン リストから場所を選択し、スライダーでユニット数を設定します。

![ユニットの指定][api-management-select-location-units]

**[追加]** をクリックすると、選択した内容が場所のテーブルに記載されます。 

すべての場所が構成されるまでこのプロセスを繰り返したら、ツールバーの **[保存]** をクリックして、デプロイ プロセスを開始します。

## <a name="remove-region"> </a>場所から API Management サービス インスタンスを削除する

Azure Portal で API Management サービス インスタンスの **[スケールと料金]** ページに移動します。 

![[スケール] タブ][api-management-scale-service]

テーブルの右端にある **[...]** ボタンを使用して、削除する場所のコンテキスト メニューを開きます。 **[削除]** を選択します。

削除されたことを確認したら、**[保存]** をクリックして変更を適用します。

[api-management-management-console]: ./media/api-management-howto-deploy-multi-region/api-management-management-console.png

[api-management-scale-service]: ./media/api-management-howto-deploy-multi-region/api-management-scale-service.png
[api-management-add-region]: ./media/api-management-howto-deploy-multi-region/api-management-add-region.png
[api-management-select-location-units]: ./media/api-management-howto-deploy-multi-region/api-management-select-location-units.png
[api-management-remove-region]: ./media/api-management-howto-deploy-multi-region/api-management-remove-region.png

[Create an API Management service instance]: get-started-create-service-instance.md
[Get started with Azure API Management]: get-started-create-service-instance.md

[Deploy an API Management service instance to a new region]: #add-region
[Delete an API Management service instance from a region]: #remove-region

[unit]: http://azure.microsoft.com/pricing/details/api-management/
[Premium]: http://azure.microsoft.com/pricing/details/api-management/

