---
author: laujan
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 06/27/2019
ms.author: lajanuar
ms.openlocfilehash: 262cde08f93ca7d56a454effe17579dd086c3d7a
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128909491"
---
Form Recognizer は、これらの要件を満たす入力ドキュメントを処理します。

* サポートされているファイル形式: JPEG、PNG、BMP、TIFF、および PDF (テキスト埋め込みまたはスキャン済み)。 文字の抽出と位置に関するエラーが発生する可能性を排除するには、テキストが埋め込まれている PDF が最適です。
* ファイル サイズは 50 MB 未満である必要があります。
* 画像の寸法は、50 x 50 ピクセルから 10,000 x 10,000 ピクセルの間である必要があります。
* PDF の寸法は、最大で 17 x 17 インチにする必要があります (リーガル サイズまたは A3 サイズ以下の用紙に対応します)。
* PDF および TIFF の場合、最初の 200 ページのみが処理されます (Free レベルのサブスクリプションでは、最初の 2 ページのみが処理されます)。
* トレーニング データ セットの合計サイズは、500 ページ以下にする必要があります。
* PDF がパスワードでロックされている場合は、送信前にロックを解除する必要があります。
* 紙のドキュメントからスキャンする場合、フォームは高品質のスキャンでなければなりません。
* 教師なし学習 (ラベル付けされたデータ不使用) の場合、データには、キーと値を含める必要があります。
* 教師なし学習 (ラベル付けされたデータ不使用) の場合、キーは、値の上または左に配置する必要があります。下や右に配置されていてはいけません。
