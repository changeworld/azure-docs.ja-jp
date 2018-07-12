---
title: インクルード ファイル
description: インクルード ファイル
services: signalr
author: wesmc7777
ms.service: signalr
ms.topic: include
ms.date: 04/17/2018
ms.author: wesmc
ms.custom: include file
ms.openlocfilehash: c26e2de2c492c51c35527979ef0450ed7c2de499
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/29/2018
ms.locfileid: "37100829"
---
1. 新しい Azure SignalR Service のリソースを作成するには、まず [Azure portal](https://portal.azure.com) にサインインします。 ページの左上で、**[+ リソースの作成]** をクリックします。 **[Marketplace を検索]** テキスト ボックスに「**SignalR Service**」と入力し、**Enter** キーを押します。

2. 結果から **[SignalR Service]** をクリックし、**[作成]** をクリックします。

3. 新しい **SignalR** の設定ページで、SignalR の新しいリソースに次の設定を追加します。

    | Name | 推奨値 | 説明 |
    | ---- | ----------------- | ----------- |
    | リソース名 | *testsignalr* | SignalR リソースに使用する一意のリソース名を入力します。 名前は 1 から 63 文字の文字列で、数字、英字、`-` 文字のみを使用する必要があります。 名前の先頭と末尾には `-` 文字を使用できません。また、連続する `-` 文字は無効です。|
    | サブスクリプション | サブスクリプションの選択 |  SignalR のテストに使用する Azure サブスクリプションを選択します。 アカウントにサブスクリプションが 1 つしかない場合は自動的に選択されるため、**[サブスクリプション]** ドロップダウンは表示されません。|
    | リソース グループ | *SignalRTestResources* という新しいリソース グループを作成する| SignalR リソースのリソース グループを選択または作成します。 このグループは複数のリソースを整理し、そのリソース グループを削除することで複数のリソースを同時削除できるため便利です。 詳細については、[リソース グループを使用した Azure リソースの管理](../articles/azure-resource-manager/resource-group-overview.md)に関するページを参照してください。 |
    | リージョン | *米国東部* | **[場所]** を使用して、SignalR リソースがホストされている地理的位置を指定します。 最高のパフォーマンスを得るには、アプリケーションの他のコンポーネントと同じリージョンにリソースを作成することをお勧めします。 |
    | [価格レベル]  | *Free* | 現時点では、**Free** と **Standard** のオプションを利用できます。 |
    | [ダッシュボードにピン留めする] | ✔ | リソースが探しやすくなるようにダッシュボードにピン留めするには、このボックスをオンにします。 |

4. **Create** をクリックしてください。 デプロイが完了するまでに数分かかる場合があります。

5. デプロイが完了したら、**[設定]** の下の **[キー]** をクリックします。 主キーの接続文字列をコピーします。 これは、後でお使いのアプリがその Azure SignalR Service のリソースを使用するように構成するために使用します。

    接続文字列は、次の形式になります。
    
        Endpoint=<service_endpoint>;AccessKey=<access_key>;
