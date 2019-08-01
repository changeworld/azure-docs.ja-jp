---
title: Azure Maps でアクセス可能なアプリケーションを作成 | Microsoft Docs
description: Azure Maps を使用してアクセス可能なアプリケーションをビルドする方法
services: azure-maps
author: chgennar
ms.author: chgennar
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
manager: timlt
ms.openlocfilehash: 8865027c895be09150797608e43184f1fdefb267
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/30/2019
ms.locfileid: "68638790"
---
# <a name="building-an-accessible-application"></a>アクセス可能なアプリケーションをビルド

この記事では、スクリーン リーダーで使用できるマップ アプリケーションをビルドする方法を示します。

## <a name="understand-the-code"></a>コードの理解

<iframe height='500' scrolling='no' title='アクセス可能なアプリケーションを作成' src='//codepen.io/azuremaps/embed/ZoVyZQ/?height=504&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a> の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) によって <a href='https://codepen.io/azuremaps/pen/ZoVyZQ/'>アクセス可能なアプリケーションを作成する</a> ペンを参照してください。
</iframe>

マップには、ユーザー補助機能が事前に組み込まれています。 ユーザーはキーボードを使用して、マップ内を移動できます。 スクリーン リーダーが実行されている場合、マップの状態の変化がユーザーに通知されます。
たとえば、マップがパンまたはズームされると、マップの変化がユーザーに通知されます。 基本マップ上に配置される追加情報には、スクリーン リーダー ユーザーの対応するテキスト情報が含まれています。

[Popup](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest) の使用は、これを実現する方法の 1 つです。 上記の検索の例では、テキスト情報を含むポップアップは、マップ上に配置されているすべてのピンに追加されます。 [Popup](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest) の[添付](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#attach)メソッドを使用すると、マップ上にポップアップを視覚的に表示せずに、スクリーン リーダーで確認できます。

## <a name="next-steps"></a>次の手順

この記事で使われている Popup クラスとメソッドの詳細については、次を参照してください。

> [!div class="nextstepaction"]
> [Popup](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest)

他のコード サンプルについては、次を確認してください。

> [!div class="nextstepaction"]
> [コード サンプル ページ](https://aka.ms/AzureMapsSamples)
