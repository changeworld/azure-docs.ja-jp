---
title: ドローンの画像を取得する
description: パートナーからドローンの画像を取得する方法について説明します
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 6ee4391369744f966ce273697e5ba9e7f0590edd
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/09/2019
ms.locfileid: "73890991"
---
# <a name="get-drone-imagery-from-drone-partners"></a>ドローン パートナーからドローンの画像を取得する

この記事では、ドローン画像パートナーから Azure FarmBeats データ ハブにオルソモザイク データを取り込む方法について説明します。 オルソモザイクとは、ドローンによって収集されたデータから幾何学的に修正され合成される、航空写真のイラスト/画像です。

現在は、次の画像パートナーがサポートされています。

  ![プロジェクト (Farm Beats)](./media/get-drone-imagery-from-drone-partner/drone-partner-1.png)

ドローンの画像データを Azure FarmBeats と統合すると、ファームで実施するドローン飛行から得られたオルソモザイク データをデータ ハブに取得するのに役立ちます。 データは、使用可能になると、FarmBeats Accelerator に表示できるようになります。また、データ融合や AI/ML モデルの構築に使用できるようになります。

## <a name="before-you-begin"></a>開始する前に

  - Azure FarmBeats をデプロイしていることを確認します。 デプロイするには、「[FarmBeats のデプロイ](prepare-for-deployment.md)」を参照してください。
  - FarmBeats システムで、(ドローン画像を必要とする) ファームが定義されていることを確認します。

## <a name="enable-drone-imagery-integration-with-farmbeats"></a>FarmBeats とのドローン画像の統合を有効にする   

FarmBeats との統合を有効にするには、デバイス プロバイダーに次の情報を提供する必要があります。  
 - API エンドポイント  
 - テナント ID  
 - クライアント ID  
 - クライアント シークレット  

次の手順に従います。

1. この[スクリプト](https://aka.ms/farmbeatspartnerscript)をダウンロードし、ローカル ドライブに展開します。 この ZIP ファイル内には、2 つのファイルがあります。  
2. [Azure portal](https://portal.azure.com/) にサインインし、Cloud Shell を開きます (このオプションは、ポータルの右上のバーで利用できます)。   

    ![プロジェクト (Farm Beats)](./media/get-drone-imagery-from-drone-partner/navigation-bar-1.png)

3. 環境が **[PowerShell]** に設定されていることを確認します。

    ![プロジェクト (Farm Beats)](./media/get-drone-imagery-from-drone-partner/power-shell-new-1.png)

4. Cloud Shell に、(上記の手順 1 で) ダウンロードした 2 つのファイルをアップロードします。  

    ![プロジェクト (Farm Beats)](./media/get-drone-imagery-from-drone-partner/power-shell-two-1.png)

5. ファイルがアップロードされたディレクトリに移動します (既定では、ホーム ディレクトリ > <ユーザー名> にアップロードされます)。  
6. 次のスクリプトを実行します。

    ```azurepowershell-interactive 

    ./generateCredentials.ps1   

    ```

7. 画面の指示に従って、API エンドポイント、テナント ID、クライアント ID、クライアント シークレット、および EventHub 接続文字列の値をキャプチャします。

    必要な資格情報をパートナーのドローン ソフトウェア システムに入力すると、FarmBeats システムからすべてのファームをインポートし、ファームの詳細情報を使用して飛行経路の計画やドローン画像の収集を実行できるようになります。

    ドローン プロバイダーのソフトウェアで未加工画像が処理された後、合成されたオルソモザイク画像やその他の処理済み画像がドローン ソフトウェア システムによってデータ ハブにアップロードされます。

## <a name="view-drone-imagery"></a>ドローンの画像を表示する

データが FarmBeats データ ハブに送信されると、FarmBeats データ ハブ API シリーズを使用してシーン ストアに対してクエリを実行できるようになります。

または、 **[Farm Details]\(ファームの詳細\)** ページで最新のドローン画像を表示することもできます。 表示するには、次の手順に従います。  

1. 画像がアップロードされたファームを選択します。 **[Farm Details]\(ファームの詳細\)** ページが表示されます。
2. **[Latest Precision Maps]\(最新のプレシジョン マップ\)** セクションまで下にスクロールします。
3. **[Drone Imagery]\(ドローンの画像\)** セクションで画像を確認できます。

    ![プロジェクト (Farm Beats)](./media/get-drone-imagery-from-drone-partner/drone-imagery-1.png)

## <a name="download-drone-imagery"></a>ドローンの画像をダウンロードする

[Drone Imagery]\(ドローンの画像\) セクションを選択すると、ポップアップが開き、ドローンを使った高解像度のオルソモザイク画像が表示されます。

![プロジェクト (Farm Beats)](./media/get-drone-imagery-from-drone-partner/download-drone-imagery-1.png)

## <a name="view-all-drone-maps"></a>すべてのドローン マップを表示する

ドローン プロバイダーによってアップロードされたファイルと画像は、[Maps]\(マップ\) セクションに表示されます。 **[Maps]\(マップ\)** セクションを選択して、 **[Farm]\(ファーム\)** でフィルター処理し、表示およびダウンロードする適切なファイルを選択します。

  ![プロジェクト (Farm Beats)](./media/get-drone-imagery-from-drone-partner/view-drone-maps-1.png)

## <a name="next-steps"></a>次の手順

FarmBeats データ ハブ [API](references-for-farmbeats.md#rest-api) シリーズを使用して、ドローンの画像を取得する方法について説明します。
