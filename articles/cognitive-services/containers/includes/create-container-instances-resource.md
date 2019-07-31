---
title: コンテナー サポート
titleSuffix: Azure Cognitive Services
description: Azure コンテナー インスタンス リソースを作成する方法を説明します。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 7/3/2019
ms.author: dapine
ms.openlocfilehash: 05284d434e6bd22fd50957f7cc5ec966f88a4fd4
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/15/2019
ms.locfileid: "68229188"
---
## <a name="create-an-azure-container-instance-resource"></a>Azure コンテナー インスタンス リソースを作成する

1. コンテナー インスタンスの[作成](https://ms.portal.azure.com/#create/Microsoft.ContainerInstances)のページに移動します。

2. **[基本]** タブで、以下の詳細を入力します。

    |Setting|値|
    |--|--|
    |Subscription|サブスクリプションを選択します。|
    |Resource group|利用可能なリソース グループを選択するか、新しいリソース グループを作成します (`cognitive-services` など)。|
    |コンテナー名|`cognitive-container-instance` などの名前を入力します。 この名前は小文字である必要があります。|
    |Location|デプロイのためのリージョンを選択します。|
    |イメージの種類|`Public`|
    |イメージ名|Cognitive Services コンテナーの場所を入力します。 場所は `docker pull` コマンドで使用するものと同じでも構いません。使用可能なイメージ名と対応するリポジトリについては、[コンテナーのリポジトリとイメージ](../../cognitive-services-container-support.md#container-repositories-and-images)を参照してください。|
    |OS の種類|`Linux`|
    |Size|特定の Cognitive Service コンテナーに対して推奨されるサイズに変更します。<br>2 CPU コア<br>4 GB

3. **[ネットワーク]** タブで、以下の詳細を入力します。

    |Setting|値|
    |--|--|
    |Port|TCP ポートを `5000` に設定します。 ポート 5000 上でコンテナーを公開します。|

4. **[詳細]** タブで、ACI リソースのコンテナー課金設定に必要な**環境変数**を入力します。

    | キー | 値 |
    |--|--|
    |`apikey`|リソースの **[キー]** ページからコピーされます。 スペースやダッシュを含まない 32 文字の英数字文字列 (`xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`) です。|
    |`billing`|リソースの **[概要]** ページからコピーされます。|
    |`eula`|`accept`|

1. **[確認と作成]** をクリックします
1. 検証に合格した後、 **[作成]** をクリックして作成プロセスを終了します
1. リソースが正常にデプロイされると、準備完了です