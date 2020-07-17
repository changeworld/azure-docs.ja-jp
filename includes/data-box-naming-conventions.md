---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 05/21/2019
ms.author: alkohli
ms.openlocfilehash: d965b89659a9e3dc01035221a729f094c336eb5b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "66244592"
---
| Entity                                       | 規則   |
|----------------------------------------------|-----------------------------------------------------|
| ブロック BLOB とページ BLOB のコンテナー名 | 3 ～ 63 文字の有効な DNS 名にする必要があります。 <br>  先頭は文字か数字にします。 <br> 小文字、数字、ハイフン (-) のみを含めることができます。 <br> すべてのハイフン (-) は、その直前または直後に文字または数字が使用されている必要があります。 <br> 連続するハイフンを名前に使用することはできません。 |
| Azure ファイルの共有名                  | 同上                                          |
| Azure ファイルのディレクトリ名とファイル名     |<li> 小文字、大文字を区別し、長さが 255 文字以内である必要があります。 </li><li> 末尾にフォワード スラッシュ (/) を使用することはできません。 </li><li>使用した場合、自動的に削除されます。 </li><li> 次の文字は使用できません: <code>" \\ / : \| < > * ?</code></li><li> URL の予約文字は適切にエスケープしてください。 </li><li> 無効な URL パス文字は使用できません。 \\uE000 のようなコード ポイントは、有効な Unicode 文字ではありません。 制御文字 (0x00 to 0x1F, \\u0081 など) などの一部の ASCII または Unicode 文字も使用できません。 Http/1.1 での Unicode 文字列に適用される規則については、RFC 2616 の Section 2.2:「Basic Rules」および RFC 3987 を参照してください。 </li><li> 以下のファイル名は使用できません。LPT1、LPT2、LPT3、LPT4、LPT5、LPT6、LPT7、LPT8、LPT9、COM1、COM2、COM3、COM4、COM5、COM6、COM7、COM8、COM9、PRN、AUX、NUL、CON、CLOCK$、ドット (.)、2 ドット(..)</li>|
| ブロック BLOB とページ BLOB の BLOB 名      | </li><li>BLOB 名は、大文字と小文字が区別されます。また、名前には任意の組み合わせの文字を使用できます。 </li><li>BLOB 名は 1 文字から 1,024 文字にする必要があります。 </li><li>URL の予約文字は適切にエスケープしてください。 </li><li>BLOB 名を構成するパスのセグメントの数が 254 個を超えないようにしてください。 パスのセグメントは、仮想ディレクトリの名前に対応した連続する区切り記号文字 (スラッシュ "/" など) の間の文字列です。</li> |
