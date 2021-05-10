---
title: azcopy jobs resume | Microsoft Docs
description: この記事では、azcopy jobs remove コマンドに関する参照情報を提供します。
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 07/24/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: c6a4745c4059c81384448deba37495030c4bf3a3
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/14/2021
ms.locfileid: "107503373"
---
# <a name="azcopy-jobs-remove"></a>azcopy jobs remove

指定されたジョブ ID に関連付けられているすべてのファイルを削除します。

> [!NOTE] 
> ログ ファイルとプラン ファイルが保存される場所をカスタマイズできます。 詳細については、[azcopy env](storage-ref-azcopy-env.md) コマンドを参照してください。

```
azcopy jobs remove [jobID] [flags]
```

## <a name="related-conceptual-articles"></a>関連する概念に関する記事

- [AzCopy を使ってみる](storage-use-azcopy-v10.md)
- [AzCopy と Blob Storage でデータを転送する](./storage-use-azcopy-v10.md#transfer-data)
- [AzCopy とファイル ストレージでデータを転送する](storage-use-azcopy-files.md)

## <a name="examples"></a>例

```
  azcopy jobs rm e52247de-0323-b14d-4cc8-76e0be2e2d44
```

## <a name="options"></a>Options

**--help**                remove のヘルプを表示します。

## <a name="options-inherited-from-parent-commands"></a>親コマンドから継承されるオプション

**--cap-mbps float**      転送速度の上限を設定します (メガビット/秒)。 瞬間的なスループットは、上限と若干異なる場合があります。 このオプションを 0 に設定した場合や省略した場合、スループットは制限されません。

**--output-type** string   コマンドの出力形式。 選択肢には、text、json などがあります。 既定値は `text` です。 (既定値は `text`)

**--trusted-microsoft-suffixes** string   Azure Active Directory ログイン トークンを送信できる追加のドメイン サフィックスを指定します。  既定値は " *.core.windows.net;* .core.chinacloudapi.cn; *.core.cloudapi.de;* .core.usgovcloudapi.net" です。 ここに記載されているすべてが既定値に追加されます。 セキュリティのために、Microsoft Azure のドメインのみをここに入力してください。 複数のエンティティは、セミコロンで区切ります。

## <a name="see-also"></a>関連項目

- [azcopy jobs](storage-ref-azcopy-jobs.md)
