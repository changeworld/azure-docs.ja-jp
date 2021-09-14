---
title: azcopy list | Microsoft Docs
description: この記事では、azcopy list コマンドに関する参照情報を提供します。
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 09/21/2021
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 31bbabd194b2b2ef06266feb39b4d9618bbc0794
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/03/2021
ms.locfileid: "123435818"
---
# <a name="azcopy-list"></a>azcopy list

指定されたリソース内のエンティティを一覧表示します。

## <a name="synopsis"></a>概要

現在のリリースでは、BLOB コンテナーのみがサポートされています。

```azcopy
azcopy list [containerURL] [flags]
```

## <a name="related-conceptual-articles"></a>関連する概念に関する記事

- [AzCopy を使ってみる](storage-use-azcopy-v10.md)
- [AzCopy と Blob Storage でデータを転送する](./storage-use-azcopy-v10.md#transfer-data)
- [AzCopy とファイル ストレージでデータを転送する](storage-use-azcopy-files.md)

## <a name="examples"></a>例

```azcopy
azcopy list [containerURL]
```

## <a name="options"></a>オプション

|オプション|説明|
|--|--|
|-h, --help|list コマンドのヘルプ コンテンツを表示します。|
|--machine-readable|ファイル サイズをバイト単位で一覧表示します。|
|--mega-units|単位を 1000 の桁で表示します (1024 ではない)。|
| --properties |   一覧の出力で必要なプロパティの区切り記号 (;) で区切られた値。 |
|--running-tally|ファイルの合計数とそのサイズをカウントします。|

## <a name="options-inherited-from-parent-commands"></a>親コマンドから継承されるオプション

|オプション|説明|
|---|---|
|--cap-mbps float|転送速度の上限を設定します (メガビット/秒)。 瞬間的なスループットは、上限と若干異なる場合があります。 このオプションを 0 に設定した場合や省略した場合、スループットは制限されません。|
|--output-type string|コマンドの出力形式。 選択肢には、text、json などがあります。 既定値は "text" です。|
|--trusted-microsoft-suffixes string   |Azure Active Directory ログイン トークンを送信できる追加のドメイン サフィックスを指定します。  既定値は " *.core.windows.net;* .core.chinacloudapi.cn; *.core.cloudapi.de;* .core.usgovcloudapi.net" です。 ここに記載されているすべてが既定値に追加されます。 セキュリティのために、Microsoft Azure のドメインのみをここに入力してください。 複数のエンティティは、セミコロンで区切ります。|

## <a name="see-also"></a>関連項目

- [azcopy](storage-ref-azcopy.md)
