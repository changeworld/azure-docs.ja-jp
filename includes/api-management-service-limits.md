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
ms.openlocfilehash: 646ebc79e3615b289b60e47c0a38595e7892d8cc
ms.sourcegitcommit: 58dc0d48ab4403eb64201ff231af3ddfa8412331
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/26/2019
ms.locfileid: "55084852"
---
| リソース | 制限 |
| --- | --- |
| スケール ユニットの最大数 | リージョンあたり 10<sup>1</sup> |
| キャッシュ サイズ | ユニットあたり 5 GB<sup>2</sup> |
| HTTP 機関あたりのバックエンドの同時接続<sup>3</sup> | ユニットあたり 2,048<sup>4</sup> |
| キャッシュする応答の最大サイズ | 2 MB |
| ポリシー ドキュメントの最大サイズ | 256 KB<sup>5</sup> | 
| サービス インスタンスあたりの最大カスタム ゲートウェイ ドメイン<sup>6</sup> | 20 | 
| サブスクリプションあたりのサービス インスタンスの最大数<sup>7</sup> | 20 | 
| サービス インスタンスあたりのサブスクリプションの最大数<sup>7</sup> | 500 |
| サービス インスタンスあたりのクライアント証明書の最大数<sup>7</sup> | 50 | 
| サービス インスタンスあたりの API の最大数<sup>7</sup> | 50 | 
| サービス インスタンスあたりの API 操作の最大数<sup>7</sup> | 1,000 | 
| 最大合計要求時間<sup>7</sup> | 30 秒 | 
| バッファー処理されたペイロードの最大サイズ<sup>7</sup> | 2 MB | 


<sup>1</sup> スケーリングの制限は、価格レベルによって変わります。 価格レベルと、価格レベルのスケーリング制限については、「[API Management の価格](https://azure.microsoft.com/pricing/details/api-management/)」を参照してください。<br/>
<sup>2</sup> ユニット キャッシュ サイズは、価格レベルによって変わります。 価格レベルと、価格レベルのスケーリング制限については、「[API Management の価格](https://azure.microsoft.com/pricing/details/api-management/)」を参照してください。<br/>
<sup>3</sup> バックエンドによって明示的に終了されない場合、接続はプールされ、再利用されます。<br/>
<sup>4</sup> Basic レベル、Standard レベル、および Premium レベルのユニットあたり。 Developer レベルは 1024 に制限されます。 従量課金レベルには適用されません。<br/> 
<sup>5</sup> Basic、Standard、および Premium エディションの場合。 従量課金レベルのポリシー ドキュメントのサイズは 4 KB に制限されています。<br/>
<sup>6</sup> Premium レベルでのみ使用できます。<br/>
<sup>7</sup> 従量課金レベルにのみ適用されます。<br/>



