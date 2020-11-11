---
title: Azure File Sync を構成する
description: Azure File Sync を構成します。移行ドキュメント間で共有される一般的なテキスト ブロック。
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 2/20/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 64b99976a306c3c8423f5115c95a15158a3ddb51
ms.sourcegitcommit: 4f4a2b16ff3a76e5d39e3fcf295bca19cff43540
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/30/2020
ms.locfileid: "93043163"
---
この手順では、前の手順で Windows Server インスタンスに設定したすべてのリソースとフォルダーを結び付けます。

1. [Azure portal](https://portal.azure.com) にサインインします。
1. ストレージ同期サービスのリソースを見つけます。
1. 各 Azure ファイル共有のストレージ同期サービス リソース内に新しい " *同期グループ* " を作成します。 Azure File Sync の用語では、Azure ファイル共有は、同期グループの作成と共に記述する同期トポロジの " *クラウド エンドポイント* " になります。 同期グループを作成しているときは、ここで同期するファイルのセットを認識できるように、わかりやすい名前を付けます。 名前が一致する Azure ファイル共有を参照していることを確認します。
1. 同期グループが作成されると、同期グループの一覧にその行が表示されます。 名前 (リンク) を選択して、同期グループの内容を表示します。 **[クラウド エンドポイント]** の下に Azure ファイル共有が表示されます。
1. **[+ サーバー エンドポイントの追加]** ボタンを見つけます。 プロビジョニングしたローカル サーバー上のフォルダーは、この " *サーバー エンドポイント* " のパスになります。
