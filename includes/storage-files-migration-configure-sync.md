---
title: インクルード ファイル
description: インクルード ファイル
services: storage
author: fauhse
ms.service: storage
ms.topic: include
ms.date: 2/20/2020
ms.author: fauhse
ms.custom: include file
ms.openlocfilehash: f4baa89757d4ae93af8c1067cf6ef4617ce95e5a
ms.sourcegitcommit: ba8f0365b192f6f708eb8ce7aadb134ef8eda326
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/08/2021
ms.locfileid: "109645173"
---
このステップでは、前の手順で Windows Server インスタンスに設定したすべてのリソースとフォルダーを結び付けます。

1. [Azure portal](https://portal.azure.com) にサインインします。
1. ストレージ同期サービスのリソースを見つけます。
1. 各 Azure ファイル共有のストレージ同期サービス リソース内に新しい "*同期グループ*" を作成します。 Azure File Sync の用語では、Azure ファイル共有は、同期グループの作成と共に記述する同期トポロジの "*クラウド エンドポイント*" になります。 同期グループを作成する際に、ここで同期するファイルのセットを認識できるように、わかりやすい名前を付けます。 名前が一致する Azure ファイル共有を参照していることを確認します。
1. 同期グループを作成すると、同期グループの一覧にその行が表示されます。 名前 (リンク) を選択して、同期グループの内容を表示します。 **[クラウド エンドポイント]** の下に Azure ファイル共有が表示されます。
1. **[サーバー エンドポイントの追加]** ボタンを見つけます。 プロビジョニングしたローカル サーバー上のフォルダーは、この "*サーバー エンドポイント*" のパスになります。
