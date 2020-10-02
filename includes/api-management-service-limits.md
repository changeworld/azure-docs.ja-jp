---
title: インクルード ファイル
description: インクルード ファイル
services: api-management
author: vladvino
ms.assetid: 1b813833-39c8-46be-8666-fd0960cfbf04
ms.service: api-management
ms.topic: include
ms.date: 04/14/2020
ms.author: vlvinogr
ms.custom: include file
ms.openlocfilehash: 9ed918c841989cb50efa362251d09c0b655b7fc4
ms.sourcegitcommit: c52e50ea04dfb8d4da0e18735477b80cafccc2cf
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/08/2020
ms.locfileid: "89570035"
---
| リソース | 制限 |
| ---------------------------------------------------------------------- | -------------------------- |
| スケール ユニットの最大数 | リージョンあたり 10<sup>1</sup> |
| キャッシュ サイズ | ユニットあたり 5 GiB<sup>2</sup> |
| HTTP 機関あたりのバックエンドの同時接続<sup>3</sup> | ユニットあたり 2,048<sup>4</sup> |
| キャッシュする応答の最大サイズ | 2 MiB |
| ポリシー ドキュメントの最大サイズ | 256 KiB<sup>5</sup> |
| サービス インスタンスあたりの最大カスタム ゲートウェイ ドメイン<sup>6</sup> | 20 |
| サービス インスタンスあたりの CA 証明書の最大数<sup>7</sup> | 10 |
| サブスクリプションあたりのサービス インスタンスの最大数<sup>8</sup> | 20 |
| サービス インスタンスあたりのサブスクリプションの最大数<sup>8</sup> | 500 |
| サービス インスタンスあたりのクライアント証明書の最大数<sup>8</sup> | 50 |
| サービス インスタンスあたりの API の最大数<sup>8</sup> | 50 |
| サービス インスタンスあたりの API 操作の最大数<sup>8</sup> | 1,000 |
| 最大合計要求時間<sup>8</sup> | 30 秒 |
| バッファー処理されたペイロードの最大サイズ<sup>8</sup> | 2 MiB |
| 要求 URL の最大サイズ<sup>9</sup> | 4,096 バイト |
| セルフホステッド ゲートウェイの最大数<sup>10</sup> | 25 |

<sup>1</sup> スケーリングの制限は、価格レベルによって変わります。 価格レベルと、価格レベルのスケーリング制限については、「[API Management の価格](https://azure.microsoft.com/pricing/details/api-management/)」を参照してください。<br/>
<sup>2</sup> ユニット キャッシュ サイズは、価格レベルによって変わります。 価格レベルと、価格レベルのスケーリング制限については、「[API Management の価格](https://azure.microsoft.com/pricing/details/api-management/)」を参照してください。<br/>
<sup>3</sup>バックエンドによって明示的に終了されない場合、接続はプールされ、再利用されます。<br/>
<sup>4</sup>この制限は、Basic レベル、Standard レベル、および Premium レベルのユニットあたりです。 Developer レベルは 1,024 に制限されます。 この制限は、従量課金レベルには適用されません。<br/>
<sup>5</sup>この制限は Basic、Standard、および Premium レベルに適用されます。 従量課金レベルのポリシー ドキュメントのサイズは 4 KiB に制限されています。<br/>
<sup>6</sup>複数のカスタム ドメインは、Developer レベルと Premium レベルでのみサポートされます。<br/>
<sup>7</sup>CA 証明書は従量課金レベルではサポートされていません。<br/>
<sup>8</sup>この制限は、従量課金レベルにのみ適用されます。 他のレベルについては、これらのカテゴリに制限はありません。<br/>
<sup>9</sup>従量課金レベルにのみ適用されます。 最大 2,048 バイト長のクエリ文字列を含みます。<br/>
<sup>10</sup>セルフホステッド ゲートウェイは、Developer レベルと Premium レベルでのみサポートされます。 この制限は、[セルフホステッド ゲートウェイ リソース](https://docs.microsoft.com/rest/api/apimanagement/2019-12-01/gateway)の数に適用されます。 この制限を引き上げるには、[サポート](https://azure.microsoft.com/support/options/)にお問い合わせください。 セルフホステッド ゲートウェイ リソースに関連付けられるノード (またはレプリカ) の数は、Premium レベルでは無制限であり、Developer レベルでは単一のノードに制限されることに注意してください。
