---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 02/07/2019
ms.author: alkohli
ms.openlocfilehash: ad7ceffed61665a729d4391b5f0ff2935375c253
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/08/2019
ms.locfileid: "55966939"
---
Azure にデータを移動する際には、次の注意事項がデータに適用されます。

- 複数のデバイスが同じコンテナーに書き込まないようにすることをお勧めします。
- コピー対象のオブジェクトと同じ名前の既存の Azure オブジェクト (BLOB やファイルなど) がクラウド内にある場合、デバイスはクラウド内のファイルを上書きします。
- 共有フォルダーの下に作成される空のディレクトリ階層 (ファイルを含まない) は、BLOB コンテナーにアップロードされません。
- 大きなファイルの場合は、断続的なエラーに対してコピー操作が再試行されるため、robocopy を使用することをお勧めします。
