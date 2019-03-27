---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 02/14/2019
ms.author: alkohli
ms.openlocfilehash: 8bc4b78d262cf5f30076e90b40b92c4f3237924a
ms.sourcegitcommit: d2329d88f5ecabbe3e6da8a820faba9b26cb8a02
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/16/2019
ms.locfileid: "56333772"
---
Azure にデータを移動する際には、次の注意事項がデータに適用されます。

- 複数のデバイスが同じコンテナーに書き込まないようにすることをお勧めします。
- コピー対象のオブジェクトと同じ名前の既存の Azure オブジェクト (BLOB やファイルなど) がクラウド内にある場合、デバイスはクラウド内のファイルを上書きします。
- 共有フォルダーの下に作成される空のディレクトリ階層 (ファイルを含まない) は、BLOB コンテナーにアップロードされません。
- 大きなファイルの場合は、断続的なエラーに対してコピー操作が再試行されるため、robocopy を使用することをお勧めします。
- Azure Storage コンテナーに関連付けられた共有から、作成時に共有に定義された BLOB の種類と一致しない BLOB がアップロードされた場合、そのような BLOB は更新されません。 たとえば、デバイス上にブロック BLOB の共有を作成します。 共有と、ページ BLOB がある既存のクラウド コンテナーを関連付けます。 その共有を更新してファイルをダウンロードします。 クラウドにページ BLOB として既に保存されている、更新されたファイルの一部を変更します。 アップロード エラーが表示されます。
