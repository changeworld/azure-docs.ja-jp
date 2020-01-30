---
title: パートナーからセンサー データを取得する
description: この記事では、パートナーからセンサー データを取得する方法について説明します。
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 745e3f0b6c7bd4e6d984ce2df29b9965d4b8ca21
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/22/2020
ms.locfileid: "76513739"
---
# <a name="get-sensor-data-from-sensor-partners"></a>センサー パートナーからセンサー データを取得する

Azure FarmBeats を使用すると、IoT デバイスとセンサーからデータ ハブにストリーミング データを取り込むことができます。 現時点では、次のセンサー デバイス パートナーがサポートされています。

  ![FarmBeats パートナー](./media/get-sensor-data-from-sensor-partner/partner-information-2.png)

デバイス データを Azure FarmBeats と統合すると、ファームに配置されている IoT センサーから地上データをデータ ハブに取り込む際に役立ちます。 データが使用可能になったら、FarmBeats アクセラレーターを使用して視覚化できます。 FarmBeats を使用して、データをデータ融合と機械学習/人工知能 (ML/AI) モデルの構築に使用できます。

センサー データのストリーム配信を開始するには、次のことを確認します。

-  Azure Marketplace から FarmBeats をインストールしている。
-  ファームに設置するセンサーとデバイスを決定している。
-  土壌水分センサーを使用する予定がある場合は、FarmBeats の土壌水分センサー配置マップを使用して、センサーの数とセンサーを配置する必要がある正確な場所に関する推奨事項を取得します。 詳細については、「[マップを生成する](generate-maps-in-azure-farmbeats.md)」を参照してください。
- デバイス パートナーからデバイスやセンサーを購入し、ファーム内に配置している。 デバイス パートナーのソリューションを介してセンサー データにアクセスできることを確認してください。

## <a name="enable-device-integration-with-farmbeats"></a>FarmBeats とのデバイス統合を有効にする

センサー データのストリーミングを開始したら、FarmBeats システム内にデータを取得するプロセスを開始できます。 デバイス プロバイダーに次の情報を提供して、FarmBeats との統合を有効にします。

 - API エンドポイント
 - テナント ID
 - クライアント ID
 - クライアント シークレット
 - EventHub 接続文字列

上記の情報は、システム インテグレーターによって提供されます。 デバイスの統合を有効にする際に発生した問題については、システム インテグレーターにお問い合わせください。

または、Azure Cloud Shell から次のスクリプトを実行することで、資格情報を生成することもできます。 次の手順に従います。

1. [zip ファイル](https://aka.ms/farmbeatspartnerscriptv2)をダウンロードし、ローカル ドライブに展開します。 ZIP ファイル内には 1 つのファイルがあります。
2. https://portal.azure.com/ にサインインし、[Azure Active Directory] -> [アプリの登録] に進みます。

3. FarmBeats デプロイの一部として作成された アプリの登録］ をクリックします。 それは、FarmBeats データ ハブと同じ名前になります。

4. [API の公開] をクリックし、[クライアント アプリケーションの追加] をクリックし、「**04b07795-8ddb-461a-bbee-02f9e1bf7b46**」と入力し、[Authorize Scope]\(スコープの承認\) をオンにします。 これにより、Azure CLI (Cloud Shell) にアクセスして、次の手順を実行することができます。

5. Cloud Shell を開きます。 このオプションは、Azure portal の右上隅にあるツール バーで使用できます。

    ![Azure portal のツール バー](./media/get-drone-imagery-from-drone-partner/navigation-bar-1.png)

6. 環境が **[PowerShell]** に設定されていることを確認します。 既定では、[Bash] に設定されています。

    ![PowerShell ツール バーの設定](./media/get-sensor-data-from-sensor-partner/power-shell-new-1.png)

7. 手順 1 のファイルを Cloud Shell インスタンスでアップロードします。

    ![ツール バーの [アップロード] ボタン](./media/get-sensor-data-from-sensor-partner/power-shell-two-1.png)

8. ファイルがアップロードされたディレクトリに移動します。 既定では、ファイルはユーザー名の下のホーム ディレクトリにアップロードされます。

9. 次のスクリプトを実行します。 このスクリプトは、[Azure Active Directory] -> [概要] ページから取得できるテナント ID を要求します。

    ```azurepowershell-interactive 

    ./generatePartnerCredentials.ps1   

    ```

10. 画面の指示に従って、**API エンドポイント**、**テナント ID**、**クライアント ID**、**クライアント シークレット**、および **EventHub 接続文字列**の値をキャプチャします。

### <a name="integrate-device-data-by-using-the-generated-credentials"></a>生成された資格情報を使用してデバイス データを統合する

前のセクションで生成した資格情報のセットを使用して、FarmBeats にリンクされているデバイス パートナーのポータルに移動します。

 - API エンドポイント
 - EventHub 接続文字列
 - クライアント ID
 - クライアント シークレット
 - テナント ID

 デバイス プロバイダーは、正常に統合されたことを確認します。 確認後、Azure FarmBeats 上にすべてのデバイスとセンサーを表示できます。

## <a name="view-devices-and-sensors"></a>デバイスとセンサーの表示

次のセクションを使用して、ファームのデバイスとセンサーを表示します。

### <a name="view-devices"></a>デバイスの表示

現時点では、FarmBeats では次のデバイスがサポートされています。

- **ノード**:1 つ以上のセンサーが接続されているデバイス。
- **ゲートウェイ**: 1 つ以上のノードが接続されているデバイス。

次の手順に従います。

1. ホーム ページで、メニューから **[デバイス]** を選択します。
  **[デバイス]** ページには、デバイスの種類、モデル、状態、配置先のファーム、およびメタデータの最終更新日が表示されます。 既定では、[ファーム] 列には *NULL* が設定されます。 デバイスをファームに割り当てることもできます。 詳細については、「[デバイスの割り当て](#assign-devices)」を参照してください。
2. デバイスのプロパティ、テレメトリ、およびそのデバイスに接続されている子デバイスを表示するデバイスを選択します。

    ![[デバイス] ページ](./media/get-sensor-data-from-sensor-partner/view-devices-1.png)

### <a name="view-sensors"></a>センサーの表示

次の手順に従います。

1. ホーム ページで、メニューから **[センサー]** を選択します。
  **[センサー]** ページには、センサーの種類、接続先のファーム、親デバイス、ポート名、ポートの種類、および最終的に更新された状態に関する詳細が表示されます。
2. センサーのプロパティ、アクティブなアラート、およびセンサーからのテレメトリを表示するセンサーを選択します。

    ![[センサー] ページ](./media/get-sensor-data-from-sensor-partner/view-sensors-1.png)

## <a name="assign-devices"></a>デバイスの割り当て  

センサー データが流れるようになったら、センサーを配置したファームにそれを割り当てることができます。

1. ホーム ページで、メニューから **[ファーム]** を選択します。 **ファーム**の一覧ページが表示されます。
2. デバイスを割り当てるファームを選択し、 **[デバイスの追加]** を選択します。
3. **[デバイスの追加]** ウィンドウが表示されます。 ファームに割り当てるデバイスを選択します。

    ![[デバイスの追加] ウィンドウ](./media/get-sensor-data-from-sensor-partner/add-devices-1.png)

4. **[Add Devices]\(デバイスの追加\)** を選択します。 または、 **[デバイス]** メニューに移動し、ファームに割り当てるデバイスを選択し、 **[デバイスの関連付け]** を選択します。
5. **[デバイスの関連付け]** ウィンドウで、ドロップダウン リストからファームを選択し、 **[すべてに適用]** を選択して、選択したすべてのデバイスにファームを関連付けます。

    ![[デバイスの関連付け] ウィンドウ](./media/get-sensor-data-from-sensor-partner/associate-devices-1.png)

6. 各デバイスを異なるファームに関連付けるには、 **[ファームに割り当て]** 列のドロップダウン矢印を選択し、デバイス行ごとにファームを選択します。
7. **[割り当て]** を選択して、デバイスの割り当てを完了します。

### <a name="visualize-sensor-data"></a>センサー データの視覚化

次の手順に従います。

1. ホーム ページのメニューから **[Farms]\(ファーム\)** を選択し、 **[Farms]\(ファーム\)** ページを表示します。
2. センサー データを表示する**ファーム**を選択します。
3. **[Farm]\(ファーム\)** ダッシュボードで、テレメトリ データを確認できます。 ライブ テレメトリを表示したり、 **[ユーザー設定の範囲]** を使用して特定の日付範囲で表示したりできます。

    ![ファーム ダッシュボード](./media/get-sensor-data-from-sensor-partner/telemetry-data-1.png)

## <a name="delete-a-sensor"></a>センサーを削除する

次の手順に従います。

1. ホーム ページのメニューから **[センサー]** を選択し、 **[センサー]** ページを表示します。
2. 削除するデバイスを選択し、確認ウィンドウで **[削除]** を選択します。

    ![[削除] ボタン](./media/get-sensor-data-from-sensor-partner/delete-sensors-1.png)

確認メッセージでは、センサーが正常に削除されたことが示されます。

## <a name="delete-devices"></a>デバイスの削除

次の手順に従います。

1. ホーム ページで、 **[デバイス]** を選択して、 **[デバイス]** ページを表示します。
2. 削除するデバイスを選択し、確認ウィンドウで **[削除]** を選択します。

    ![[削除] ボタン](./media/get-sensor-data-from-sensor-partner/delete-device-1.png)

## <a name="next-steps"></a>次のステップ

これで、Azure FarmBeats インスタンスにセンサー データが流れるようになりました。 次に、ファームの[マップを生成する](generate-maps-in-azure-farmbeats.md#generate-maps)方法について説明します。
