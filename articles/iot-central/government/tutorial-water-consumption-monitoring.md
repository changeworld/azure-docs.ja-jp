---
title: チュートリアル:Azure IoT Central によって水消費量のモニタリング アプリを作成する
description: チュートリアル:Azure IoT Central アプリケーション テンプレートを使用して水消費量のモニタリング アプリケーションを作成する方法について説明します。
author: miriambrus
ms.author: miriamb
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: 1bad4ab6320e757ac766776a95b8dbe6ebaa3259
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/05/2020
ms.locfileid: "77016427"
---
# <a name="tutorial-create-a-water-consumption-monitoring-application-in-iot-central"></a>チュートリアル:IoT Central で水消費量のモニタリング アプリケーションを作成する



このチュートリアルでは、Azure IoT Central の水消費量のモニタリング アプリケーション テンプレートから IoT Central の水消費量のモニタリング アプリケーションを作成する方法について説明します。 

このチュートリアルでは、次の内容を学習します。 

> [!div class="checklist"]
> * Azure IoT Central の**Water consumption monitoring (水消費量のモニタリング)** テンプレートを使用して、水消費量のモニタリング アプリケーションを作成する
> * オペレーター ダッシュボードの探索とカスタマイズ 
> * デバイス テンプレートの探索
> * シミュレートされたデバイスを探索する
> * ルールを探索して構成する
> * ジョブの構成
> * ホワイトラベリングを使用してアプリケーション ブランドをカスタマイズする

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、次のものが必要です。
-  Azure サブスクリプションをお勧めします。 Azure サブスクリプションがない場合は、[Azure サインアップ ページ](https://aka.ms/createazuresubscription)で作成できます。

## <a name="create-water-consumption-monitoring-app-in-iot-central"></a>IoT Central で水消費量のモニタリング アプリを作成する

このセクションでは、Azure IoT Central の **Water consumption monitoring template (水消費量のモニタリング テンプレート)** を使用して、IoT Central で水消費量のモニタリング アプリケーションを作成します。

新しい Azure IoT Central の水消費量のモニタリング アプリケーションを作成するには:  

1. [Azure IoT Central ホームページ](https://aka.ms/iotcentral) Web サイトに移動します。

    Azure サブスクリプションを持っている場合は、そのサブスクリプションへのアクセス用の資格情報を使用してサインインします。持っていない場合は、Microsoft アカウントを使用してサインインします。

    ![所属組織のアカウントを入力](media/tutorial-waterconsumptionmonitoring/sign-in.png)

2. 左側のウィンドウで **[ビルド]** をクリックし、 **[政府機関]** タブを選択します。政府機関ページに、いくつかの政府機関アプリケーション テンプレートが表示されます。

   ![政府機関アプリ テンプレートを構築する](./media/tutorial-waterconsumptionmonitoring/iotcentral-government-tab-overview1.png)

1. **Water consumption monitoring (水消費量のモニタリング)** アプリケーション テンプレートを選択します。 このテンプレートには、サンプルの水消費デバイス テンプレート、シミュレートされたデバイス、オペレーター ダッシュボード、事前構成済みのモニタリング ルールが含まれています。    

2. **[アプリの作成]** をクリックすると、次のフィールドがある **[新しいアプリケーション]** 作成フォームが開きます。
    * **アプリケーション名**: 既定で、アプリケーションでは、*Water consumption monitoring* と、その後に IoT Central が生成する一意の ID 文字列が使われます。 必要に応じて、わかりやすいアプリケーション名を選択します。 後でアプリケーション名を変更することもできます。
    * **URL**: IoT Central によって、アプリケーション名に基づいて、URL が自動生成されます。 好みに合わせて URL を更新することができます。 URL は後で変更することもできます。 
    * Azure サブスクリプションをお持ちの場合は、"*ディレクトリ、Azure サブスクリプション、リージョン*" を入力します。 サブスクリプションをお持ちでない場合は、**7 日間の無料試用版**を有効にし、必須の連絡先情報を入力できます。  

    ディレクトリとサブスクリプションの詳細については、[アプリケーションの作成のクイック スタート](../core/quick-deploy-iot-central.md)に関するページを参照してください。

5. ページの下部にある **[作成]** ボタンをクリックします。 

    ![Azure IoT Central の [Create Application]\(アプリケーションの作成\) ページ](./media/tutorial-waterconsumptionmonitoring/new-application-waterconsumptionmonitoring.png)

    ![Azure IoT Central のアプリケーションの作成の課金情報](./media/tutorial-waterconsumptionmonitoring/new-application-waterconsumptionmonitoring-billinginfo.png)

6. これで、Azure IoT Central の **Water consumption monitoring (水消費量のモニタリング)** テンプレートを使用して、水消費量のモニタリング アプリを作成できました。

お疲れさまでした。 水質のモニタリング アプリケーションの作成が完了しました。これには、次のものが事前構成されて付属しています。
* オペレーター ダッシュボードのサンプル
* 定義済みの水流量とバルブ デバイス テンプレートのサンプル
* シミュレートされた水流量とスマート バルブ デバイス
* 事前構成済みの規則とジョブ
* ホワイト ラベルを使用したブランド化のサンプル 

これは自分のアプリケーションであるため、いつでも変更できます。 ここで、アプリケーションを探索して、いくつかのカスタマイズを行いましょう。  

## <a name="explore-and-customize-operator-dashboard"></a>オペレーター ダッシュボードの探索とカスタマイズ 
アプリケーションの作成後、**Wide World water consumption monitoring dashboard (Wide World water 水消費量のモニタリング ダッシュボード)** というサンプル オペレーター ダッシュボードに移動します。

   ![水消費量のモニタリング ダッシュボード](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-dashboardfull.png)

ビルダーは、オペレーターのダッシュボードのビューを作成したりカスタマイズしたりすることができます。 カスタマイズしてみる前に、ダッシュボードを探索しましょう。 

> [!NOTE]
> ダッシュボードに表示されるすべてのデータは、シミュレートされたデバイス データに基づいています。これについては、次のセクションで詳しく説明します。 
  
ダッシュボードは、さまざまな種類のタイルで構成されています。

* **Wide World Water utility image tile (Wide World Water 公益事業イメージ タイル)** : ダッシュボードの最初のタイルは、架空の公益事業 "Wide World Water" のイメージ タイルです。 タイルをカスタマイズしたり、独自のイメージを追加したり、削除したりできます。 

* **Average water flow KPI tile (平均水流量 KPI タイル)** : たとえば*過去 30 分間の平均*を表示するように KPI タイルが構成されています。 KPI タイルをカスタマイズし、別の種類や時間の範囲に設定できます。

* ダッシュボードの右側に、**Close valve (バルブを閉じる)** 、**Open valve (バルブを開く)** 、**Set valve position (バルブの位置を設定する)** ための *Device Command (デバイス コマンド)* タイルがあります。 コマンドをクリックすると、シミュレートされたデバイスのコマンド ページに移動します。 IoT Central で、"*コマンド*" は "*デバイス機能*" の種類であり、これについては、このチュートリアルの**デバイス テンプレートのセクション**で後ほど説明します。

*  **Water distribution area map (配水区域マップ)** : マップでは Azure Maps が使用されており、Azure IoT Central で直接構成できます。 マップ タイルにデバイスの場所が表示されています。 マップの上にポインターを置いて、マップ上で*ズームイン*、*ズームアウト*、*展開*などのコントロールを試してみてください。 

    ![水消費量のモニタリング ダッシュボードのマップ](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-dashboard-map.png)

* **Average water flow line chart (平均水流量折れ線グラフ)** と **Environment conditions line chart (環境条件折れ線グラフ)** : 1 つまたは複数のデバイス テレメトリを、目的の時間範囲での折れ線グラフとしてプロットして視覚化できます。  

* **Average valve pressure heatmap chart (平均バルブ圧力ヒートマップ グラフ)** : 色インデックスを使用して、ある時間範囲での分布を確認することに興味があるデバイス テレメトリ データのヒートマップ視覚化の種類を選択できます。

* **Reset alert threshold content tile (アラートしきい値のリセット コンテンツ タイル)** : アクション ページへのリンクを埋め込むアクション コンテンツ タイルへの呼び出しを含めることができます。 この例では、アラートしきい値のリセットによって、アプリケーション **Jobs** に移動し、そこでデバイスのプロパティの更新を実行できます。これについては、このチュートリアルの**ジョブの構成**セクションで後ほど説明します。

* **Property tiles (プロパティ タイル)** : ダッシュボードには、**Valve operational info (バルブ操作情報)** 、**Flow alert thresholds (水流量アラートしきい値)** 、および **Maintenance info (メンテナンス情報)** が表示されます。 


### <a name="customize-dashboard"></a>ダッシュボードのカスタマイズ 

ビルダーは、オペレーターのダッシュボードのビューをカスタマイズできます。 次を行うことができます。
1. **[編集]** をクリックして、**Wide World water consumption monitoring dashboard (Wide World water 水消費量モニタリング ダッシュボード)** をカスタマイズします。 ダッシュボードをカスタマイズするには、 **[編集]** メニューをクリックします。 ダッシュボードが**編集**モードになったら、新しいタイルを追加したり、構成したりできます 

     ![ダッシュボードの編集](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-edit-dashboard.png)

2. 新しいダッシュボードを作成し、最初から構成するには、 **[+ 新規]** をクリックします。 複数のダッシュボードを作成し、ダッシュボード メニューからダッシュボード間を移動できます。 

## <a name="explore-device-template"></a>デバイス テンプレートの探索
Azure IoT Central のデバイス テンプレートは、デバイスの機能を定義します。これは、テレメトリ、プロパティ、コマンドなどになります。 ビルダーは、IoT Central で、接続するデバイスの機能を表す 1 つまたは複数のデバイス テンプレートを定義できます。 
 

**Water consumption monitoring (水消費量のモニタリング)** アプリケーションには、*流量計*と*スマート バルブ* デバイスを表す 2 つの参照デバイス テンプレートが付属しています。 

デバイス テンプレートを表示するには:

1. IoT Central で、アプリケーションの左側のナビゲーション ウィンドウにある **[デバイス テンプレート]** をクリックします。 
    [デバイス テンプレート] リストに、2 つのデバイス テンプレート **Flow meter (流量計)** と **Smart Valve (スマート バルブ)** が表示されます

   ![デバイス テンプレート](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-devicetemplate.png)

2. **[Flow meter]\(流量計\)** デバイス テンプレートをクリックして、デバイスの機能を理解します。 

     ![デバイス テンプレート Flow meter (流量計)](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-devicetemplate-flowmeter.png)

### <a name="customizing-the-device-template"></a>デバイス テンプレートのカスタマイズ

次のカスタマイズを行います。
1. デバイス テンプレート メニューから **[カスタマイズ]** に移動します
2. `Temperature` テレメトリの種類を見つけます
3. `Temperature` の**表示名**を `Reported temperature` に更新します
4. 測定単位を更新するか、*最小値*と*最大値*を設定します
5. すべての変更を**保存**します 

### <a name="add-a-cloud-property"></a>クラウド プロパティを追加する 
1. デバイス テンプレート メニューから **[クラウド プロパティ]** に移動します。
2. **[+ クラウド プロパティの追加]** をクリックして、新しいクラウド プロパティを追加します。 
    IoT Central では、デバイスに関連するプロパティを追加できます。 たとえば、クラウドのプロパティは、設置区域、アセット情報、その他のメンテナンス情報などに固有のアラートのしきい値などになります。 
3. すべての変更を**保存**します 
 
### <a name="views"></a>ビュー 
水消費量モニター デバイス テンプレートには、事前定義されたビューが付属しています。 ビューを調べて、更新を行うことができます。 ビューでは、オペレーターにデバイス データがどのように表示されるかを定義しますが、クラウドのプロパティも入力します。 

  ![デバイス テンプレートのビュー](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-devicetemplate-views.png)

### <a name="publish"></a>発行 
なんらかの変更を行った場合は、必ずデバイス テンプレートを**発行**してください。 

### <a name="create-a-new-device-template"></a>新しいデバイス テンプレートの作成 
- 新しいデバイス テンプレートを作成するには、 **[+ 新規]** を選択し、作成プロセスに従います。 カスタム デバイス テンプレートを最初から作成することも、Azure デバイス カタログからデバイス テンプレートを選択することもできます。 

## <a name="explore-simulated-devices"></a>シミュレートされたデバイスを探索する
IoT Central では、シミュレートされたデバイスを作成して、デバイス テンプレートとアプリケーションをテストできます。 **Water consumption monitoring (水消費量のモニタリング)** アプリケーションには、*Flow meter (流量計)* と *Smart Valve (スマート バルブ)* デバイス テンプレートにマップされた、2 つのシミュレートされたデバイスがあります。 

### <a name="to-view-the-devices"></a>デバイスを表示するには:
1. IoT Central の左側のナビゲーション ウィンドウから **[デバイス]** に移動します。 

   ![デバイス](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-devices.png)

2. 1 つの **[Smart Valve 1]** をクリックします 

    ![デバイス 1](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitor-device1.png)

3.  **[デバイス コマンド]** には、*スマート バルブ (Smart Valve)* デバイス テンプレートに定義されている機能である 3 つのデバイス コマンド *[Open Valve]\(バルブを開く\)* 、 *[Close Valve]\(バルブを閉じる\)* 、および *[Set Valve position]\(バルブの位置を設定する\)* を確認できます。 
4. **[デバイスのプロパティ]** タブと **[Device Dashboard]\(デバイス ダッシュボード\)** タブを調べます。 

> [!NOTE]
> すべてのタブがデバイス テンプレート ビューから構成されていることに注意してください。

### <a name="add-new-devices"></a>新しいデバイスを追加する
* 新しいデバイスを追加するには、 **[デバイス]** タブの **[+ 新規]** をクリックします。 

## <a name="explore-and-configure-rules"></a>ルールを探索して構成する

Azure IoT Central では、ルールを作成して、デバイスのテレメトリを自動的に監視し、1 つまたは複数の条件が満たされたときにアクションをトリガーできます。 アクションには、メール通知の送信、または他のサービスにデータを送信する Microsoft Flow アクションや Webhook アクションのトリガーを含めることができます。

作成した**Water consumption monitoring (水消費量のモニタリング)** アプリケーション テンプレートには、3 つの事前構成済みのルールがあります。

### <a name="to-view-rules"></a>ルールを表示するには:
1. IoT Central の左側のナビゲーション ウィンドウから **[ルール]** に移動します。 

   ![ルール](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-rules.png)

2. **[High pH alert]\(高 pH アラート\)** を選択してクリックします。これは、アプリケーションに事前構成されたルールの 1 つです。

     ![High pH Alert (高 pH アラート)](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-highflowalert.png)

    `High flow alert` ルールは、`Acidity (pH)` が `Max flow threshold` より `greater than` である条件に対して確認するように構成されています。 Max flow threshold (最大流量しきい値) は、デバイス *Smart Valve (スマート バルブ)* デバイス テンプレートに定義されているクラウドのプロパティです。 `Max flow threshold` の値は、デバイス インスタンスごとに設定されます。 

次に、メール アクションを作成しましょう。

ルールにアクションを追加するには:

1. **[+ Email]\(+ メール\)** を選択します。 
1. アクションのわかりやすい**表示名**として、「*High pH alert*」を入力します。
    * IoT Central アカウントに関連付けられているメール アドレスを **[送信先]** に入力します。 
1. 必要に応じて、メールのテキストに含めるメモを入力します。
1. **[完了]** を選択して、アクションを完了します。
1. **[保存]** を選択して、新しいルールを保存してアクティブにします。 

構成された**条件**が満たされると、数分以内にメールを受信するはずです。

> [!NOTE]
> アプリケーションから、条件が満たされるたびにメールが送信されます。 自動ルールからのメールの受信を停止するには、ルールを**無効**にします。 
  
新しいルールを作成するには: 
- 左側のナビゲーション ウィンドウにある **[ルール]** で **[+ 新規]** を選択します。

## <a name="configure-jobs"></a>ジョブの構成

IoT Central では、ジョブを使用して、複数のデバイスで、デバイスまたはクラウドのプロパティの更新をトリガーできます。 プロパティに加えて、ジョブを使用して、複数のデバイスでデバイス コマンドをトリガーすることもできます。 IoT Central によって、ワークフローが自動化されます。 

1. 左側のナビゲーション ウィンドウから **[ジョブ]** に移動します。 
2. **[+ 新規]** をクリックし、1 つまたは複数のジョブを構成します。 


## <a name="customize-your-application"></a>アプリケーションのカスタマイズ 
ビルダーとして、いくつかの設定を変更して、アプリケーションでのユーザー エクスペリエンスをカスタマイズできます。

1.  **[管理] > [アプリケーションのカスタマイズ]** に移動します。
3. **[変更]** ボタンを使用して、**アプリケーション ロゴ**としてアップロードするイメージを選択します。
4. **[変更]** ボタンを使用して、ブラウザーのタブに表示される**ブラウザー アイコン** イメージを選択します。
5. HTML 16 進数のカラー コードを追加して、既定の **[ブラウザーの色]** を置き換えることもできます。

   ![Azure IoT Central によるアプリケーションのカスタマイズ](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-customize-your-application.png)

1.  また、アプリケーション イメージを変更するには、 **[管理] > [アプリケーション設定]** の順に移動し、 **[画像の選択]** ボタンを選択して、アプリケーション イメージとしてアップロードするイメージを選択します。 
2. 最後に、アプリケーションのタイトルで **[設定]** をクリックして、 **[テーマ]** を変更することもできます。 

  
## <a name="clean-up-resources"></a>リソースをクリーンアップする

このアプリケーションを引き続き使用しない場合は、次の手順でアプリケーションを削除します。

1. IoT Central アプリケーションの左側のウィンドウで [管理] タブを開きます。 
2. [アプリケーションの設定] を選択し、ページの下部にある [削除] ボタンをクリックします。 


## <a name="next-steps"></a>次のステップ

* [水消費量のモニタリングの概念](./concepts-waterconsumptionmonitoring-architecture.md)の詳細を確認する
