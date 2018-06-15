---
title: インクルード ファイル
description: インクルード ファイル
services: functions
author: ggailey777
manager: cfowler
ms.service: functions
ms.topic: include
ms.date: 05/01/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 88c01e8e57d4a92478b8b1ca0689ff0f8e499b39
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/04/2018
ms.locfileid: "33814630"
---
ローカルに実行している Functions ホストは、次のパスにログを書き込みます。

```
<DefaultTempDirectory>\LogFiles\Application\Functions
```

Windows では、`<DefaultTempDirectory>` は TMP、TEMP、USERPROFILE 環境変数、または Windows ディレクトリで最初に見つかった値です。
MacOS または Linux では、`<DefaultTempDirectory>` は TMPDIR 環境変数です。

> [!NOTE]
> Functions ホストは、開始するときに、ディレクトリ内の既存のファイル構造を上書きします。