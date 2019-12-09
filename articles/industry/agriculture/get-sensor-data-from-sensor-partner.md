---
title: パートナーからセンサー データを取得する
description: パートナーからセンサー データを取得する方法について説明します
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: b985dfc1f16372c3fad1b0a5c0894931b4c15dcc
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/22/2019
ms.locfileid: "74406482"
---
# <a name="get-sensor-data-from-sensor-partners"></a>センサー パートナーからセンサー データを取得する

Azure FarmBeats を使用すると、IoT デバイスとセンサーからデータ ハブにストリーミング データを取り込むことができます。 現在は、次のセンサー デバイス パートナーがサポートされています。

  ![プロジェクト (Farm Beats)](./media/get-sensor-data-from-sensor-partner/partner-information-1.png)

デバイス データを Azure FarmBeats と統合すると、ファームに配置されている IoT センサーから地上データをデータ ハブに取り込む際に役立ちます。 使用可能になったデータは、FarmBeats Accelerator を通じて視覚化できるほか、FarmBeats を使用したデータ融合や AI および ML モデルの構築に使用することもできます。

センサー データのストリーム配信を開始するには、次のことを確認します。

-  Azure Marketplace から FarmBeats をインストールしている。
-  ファームに設置するセンサーとデバイスを決定している。
-  土壌水分センサーを使用する予定がある場合は、FarmBeats の土壌水分センサー配置マップを使用すると、センサーの数とセンサーを配置する必要がある正確な場所に関する推奨事項を取得できます。 詳細については、「[マップを生成する](generate-maps.md)」を参照してください。

- デバイス パートナーからデバイスやセンサーを購入し、ファーム内に配置する。 デバイス パートナーのソリューションを介してセンサー データにアクセスできることを確認してください。

### <a name="enable-device-integration-with-farmbeats"></a>FarmBeats とのデバイス統合を有効にする   

センサー データのストリーム配信を開始したら、FarmBeats システムにデータを取得するプロセスを開始できます。 FarmBeats との統合を有効にするには、デバイス プロバイダーに次の情報を提供する必要があります。  

 - API エンドポイント  
 - テナント ID  
 - クライアント ID  
 - クライアント シークレット  
 - EventHub 接続文字列

上記の情報は、システム インテグレーターによって提供されます。 デバイスの統合を有効にする際に発生した問題については、システム インテグレーターにお問い合わせください。

または、Azure Cloud Shell から次のスクリプトを実行することで、資格情報を生成することもできます。 次の手順に従ってください。

1. [ZIP ファイル](https://aka.ms/farmbeatspartnerscript)をダウンロードし、ローカル ドライブに展開します。 この ZIP ファイル内には 2 つのファイルがあります。
2. https://portal.azure.com/ にサインインし、Cloud Shell を開きます (このオプションは、ポータルの右上のバーで利用できます)。  

    ![プロジェクト (Farm Beats)](./media/get-drone-imagery-from-drone-partner/navigation-bar-1.png)

3. 環境が **[PowerShell]** に設定されていることを確認します。既定では、[Bash] に設定されています。

    ![プロジェクト (Farm Beats)](./media/get-sensor-data-from-sensor-partner/power-shell-new-1.png)

4. Cloud Shell に、(上記の手順 1 の) 2 つのファイルをアップロードします。

    ![プロジェクト (Farm Beats)](./media/get-sensor-data-from-sensor-partner/power-shell-two-1.png)

5. ファイルがアップロードされたディレクトリに移動します (既定では、ホーム ディレクトリ > <ユーザー名> にアップロードされます)。
6. 次のスクリプトを実行します。

    ```azurepowershell-interactive 

    ./generateCredentials.ps1   

    ```
7. 画面の指示に従って、値をキャプチャします (API エンドポイント、テナント ID、クライアント ID、クライアント シークレット、EventHub 接続文字列)。 EventHub 接続文字列は、Swagger で API 応答の一部として確認できます。

**生成された資格情報を使用してデバイス データを統合する**

前のセクションで生成した資格情報のセットを使用して FarmBeats をリンクするには、デバイス パートナーのポータルにアクセスします。

 - API エンドポイント  
 - EventHub 接続文字列  
 - クライアント ID  
 - クライアント シークレット  
 - テナント ID  

 デバイス プロバイダーは、正常に統合されたことを確認します。 確認後、Azure FarmBeats 上にすべてのデバイスとセンサーを表示できます。

## <a name="view-devices-and-sensors"></a>デバイスとセンサーの表示

次のセクションを使用して、ファーム内のデバイスとセンサーを表示します。

### <a name="view-devices"></a>デバイスの表示

現在、FarmBeats では次のデバイスがサポートされています。

- **ノード**: 1 つ以上のセンサーが接続されているデバイス。
- **ゲートウェイ**: 1 つ以上のノードが接続されているデバイス。

次の手順に従います。

1. ホーム ページで、メニューから **[デバイス]** を選択します。
  [デバイス] ページには、デバイスの種類、モデル、状態、配置先のファーム、およびメタデータの最終更新日が表示されます。 既定では、[FARM]\(ファーム\) 列は NULL に設定されます。 デバイスをファームに割り当てることもできます。 詳細については、「[デバイスの割り当て](#assign-devices)」を参照してください。
2. デバイスを選択すると、デバイスのプロパティ、テレメトリ、およびそのデバイスに接続されている子デバイスが表示されます。  

    ![プロジェクト (Farm Beats)](./media/get-sensor-data-from-sensor-partner/view-devices-1.png)

### <a name="view-sensors"></a>センサーの表示

次の手順に従います。

1. ホーム ページで、メニューから **[センサー]** を選択します。
  [センサー] ページには、センサーの種類、接続先のファーム、親デバイス、ポート名、ポートの種類、および最終的に更新された状態に関する詳細が表示されます。
2. センサーを選択すると、センサーのプロパティ、アクティブなアラート、センサーからのテレメトリが表示されます。

    ![プロジェクト (Farm Beats)](./media/get-sensor-data-from-sensor-partner/view-sensors-1.png)

## <a name="assign-devices"></a>デバイスの割り当て  

センサー データが流れるようになったら、センサーを配置したファームに割り当てることができます。

1. ホーム ページで、メニューから **[Farms]\(ファーム\)** を選択すると、 **[Farms]\(ファーム\)** の一覧ページが表示されます。  
2. デバイスを割り当てるファームを選択し、 **[Add Devices]\(デバイスの追加\)** を選択します。  
3. **[Add Devices]\(デバイスの追加\)** ウィンドウが表示されます。 ファームに割り当てるデバイスを選択します。

    ![プロジェクト (Farm Beats)](./media/get-sensor-data-from-sensor-partner/add-devices-1.png)

4. **[Add Devices]\(デバイスの追加\)** を選択します。 または、 **[デバイス]** メニューに移動して、ファームに割り当てるデバイスを選択し、 **[Associate Devices]\(デバイスの関連付け\)** を選択します。  
5. **[Associate Devices]\(デバイスの関連付け\)** ウィンドウで、ドロップダウンからファームを選択し、 **[すべてに適用]** を選択して、選択したすべてのデバイスにファームを関連付けます。

    ![プロジェクト (Farm Beats)](./media/get-sensor-data-from-sensor-partner/associate-devices-1.png)

6. 各デバイスを異なるファームに関連付けるには、 **[Assign to Farm]\(ファームに割り当て\)** 列のドロップダウンを選択し、デバイス行ごとにファームを選択します。  
7. **[割り当て]** を選択して、デバイスの割り当てを完了します。

### <a name="visualize-sensor-data"></a>センサー データの視覚化

次の手順に従います。

1. ホーム ページのメニューから **[Farms]\(ファーム\)** を選択し、 **[Farms]\(ファーム\)** ページを表示します。  
2. センサー データを表示する**ファーム**を選択します。  
3. **[Farm]\(ファーム\)** ダッシュボードで、テレメトリ データを確認できます。 ライブ テレメトリを表示したり、 **[Custom Range]\(ユーザー設定の範囲\)** を使用して特定の日付範囲で表示したりできます。

    ![プロジェクト (Farm Beats)](./media/get-sensor-data-from-sensor-partner/telemetry-data-1.png)

## <a name="delete-sensor"></a>センサーの削除

次の手順に従います。

1. ホーム ページのメニューから **[センサー]** を選択し、 **[センサー]** ページを表示します。  
2. 削除するデバイスを選択し、確認ウィンドウで **[削除]** を選択します。

    ![プロジェクト (Farm Beats)](./media/get-sensor-data-from-sensor-partner/delete-sensors-1.png)

確認メッセージでは、センサーが正常に削除されたことが示されます。  

## <a name="delete-devices"></a>デバイスの削除

次の手順に従います。

1. ホーム ページのメニューから **[デバイス]** を選択し、デバイス ページを表示します。  
2. 削除するデバイスを選択し、確認ウィンドウで **[削除]** を選択します。

    ![プロジェクト (Farm Beats)](./media/get-sensor-data-from-sensor-partner/delete-device-1.png)

## <a name="next-steps"></a>次の手順

これで、Azure FarmBeats インスタンスにセンサー データが流れるようになりました。 次に、ファームの[マップを生成する](generate-maps.md#generate-maps)方法について説明します。
