---
title: "Azure Cloud Shell (プレビュー) の制限 | Microsoft Docs"
description: "Azure Cloud Shell の制限の概要"
services: 
documentationcenter: 
author: jluk
manager: timlt
tags: azure-resource-manager
ms.assetid: 
ms.service: 
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 05/10/2017
ms.author: juluk
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: a48b32d3e2383497f68ab4eba81af81dd9f8449d
ms.contentlocale: ja-jp
ms.lasthandoff: 05/10/2017

---

# <a name="limitations"></a>制限事項
Azure Cloud Shell には、次の既知の制限があります。

## <a name="system-state-and-persistence"></a>システム状態と永続化
Cloud Shell セッションを提供するコンピューターは一時的であり、セッションが 10 分間非アクティブの状態になると、リサイクルされます。 Cloud Shell では、ファイル共有をマウントする必要があります。
* マウントされた記憶域内でのみ、`$Home` ディレクトリまたは `clouddrive` ディレクトリ内の変更が永続化されます。
  * ファイル共有は、[割当済みリージョン](persisting-shell-storage.md#pre-requisites-for-manual-mounting)内からのみマウントできます。
  * Azure Files は、LRS および GRS のストレージ アカウントのみをサポートします。

## <a name="user-permissions"></a>ユーザーのアクセス許可
権限は、sudo アクセスのない、通常のユーザーとして設定されます。 $Home 外のインストールはすべて失われます。
`clouddrive` ディレクトリ内の `git clone` などの特定のコマンドには適切なアクセス許可がありませんが、$Home ディレクトリにはアクセス許可があります。

## <a name="browser-support"></a>ブラウザーのサポート
Cloud Shell では、Microsoft Edge、Microsoft Internet Explorer、Google Chrome、Mozilla Firefox、および Apple Safari の最新バージョンがサポートされます。 プライベート モードの Safari はサポートされません。

## <a name="copy-and-paste"></a>コピーと貼り付け
Ctrl + V キーと Ctrl + C キーは Windows コンピューターでコピー/貼り付けとして機能しません。コピー/貼り付けには、Ctrl + Insert キーと Shift + Insert キーをお使いください。
右クリックしてコピー/貼り付けのオプションを使用することもできますが、これはブラウザー固有のクリップボード アクセスに依存します。

## <a name="usage-limits"></a>Usage limits (使用状況の制限)
Cloud Shell は対話型のユース ケース向けであるため、実行時間の長い非対話型セッションは、警告なしで終了します。

## <a name="network-connectivity"></a>ネットワーク接続
Cloud Shell での待機時間はローカル インターネット接続の影響によって生じますが、Cloud Shell は送信される命令について機能しようとし続けます。

## <a name="next-steps"></a>次のステップ
[Cloud Shell のクイック スタート](quickstart.md)
