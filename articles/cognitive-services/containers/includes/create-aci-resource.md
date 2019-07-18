---
title: コンテナー サポート
titleSuffix: Azure Cognitive Services
description: Azure コンテナー インスタンス (ACI) リソースを作成する方法を説明します。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 06/26/2019
ms.author: dapine
ms.openlocfilehash: b24a78873c3220b969fc548d2553e465e80773cd
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2019
ms.locfileid: "67455068"
---
## <a name="create-an-azure-container-instance-aci-resource"></a>Azure コンテナー インスタンス (ACI) リソースを作成する

1. コンテナー インスタンスの[作成](https://ms.portal.azure.com/#create/Microsoft.ContainerInstances)のページに移動します。

2. **[基本]** タブで、以下の詳細を入力します。

    |Setting|値|
    |--|--|
    |サブスクリプション|サブスクリプションを選択します。|
    |リソース グループ|利用可能なリソース グループを選択するか、新しいリソース グループを作成します (`cognitive-services` など)。|
    |コンテナー名|`cognitive-container-instance` などの名前を入力します。 この名前は小文字である必要があります。|
    |Location|デプロイのためのリージョンを選択します。|
    |イメージの種類|`Public`|
    |イメージ名|Cognitive Services コンテナーの場所を入力します。 これは、`docker pull` コマンドで使用した場所と同じ場所にすることができます。例: __ <br>`mcr.microsoft.com/azure-cognitive-services/sentiment`|
    |OS の種類|`Linux`|
    |Size|特定の Cognitive Service コンテナーに対して推奨されるサイズに変更します。<br>2 コア<br>4 GB

3. **[ネットワーク]** タブで、以下の詳細を入力します。

    |Setting|値|
    |--|--|
    |Port|TCP ポートを `5000` に設定します。 ポート 5000 上でコンテナーを公開します。|

4. **[詳細設定]** タブで以下の詳細を入力して、コンテナーの[必要な課金設定](https://docs.microsoft.com/azure/cognitive-services/text-analytics/how-tos/text-analytics-how-to-install-containers#billing-arguments)を ACI リソースにパススルーします。

    |高度なページのキー|高度なページの値|
    |--|--|
    |`apikey`|Text Analytics リソースの **[キー]** ページからコピーされます。 スペースやダッシュを含まない 32 文字の英数字文字列 (`xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`) です。|
    |`billing`|Text Analytics リソースの **[概要]** ページからコピーされます。 例: `https://northeurope.api.cognitive.microsoft.com/text/analytics/v2.0`|
    |`eula`|`accept`|

1. **[確認と作成]** をクリックします
1. 検証に合格した後、 **[作成]** をクリックして作成プロセスを終了します
1. リソースが正常にデプロイされると、使用できるようになります