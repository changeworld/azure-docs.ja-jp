---
title: インクルード ファイル
description: インクルード ファイル
services: api-management
author: vladvino
ms.assetid: 1b813833-39c8-46be-8666-fd0960cfbf04
ms.service: api-management
ms.topic: include
ms.date: 03/22/2018
ms.author: vlvinogr
ms.custom: include file
ms.openlocfilehash: bee289da3f18edd0cb425f3d9acde084567a3b13
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2018
---
| リソース | 制限 |
| --- | --- |
| スケールの単位 | リージョンあたり 10<sup>1</sup> |
| キャッシュ | ユニットあたり 5 GB<sup>1</sup> |
| HTTP 機関あたりのバックエンドの同時接続<sup>2</sup> | ユニットあたり 2048<sup>3</sup> |
| キャッシュする応答の最大サイズ | 10 MB |
| カスタム ゲートウェイ ドメインの最大数 | サービス インスタンスあたり 20<sup>4</sup> |


<sup>1</sup>API Management に関する制限は、各価格レベルによって異なります。 価格レベルと、価格レベルのスケーリング制限については、「[API Management の価格](https://azure.microsoft.com/pricing/details/api-management/)」を参照してください。
<sup>2</sup> バックエンドによって明示的に終了されない場合、接続はプールされ、再利用されます。
<sup>3</sup> Basic レベル、Standard レベル、および Premium レベルのユニットあたり。 Developer レベルは 1024 に制限されます。
<sup>4</sup> Premium レベルでのみ使用できます。


