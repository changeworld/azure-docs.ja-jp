---
title: Azure Maps でアクセス可能なアプリケーションを作成 | Microsoft Docs
description: Azure Maps を使用してアクセス可能なアプリケーションをビルドする方法
services: azure-maps
keywords: ''
author: chgennar
ms.author: chgennar
ms.date: 05/18/2018
ms.topic: article
ms.service: azure-maps
documentationcenter: ''
manager: timlt
ms.devlang: na
ms.openlocfilehash: 537a8c80dc0d1fcb2f536d0e30200de19a2111a4
ms.sourcegitcommit: ab3b2482704758ed13cccafcf24345e833ceaff3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/06/2018
ms.locfileid: "37867054"
---
# <a name="building-an-accessible-application"></a>アクセス可能なアプリケーションをビルド

この記事では、スクリーン リーダーで使用できるマップ アプリケーションをビルドする方法を示します。

## <a name="understand-the-code"></a>コードの理解

<iframe height='500' scrolling='no' title='アクセス可能なアプリケーションを作成' src='//codepen.io/azuremaps/embed/ZoVyZQ/?height=504&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a> の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) によって <a href='https://codepen.io/azuremaps/pen/ZoVyZQ/'>アクセス可能なアプリケーションを作成する</a> ペンを参照してください。
</iframe>

マップは、一部のユーザー補助機能を作成済みです。 ユーザーがキーボードを使用してマップを移動し、スクリーン リーダーが実行されている場合、マップがその状態の変更をユーザーに通知します。 たとえば、マップをパンまたはズームする際、ユーザーには、マップの新しい緯度、経度、ズームおよび局所性が通知されます。 基本マップ上に配置される追加情報には、スクリーン リーダー ユーザーの対応するテキスト情報が含まれています。 [Popup](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popup?view=azure-iot-typescript-latest) の使用は、これを実現する方法の 1 つです。 上記の検索の例では、テキスト情報を含むポップアップは、マップ上に配置されているすべてのピンに追加されます。 [Popup の ](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popup?view=azure-iot-typescript-latest) 添付メソッドにより、マップ上のポップアップを視覚的に表示せず、スクリーン リーダーで確認できるようにします。

## <a name="next-steps"></a>次の手順

この記事で使われている Popup クラスとメソッドの詳細については、次を参照してください。

* [Popup](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popup?view=azure-iot-typescript-latest)
    * [attach](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popup?view=azure-iot-typescript-latest#attach)
    * [remove](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popup?view=azure-iot-typescript-latest#remove)
    * [open](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popup?view=azure-iot-typescript-latest#open)
    * [close](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popup?view=azure-iot-typescript-latest#close)

より多くのマッピング シナリオの [サンプルのコード ページ](http://aka.ms/AzureMapsSamples) をチェック アウトします。
