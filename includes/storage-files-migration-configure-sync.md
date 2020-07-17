---
title: Azure File Sync の構成
description: Azure File Sync を構成します。移行ドキュメント間で共有される一般的なテキスト ブロック。
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 2/20/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 562099c65273cad50596268b89055ec78d92d18a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/28/2020
ms.locfileid: "82143608"
---
この手順では、前の手順で Windows Server インスタンスに設定したすべてのリソースとフォルダーを結び付けます。

1. [Azure portal](https://portal.azure.com) にサインインします。
1. ストレージ同期サービスのリソースを見つけます。
1. 各 Azure ファイル共有のストレージ同期サービス リソース内に新しい "*同期グループ*" を作成します。 Azure File Sync の用語では、Azure ファイル共有は、同期グループの作成と共に記述する同期トポロジの "*クラウド エンドポイント*" になります。 同期グループを作成しているときは、ここで同期するファイルのセットを認識できるように、わかりやすい名前を付けます。 名前が一致する Azure ファイル共有を参照していることを確認します。
1. 同期グループが作成されると、同期グループの一覧にその行が表示されます。 名前 (リンク) を選択して、同期グループの内容を表示します。 **[クラウド エンドポイント]** の下に Azure ファイル共有が表示されます。
1. **[+ サーバー エンドポイントの追加]** ボタンを見つけます。 プロビジョニングしたローカル サーバー上のフォルダーは、この "*サーバー エンドポイント*" のパスになります。
