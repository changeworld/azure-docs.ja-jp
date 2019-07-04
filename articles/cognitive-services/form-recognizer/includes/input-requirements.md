---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: form-recognizer
ms.topic: include
ms.date: 06/27/2019
ms.author: pafarley
ms.openlocfilehash: 690219347782aad2140b0a1b0541590c5426c568
ms.sourcegitcommit: aa66898338a8f8c2eb7c952a8629e6d5c99d1468
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2019
ms.locfileid: "67459738"
---
Form Recognizer は、次の要件を満たす入力ドキュメントを処理します。

* 形式は、JPG、PNG、または PDF (テキストまたはスキャン済み) である必要があります。 テキストが埋め込まれている PDF は、文字の抽出と場所にエラーが生じる可能性がないため、最適です。
* ファイル サイズは 4 メガバイト (MB) 未満である必要があります。
* イメージの場合、寸法は 50 x 50 ピクセルと 4200 x 4200 ピクセルの間に収める必要があります。
* 紙のドキュメントからスキャンする場合、フォームは高品質のスキャンでなければなりません。
* テキストでは、ラテン アルファベット (英語の文字) を使用する必要があります。
* データは (手書きではなく) 印刷する必要があります。
* データには、キーと値を含める必要があります。
* キーは値の上または左に配置されていてかまいませんが、下または右に配置されていてはいけません。

Form Recognizer は現在、次の種類の入力データをサポートしていません。

* 複雑なテーブル (入れ子になったテーブル、マージされたヘッダーまたはセルなど)。
* チェックボックスまたはラジオ ボタン。
* 50 ページを超える PDF ドキュメント。