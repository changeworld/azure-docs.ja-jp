---
title: Windows ユーザー用 Azure Cloud Shell | Microsoft Docs
description: Linux システムに慣れていないユーザー向けのガイド
services: azure
documentationcenter: ''
author: maertendMSFT
manager: hemantm
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/03/2018
ms.author: damaerte
ms.openlocfilehash: 5e318a0f64033aa0c4b306e547c11e1994afa229
ms.sourcegitcommit: 0b4da003fc0063c6232f795d6b67fa8101695b61
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/05/2018
ms.locfileid: "37861221"
---
# <a name="powershell-in-azure-cloud-shell-for-windows-users"></a>Windows ユーザー用 Azure Cloud Shell 内の PowerShell

2018 年 5 月、Azure Cloud Shell 内の PowerShell の変更が[発表](https://azure.microsoft.com/blog/pscloudshellrefresh/)されました。  Azure Cloud Shell での PowerShell エクスペリエンスが Linux の PowerShell Core 6 になります。
この変更により、Cloud Shell 内の PowerShell の一部の要素が、Windows PowerShell 5.1 で期待されているものと異なる場合があります。

## <a name="case-sensitivity"></a>大文字小文字の区別

Windows では、ファイル システムで大文字と小文字は区別されません。  Linux では、ファイル システムで大文字と小文字は区別されます。
つまり、`file.txt` と `FILE.txt` は以前は同じファイルとみなされていましたが、今後は別々のファイルとみなされるようになります。
ファイル システムで `tab` のコンプリート中は、大文字と小文字を適切に区別する必要があります。  `tab` コマンドレットなど、PowerShell 固有のエクスペリエンスでは大文字と小文字は区別されません。 

## <a name="windows-powershell-alias-vs-linux-utilities"></a>Windows PowerShell エイリアスと Linux ユーティリティ

`ls`、`sort`、`sleep` などの既存の Linux コマンドは PowerShell エイリアスよりも優先されます。  削除される主なエイリアスと、同等の機能を持つコマンドを次に示します。  

|削除されるエイリアス   |同等のコマンド   |
|---|---|
|`ls`     | `dir` <br> `Get-ChildItem` |
|`sort`   | `Sort-Object` |
|`sleep`  | `Start-Sleep` |

## <a name="persisting-home-vs-homeclouddrive"></a>$home と $home\clouddrive の永続化

スクリプトやその他のファイルをクラウド ドライブに長期保存していたユーザーに関して、$HOME ディレクトリがセッションをまたいで永続化されるようになりました。

## <a name="powershell-profile"></a>PowerShell プロファイル

既定では、PowerShell プロファイルは作成されません。  プロファイルを作成するには、`$HOME/.config` 下に `PowerShell` ディレクトリを作成します。  `$HOME/.config/PowerShell` 内に、`Microsoft.PowerShell_profile.ps1` という名前でプロファイルを作成できます。

## <a name="whats-new-in-powershell-core-6"></a>PowerShell Core 6 の新機能

PowerShell Core 6 の新機能の詳細については、[PowerShell ドキュメント](https://docs.microsoft.com/powershell/scripting/whats-new/what-s-new-in-powershell-core-60?view=powershell-6)および「[Getting Started with PowerShell Core](https://blogs.msdn.microsoft.com/powershell/2017/06/09/getting-started-with-powershell-core-on-windows-mac-and-linux/)」のブログ記事を参照してください。
