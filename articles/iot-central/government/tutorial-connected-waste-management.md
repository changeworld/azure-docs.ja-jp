---
title: チュートリアル:Azure IoT Central を使用して接続された廃棄物管理アプリを作成する
description: チュートリアル:Azure IoT Central アプリケーション テンプレートを使用して、接続された廃棄物管理アプリケーションを作成する方法について説明します。
author: miriambrus
ms.author: miriamb
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 6b8458ce41a1bdacddc4102e8282bbc638d4c01c
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/05/2020
ms.locfileid: "77016495"
---
# <a name="tutorial-create-a-connected-waste-management-application-in-iot-central"></a>チュートリアル:IoT Central で接続された廃棄物管理アプリケーションを作成する



このチュートリアルでは、Azure IoT Central の **Connected waste management (接続された廃棄物管理)** アプリケーション テンプレートから、IoT Central の接続された廃棄物管理アプリケーションを作成する方法について説明します。 

このチュートリアルでは、次の内容を学習します。 

> [!div class="checklist"]

> * Azure IoT Central の **Connected waste management (接続された廃棄物管理)** テンプレートを使用して、接続された廃棄物管理アプリケーションを作成する
> * オペレーター ダッシュボードの探索とカスタマイズ 
> * 接続された廃棄物処理デバイス テンプレートの探索
> * シミュレートされたデバイスを探索する
> * ルールを探索して構成する
> * ジョブの構成
> * ホワイトラベリングを使用してアプリケーション ブランドをカスタマイズする

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、次のものが必要です。
-  Azure サブスクリプションをお勧めします。 必要に応じて、7 日間無料の試用版を使用することもできます。 Azure サブスクリプションがない場合は、[Azure サインアップ ページ](https://aka.ms/createazuresubscription)で作成できます。

## <a name="create-connected-waste-management-app-in-iot-central"></a>IoT Central で接続された廃棄物管理アプリを作成する

このセクションでは、Azure IoT Central で、IoT Central の **Connected waste management (接続された廃棄物管理)** テンプレートを使用して、接続された廃棄物管理アプリケーションを作成します。

新しい Azure IoT Central の接続された廃棄物管理アプリケーションを作成するには:  

1. [Azure IoT Central ホームページ](https://aka.ms/iotcentral) Web サイトに移動します。

    Azure サブスクリプションを持っている場合は、そのサブスクリプションへのアクセス用の資格情報を使用してサインインします。持っていない場合は、Microsoft アカウントを使用してサインインします。

    ![所属組織のアカウントを入力](./media/tutorial-connectedwastemanagement/sign-in.png)

2. 左側のウィンドウで **[ビルド]** をクリックし、 **[政府機関]** タブを選択します。政府機関ページに、いくつかの政府機関アプリケーション テンプレートが表示されます。

    ![政府機関アプリ テンプレートを構築する](./media/tutorial-connectedwastemanagement/iotcentral-government-tab-overview.png)

1. **Connected Waste Management (接続された廃棄物管理)** アプリケーションテンプレートを選択します。 このテンプレートには、サンプルの接続された廃棄物処理デバイス テンプレート、シミュレートされたデバイス、オペレーター ダッシュボード、事前構成済みのモニタリング ルールが含まれています。    

2. **[アプリの作成]** をクリックすると、次のフィールドがある **[新しいアプリケーション]** 作成フォームが開きます。
    * **アプリケーション名**: 既定で、アプリケーションでは、*Connected waste management* と、その後に IoT Central が生成する一意の ID 文字列が使われます。 必要に応じて、わかりやすいアプリケーション名を選択します。 後でアプリケーション名を変更することもできます。
    * **URL** - 必要に応じて、目的の URL を選択できます。 URL は後で変更することもできます。 
    * Azure サブスクリプションをお持ちの場合は、"*ディレクトリ、Azure サブスクリプション、リージョン*" を入力します。 サブスクリプションをお持ちでない場合は、**7 日間の無料試用版**を有効にし、必須の連絡先情報を入力できます。  

    ディレクトリとサブスクリプションの詳細については、[アプリケーションの作成のクイック スタート](../core/quick-deploy-iot-central.md)に関するページを参照してください。

5. ページの下部にある **[作成]** ボタンをクリックします。 

    ![Azure IoT Central の [Create Connected Waste Application]\(接続された廃棄物アプリケーションの作成\) ページ](./media/tutorial-connectedwastemanagement/new-application-connectedwastemanagement.png)
    
    ![Azure IoT Central の接続されたアプリケーションの作成の課金情報](./media/tutorial-connectedwastemanagement/new-application-connectedwastemanagement-billinginfo.png)

 
6. これで、Azure IoT Central の **Connected waste management template (接続された廃棄物管理テンプレート)** を使用して、接続された廃棄物管理アプリを作成しました。 

お疲れさまでした。 新しく作成されたアプリケーションには、事前構成された次のものが付属しています。
* オペレーター ダッシュボードのサンプル
* 事前定義済みの接続された廃棄物処理デバイス テンプレートのサンプル
* シミュレートされた接続された廃棄物処理デバイス
* 事前構成済みの規則とジョブ
* ホワイト ラベルを使用したブランド化のサンプル 

これは自分のアプリケーションであるため、いつでも変更できます。 ここで、アプリケーションを探索して、いくつかのカスタマイズを行いましょう。  

## <a name="explore-and-customize-operator-dashboard"></a>オペレーター ダッシュボードの探索とカスタマイズ 
アプリケーションの作成後、**Wide Waste connected waste management dashboard (Wide Waste 接続された廃棄物管理ダッシュボード)** に移動します。

   ![接続された廃棄物管理ダッシュボード](./media/tutorial-connectedwastemanagement/connectedwastemanagement-dashboard1.png)

ビルダーは、オペレーターのダッシュボードのビューを作成したりカスタマイズしたりすることができます。 カスタマイズしてみる前に、ダッシュボードを探索しましょう。 

> [!NOTE]
> ダッシュボードに表示されるすべてのデータは、シミュレートされたデバイス データに基づいています。これについては、次のセクションで詳しく説明します。 

ダッシュボードは、さまざまな種類のタイルで構成されています。

* ***Wide World Waste utility image tile (Wide World Waste 公益事業イメージ タイル)***: ダッシュボードの最初のタイルは、架空の廃棄物処理事業 "Wide World Waste" のイメージ タイルです。 タイルをカスタマイズしたり、独自のイメージを追加したり、削除したりできます。 

* ***Waste bin image tile (廃棄物処理イメージ タイル)***: イメージおよびコンテンツ タイルを使用して、監視対象のデバイスと説明のテキストの視覚的表現を作成できます。 

* ***Fill level KPI tile (充填レベル KPI タイル)***: このタイルには、ごみ箱の*充填レベル* センサーによって報告された値が表示されます。 ごみ箱の*充填レベル*と、*臭気メーター*や*重量*などのその他のセンサーは、リモートで監視できます。 オペレーターは、ごみ収集トラックの派遣などのアクションをとることができます。 

*  ***Waste monitoring area map (廃棄物監視区域マップ)***: マップでは Azure Maps が使用されており、Azure IoT Central で直接構成できます。 マップ タイルにデバイスの場所が表示されています。 マップの上にポインターを置いて、マップ上でズームイン、ズームアウト、展開などのコントロールを試してみてください。

     ![接続された廃棄物管理ダッシュボードのマップ](./media/tutorial-connectedwastemanagement/connectedwastemanagement-dashboard-map.png)


* ***Fill, odor, weight level bar chart (充填、臭気、重量レベル棒グラフ)** : 1 つまたは複数のデバイス テレメトリ データを棒グラフで視覚化できます。 棒グラフを展開することもできます。  

  ![接続された廃棄物管理ダッシュボードの棒グラフ](./media/tutorial-connectedwastemanagement/connectedwastemanagement-dashboard-barchart.png)


* **Field Services content tile (フィールド サービス コンテンツ タイル)** : ダッシュボードには、Azure IoT Central アプリケーションから Dynamics 365 フィールド サービスと統合する方法へのリンクが含まれています。 たとえば、フィールド サービスを使用して、ごみ収集サービスを派遣するためのチケットを作成できます。 


### <a name="customize-dashboard"></a>ダッシュボードのカスタマイズ 

ビルダーは、オペレーターのダッシュボードのビューをカスタマイズできます。 次を行うことができます。
1. **[編集]** をクリックして、**Wide World connected waste management dashboard (Wide World 接続された廃棄物管理ダッシュボード)** をカスタマイズします。 ダッシュボードをカスタマイズするには、 **[編集]** メニューをクリックします。 ダッシュボードが**編集**モードになったら、新しいタイルを追加したり、構成したりできます 

    ![ダッシュボードの編集](./media/tutorial-connectedwastemanagement/edit-dashboard.png)

2. 新しいダッシュボードを作成し、最初から構成するには、 **[+ 新規]** をクリックすることもできます。 複数のダッシュボードを作成し、ダッシュボード メニューからダッシュボード間を移動できます。 

## <a name="explore-connected-waste-bin-device-template"></a>接続された廃棄物処理デバイス テンプレートの探索

Azure IoT Central のデバイス テンプレートは、デバイスの機能を定義します。これは、テレメトリ、プロパティ、コマンドなどになります。 ビルダーは、接続するデバイスの機能を表すデバイス テンプレートを定義できます。 
 

**Connected Waste Management (接続された廃棄物管理)** アプリケーションには、サンプルの接続された廃棄物処理デバイス テンプレートが付属しています。

デバイス テンプレートを表示するには:

1. IoT Central で、アプリケーションの左側のナビゲーション ウィンドウにある **[デバイス テンプレート]** をクリックします。 

    ![デバイス テンプレート](./media/tutorial-connectedwastemanagement/connectedwastemanagement-devicetemplate.png)

2. [デバイス テンプレート] リストに、 **[Connected Waste Bin]\(接続された廃棄物処理\)** が表示されます。 名前をクリックして開きます。

3. デバイス テンプレートの機能を理解します。 "*充填レベル*"、"*臭気メーター*"、"*重量*"、"*場所*" などのセンサーが定義されているのを確認できます。

   ![デバイス テンプレート](./media/tutorial-connectedwastemanagement/connectedwastemanagement-devicetemplate-connectedbin.png)

   

### <a name="customizing-the-device-template"></a>デバイス テンプレートのカスタマイズ

次のカスタマイズを行います。
1. デバイス テンプレート メニューから **[カスタマイズ]** に移動します
2. `Odor meter` テレメトリの種類を見つけます
3. `Odor meter` の**表示名**を `Odor level` に更新します
4. 測定単位を更新するか、*最小値*と*最大値*を設定することもできます
5. すべての変更を**保存**します 

### <a name="add-a-cloud-property"></a>クラウド プロパティを追加する 

1. デバイス テンプレート メニューから **[クラウド プロパティ]** に移動します。
2. **[+ クラウド プロパティの追加]** をクリックして、新しいクラウド プロパティを追加します。 IoT Central では、デバイスに関連していますが、デバイスから送信されることを期待しないプロパティを追加できます。 たとえば、クラウドのプロパティは、設置区域、アセット情報、メンテナンス情報、その他の情報などに固有のアラートのしきい値などになります。 
3. すべての変更を**保存**します 
 
### <a name="views"></a>ビュー 
* 接続された廃棄物処理デバイス テンプレートには、事前定義済みのビューが付属しています。 ビューを調べて、更新を行うことができます。 ビューでは、オペレーターにデバイス データがどのように表示されるかを定義しますが、クラウドのプロパティも入力します。 

  ![デバイス テンプレートのビュー](./media/tutorial-connectedwastemanagement/connectedwastemanagement-devicetemplate-views.png)

### <a name="publish"></a>発行 

* 何らかの変更を行った場合は、必ずデバイス テンプレートを**発行**してください。 

### <a name="create-a-new-device-template"></a>新しいデバイス テンプレートの作成 

* 新しいデバイス テンプレートを作成するには、 **[+ 新規]** を選択し、作成プロセスに従います。 カスタム デバイス テンプレートを最初から作成することも、Azure デバイス カタログからデバイス テンプレートを選択することもできます。 

## <a name="explore-simulated-devices"></a>シミュレートされたデバイスを探索する

IoT Central では、シミュレートされたデバイスを作成して、デバイス テンプレートとアプリケーションをテストできます。 

**Connected Waste Management (接続された廃棄物管理)** アプリケーションには、接続された廃棄物処理デバイス テンプレートにマップされた 2 つのシミュレートされたデバイスがあります。 

### <a name="to-view-the-devices"></a>デバイスを表示するには:

1. IoT Central の左側のナビゲーション ウィンドウから **[デバイス]** に移動します。 

   ![デバイス](./media/tutorial-connectedwastemanagement/connectedwastemanagement-devices.png)

2. 接続された廃棄物処理デバイスを選択して、クリックします。  

     ![デバイス 1](./media/tutorial-connectedwastemanagement/connectedwastemanagement-devices-bin1.png)

3. **[クラウドのプロパティ]** タブに移動し、`Bin full alert threshold` 値を `95` から `100` に更新します。 
* **[デバイスのプロパティ]** タブと **[Device Dashboard]\(デバイス ダッシュボード\)** タブを調べます。 

> [!NOTE]
> すべてのタブが**デバイス テンプレート ビュー**から構成されていることに注意してください。

### <a name="add-new-devices"></a>新しいデバイスを追加する

* 新しいデバイスを追加するには、 **[デバイス]** タブの **[+ 新規]** をクリックします。 

## <a name="explore-and-configure-rules"></a>ルールを探索して構成する

Azure IoT Central では、ルールを作成して、デバイスのテレメトリを自動的に監視し、1 つまたは複数の条件が満たされたときにアクションをトリガーできます。 アクションには、メール通知の送信、または他のサービスにデータを送信する Microsoft Flow アクションや Webhook アクションのトリガーを含めることができます。

**Connected waste management (接続された廃棄物管理)** アプリケーションには、4 つのサンプル ルールがあります。

### <a name="to-view-rules"></a>ルールを表示するには:
1. IoT Central の左側のナビゲーション ウィンドウから **[ルール]** に移動します。

   ![ルール](./media/tutorial-connectedwastemanagement/connectedwastemanagement-rules.png)

2. **[Bin full alert]\(ごみ箱充填アラート\)** を選択します

     ![Bin full alert (ごみ箱充填アラート)](./media/tutorial-connectedwastemanagement/connectedwastemanagement-binfullalert.png)

 3. `Bin full alert` では、**条件** `Fill level is greater than or equal to Bin full alert threshold` が確認されます。

    `Bin full alert threshold` は、`Connected waste bin` デバイス テンプレートに定義されている*クラウドのプロパティ*です。 

次に、メール アクションを作成しましょう。

### <a name="create-an-email-action"></a>メール アクションを作成する
ルールのアクションの一覧でメール アクションを構成するには:
1. **[+ Email]\(+ メール\)** を選択します。 
2. アクションのわかりやすい**表示名**として、「*High pH alert*」を入力します。
3. IoT Central アカウントに関連付けられているメール アドレスを **[送信先]** に入力します。 
4. 必要に応じて、メールのテキストに含めるメモを入力します。
5. **[完了]** を選択して、アクションを完了します。
6. **[保存]** を選択して、新しいルールを保存してアクティブにします。 

構成された**条件**が満たされると、メールを受信するはずです。

> [!NOTE]
> アプリケーションから、条件が満たされるたびにメールが送信されます。 自動ルールからのメールの受信を停止するには、ルールを**無効**にします。 
  
新しいルールを作成するには: 
1. 左側のナビゲーション ウィンドウにある **[ルール]** で **[+ 新規]** を選択します。

## <a name="configure-jobs"></a>ジョブの構成

IoT Central では、ジョブを使用して、複数のデバイスで、デバイスまたはクラウドのプロパティの更新をトリガーできます。 プロパティに加えて、ジョブを使用して、複数のデバイスでデバイス コマンドをトリガーすることもできます。 IoT Central によって、ワークフローが自動化されます。 

1. 左側のナビゲーション ウィンドウから **[ジョブ]** に移動します。 
2. **[+ 新規]** をクリックし、1 つまたは複数のジョブを構成します。 


## <a name="customize-your-application"></a>アプリケーションのカスタマイズ 

ビルダーとして、いくつかの設定を変更して、アプリケーションでのユーザー エクスペリエンスをカスタマイズできます。

### <a name="to-change-the-application-theme"></a>アプリケーション テーマを変更するには:

1. **[管理] > [アプリケーションのカスタマイズ]** に移動します。
3. **[変更]** ボタンを使用して、**アプリケーション ロゴ**としてアップロードするイメージを選択します。
4. **[変更]** ボタンを使用して、ブラウザーのタブに表示される**ブラウザー アイコン** イメージを選択します。
5. HTML 16 進数のカラー コードを追加して、既定の **[ブラウザーの色]** を置き換えることもできます。

   ![Azure IoT Central によるアプリケーションのカスタマイズ](./media/tutorial-connectedwastemanagement/connectedwastemanagement-customize-your-application.png)

6. また、アプリケーション イメージを変更するには、 **[管理] > [アプリケーション設定]** の順に移動し、 **[画像の選択]** ボタンを選択して、アプリケーション イメージとしてアップロードするイメージを選択します。
7. 最後に、アプリケーションのタイトルで **[設定]** をクリックして、 **[テーマ]** を変更することもできます。

  
## <a name="clean-up-resources"></a>リソースをクリーンアップする

このアプリケーションを引き続き使用しない場合は、次の手順でアプリケーションを削除します。

1. IoT Central アプリケーションの左側のウィンドウで [管理] タブを開きます。
2. [アプリケーションの設定] を選択し、ページの下部にある [削除] ボタンをクリックします。

  

## <a name="next-steps"></a>次のステップ

* [接続された廃棄物管理の概念](./concepts-connectedwastemanagement-architecture.md)の詳細を確認する
