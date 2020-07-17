---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 06/27/2019
ms.author: pafarley
ms.openlocfilehash: 11783f8fac147e6fb2cf371ee1a588cf318a5e9c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "75379438"
---
Form Recognizer は、次の要件を満たす入力ドキュメントを処理します。

* 形式は、JPG、PNG、PDF (テキストまたはスキャン済み)、TIFF のいずれかである必要があります。 テキストが埋め込まれている PDF は、文字の抽出と場所にエラーが生じる可能性がないため、最適です。
* PDF がパスワードでロックされている場合は、送信前にロックを解除する必要があります。
* PDF および TIFF ドキュメントは 200 ページ以下とし、トレーニング データセットの合計サイズは 500 ページ以下とする必要があります。
* 画像の場合、寸法は 600 x 100 ピクセルと 4,200 x 4,200 ピクセルの間に収める必要があります。
* 紙のドキュメントからスキャンする場合、フォームは高品質のスキャンでなければなりません。
* テキストでは、ラテン アルファベット (英語の文字) を使用する必要があります。
* 教師なし学習 (ラベル付けされたデータ不使用) の場合、データには、キーと値を含める必要があります。
* 教師なし学習 (ラベル付けされたデータ不使用) の場合、キーは、値の上または左に配置する必要があります。下や右に配置されていてはいけません。

Form Recognizer は現在、次の種類の入力データをサポートしていません。

* 複雑なテーブル (入れ子になったテーブル、マージされたヘッダーまたはセルなど)。
* チェックボックスまたはラジオ ボタン。
