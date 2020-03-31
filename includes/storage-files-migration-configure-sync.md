---
title: Azure File Sync の構成
description: Azure File Sync を構成します。移行ドキュメント間で共有する共通のテキスト ブロック。
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 2/20/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 53d7af6b43ff24ab12501570385162759f4c7633
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "78209330"
---
この手順では、前の手順で Windows Server に設定したすべてのリソースとフォルダーを結び付けます。

* [Azure Portal](https://portal.azure.com) にサインインします。
* ストレージ同期サービスのリソースを見つけます。
* 各 Azure ファイル共有のストレージ同期サービス リソース内に新しい "*同期グループ*" を作成します。 Azure File Sync の用語では、Azure ファイル共有は、同期グループの作成と共に記述する同期トポロジの "*クラウド エンドポイント*" になります。 同期グループを作成しているときは、ここで同期するファイルのセットを認識できるように、わかりやすい名前を付けます。 名前が一致する Azure ファイル共有を参照していることを確認します。
* 同期グループが作成されると、同期グループの一覧にその行が表示されます。 名前 (リンク) をクリックすると、同期グループの内容が表示されます。 [クラウド エンドポイント] の下に Azure ファイル共有が表示されます。
* *[+ サーバー エンドポイントの追加]* のコマンド ボタンを見つけて選択します。 プロビジョニングしたローカル サーバー上のフォルダーは、この*サーバー エンドポイント*のパスになります。
