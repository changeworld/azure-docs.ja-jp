---
author: laujan
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 09/22/2021
ms.author: lajanuar
ms.custom: ignite-fall-2021
ms.openlocfilehash: c93a34308e235ba487cc1ffa3f668f2b179b8055
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131027697"
---
* 最適な結果を得るには、ドキュメントごとに 1 つの鮮明な写真または高品質のスキャンを提供してください。
* サポートされているファイル形式: JPEG、PNG、BMP、TIFF、および PDF (テキスト埋め込みまたはスキャン済み)。 文字の抽出と位置に関するエラーが発生する可能性を排除するには、テキストが埋め込まれている PDF が最適です。
* PDF および TIFF の場合、最大 2000 ページを処理できます (Free レベルのサブスクリプションでは、最初の 2 ページのみが処理されます)。
* ファイル サイズは 50 MB 未満である必要があります。
* 画像の寸法は、50 x 50 ピクセルから 10,000 x 10,000 ピクセルの間である必要があります。
* PDF の寸法は、17 x 17 インチまでで、Legal または A3 サイズ以下の用紙に対応します。
* トレーニング データの合計サイズは、500 ページ以下です。
* PDF がパスワードでロックされている場合は、送信前にロックを解除する必要があります。
* 教師なし学習の場合 (ラベルの付いたデータなし):
  * データには、キーと値を含める必要があります。
  * キーは値の上または左に表示され、下または右に表示することはできません。
