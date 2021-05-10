---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 10/15/2020
ms.author: alkohli
ms.openlocfilehash: 20d30935fe03bc002ab63f2f8ca1ce890ef9e3b4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96581192"
---
ここでは、書き込むことのできる Azure オブジェクトのサイズを示します。 アップロードするすべてのファイルがこれらの制限に準拠していることを確認してください。

| Azure オブジェクトの種類 | アップロードの制限                                             |
|-------------------|-----------------------------------------------------------|
| ブロック BLOB        | 4.75 TB 以下                                                 |
| ページ BLOB         | 1 TB (テラバイト) <br> ページ BLOB 形式でアップロードするファイルには 512 バイトをアライン (整数倍) する必要があります。そうでないと、アップロードが失敗します。 <br> VHD と VHDX には 512 バイトがアラインされます。 |
| Azure Files         | 1 TB (テラバイト) <br> ページ BLOB 形式でアップロードするファイルには 512 バイトをアライン (整数倍) する必要があります。そうでないと、アップロードが失敗します。 <br> VHD と VHDX には 512 バイトがアラインされます。 |

> [!IMPORTANT]
> ストレージの種類に関係なく、ファイルの作成では、最大 5 TB まで許可されます。 ただし、前の表に定義されているアップロード制限を超えるサイズのファイルを作成した場合、そのファイルはアップロードされません。 手動でファイルを削除して、領域を解放する必要があります。