---
title: azcopy | Microsoft Docs
description: この記事では、azcopy commandコマンドに関する参照情報を提供します。
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 984d0c570c6c0d5048d58377f113319157411244
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/17/2019
ms.locfileid: "72513386"
---
# <a name="azcopy"></a>azcopy

AzCopy は、Azure Storage との間でデータを移動するコマンドライン ツールです。

## <a name="synopsis"></a>概要

コマンドの一般的な形式は、`azcopy [command] [arguments] --[flag-name]=[flag-value]` のようになります。

問題を報告する場合や、ツールの詳細については、[https://github.com/Azure/azure-storage-azcopy](https://github.com/Azure/azure-storage-azcopy) を参照してください。

## <a name="options"></a>オプション

**--cap-mbps uint32**   転送速度の上限を設定します (メガビット/秒)。 瞬間的なスループットは、上限と若干異なる場合があります。 このオプションを 0 に設定した場合や省略した場合、スループットは制限されません。

**-h, --help** azcopy のヘルプ コンテンツを表示します
      
**--output-type** コマンドの出力形式。 選択肢には、text、json などがあります。 既定値は 'text' です。 (既定値 "text")

## <a name="see-also"></a>関連項目

- [AzCopy を使ってみる](storage-use-azcopy-v10.md)
- [azcopy copy](storage-ref-azcopy-copy.md)
- [azcopy doc](storage-ref-azcopy-doc.md)
- [azcopy env](storage-ref-azcopy-env.md)
- [azcopy jobs](storage-ref-azcopy-jobs.md)
- [azcopy list](storage-ref-azcopy-list.md)
- [azcopy login](storage-ref-azcopy-login.md)
- [azcopy logout](storage-ref-azcopy-logout.md)
- [azcopy make](storage-ref-azcopy-make.md)
- [azcopy remove](storage-ref-azcopy-remove.md)
- [azcopy sync](storage-ref-azcopy-sync.md)
