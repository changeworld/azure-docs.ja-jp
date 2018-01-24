---
title: "Azure Cloud Shell の埋め込み | Microsoft Docs"
description: "Azure Cloud Shell を埋め込む方法について説明します。"
services: cloud-shell
documentationcenter: 
author: jluk
manager: timlt
tags: azure-resource-manager
ms.assetid: 
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 12/11/2017
ms.author: juluk
ms.openlocfilehash: 78b539136971aa282e5447d7882ecb02f73f346b
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/16/2017
---
# <a name="embed-azure-cloud-shell"></a>Azure Cloud Shell の埋め込み

Cloud Shell を埋め込むことで、開発者やコンテンツ作成者は専用の URL ([shell.azure.com](https://shell.azure.com)) から直接 Cloud Shell を開くことができます。これにより、Cloud Shell の認証、ツール、および最新の Azure CLI/Azure PowerShell ツールを、フル機能で即座にユーザーに提供することができます。

[![](https://shell.azure.com/images/launchcloudshell.png "Azure Cloud Shell を起動する")](https://shell.azure.com)

## <a name="how-to"></a>操作方法

次のコードをコピーして、Cloud Shell の起動ボタンをマークダウン ファイルに統合します。

```markdown
[![Launch Cloud Shell](https://shell.azure.com/images/launchcloudshell.png "Launch Cloud Shell")](https://shell.azure.com)
```

ポップアップ Cloud Shell を埋め込むための HTML は次のとおりです。
```html
<a style="cursor:pointer" onclick='javascript:window.open("https://shell.azure.com", "_blank", "toolbar=no,scrollbars=yes,resizable=yes,menubar=no,location=no,status=no")'><image src="https://shell.azure.com/images/launchcloudshell.png" /></a>
```

## <a name="customize-experience"></a>エクスペリエンスをカスタマイズする

URL を拡張して、特定のシェル エクスペリエンスを設定します。
|エクスペリエンス   |URL   |
|---|---|
|最近使用したシェル   |shell.azure.com           |
|Bash                       |shell.azure.com/bash       |
|PowerShell                 |shell.azure.com/powershell |

## <a name="next-steps"></a>次の手順
[Cloud Shell の Bash のクイックスタート](quickstart.md)<br>
[Cloud Shell の PowerShell のクイックスタート](quickstart-powershell.md)
