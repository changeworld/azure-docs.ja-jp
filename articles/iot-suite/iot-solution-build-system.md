<properties 
	pageTitle="MyDriving Azure IoT の例 - ビルドする |Microsoft Azure" 
	description="Stream Analytics、Machine Learning、Event Hubs などの Microsoft Azure で、IoT システムを設計する方法の包括的な例であるアプリをビルドします。" 
	services="" 
    documentationCenter=""
    suite="iot-suite"
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="iot-suite" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/25/2016" 
	ms.author="awills"/>


# MyDriving ソリューションを自分の環境でビルドおよびデプロイする

MyDriving は、車からデータを収集するモノのインターネット (IoT) ソリューションです。Machine Learning を使用して処理し、収集したデータを携帯電話に表示します。バックエンドは、Microsoft Azure から提供されるさまざまなサービスで構成され、Android、iOS、または Windows 10 のスマートフォンをクライアントにすることができます。

当社は独自の IoT システムを作成するきっかけを提供するために、MyDriving ソリューションを作成しました。リポジトリから Azure Resource Manager スクリプトを取得して、バックエンドのアーキテクチャを自分の Azure アカウントにデプロイしたり、さまざまなサービスを再設定できる場所から、自分のデータに合うようにクエリを変更したりすることができます。<https://github.com/Azure-Samples/MyDriving> にあるモバイル アプリ、Azure App Service API プロジェクトなどのコードを使用して、これらのスクリプトを検索できます。

まだアプリを試していない場合は、「[ファースト ステップ ガイド](iot-solution-get-started.md)」を参照してください。

## 何を作成する必要がありますか?

「[MyDriving リファレンス ガイド](http://aka.ms/mydrivingdocs)」にアーキテクチャの詳細なアカウントがあります。要約すると、当社がセットアップする構成と、同様のプロジェクトを作成するためにお客様がセットアップする構成があります。

* **クライアント アプリ**。これは、Android、iOS および Windows 10 のスマートフォンで実行されます。Xamarin プラットフォームを使用して、`src/MobileApp` の GitHub に格納されている多くのコードを共有しています。アプリでは、実際に次の 2 つの異なる機能を実行します。
 * OBD デバイスおよびその独自のロケーション サービスからシステムのクラウド バックエンドに**テレメトリをリレー**します。
 * **ユーザー インターフェイス**では、ユーザーが記録された乗車についてクエリを実行できるようにします。
* **クラウド サービス**では、乗車データをリアルタイムで取り込み、処理します。このサービスを作成するための主な作業は、多様な Azure サービスの選択、カスタマイズ、および接続です。一部のパーツには、受信データをフィルターおよび処理するためにスクリプトが必要です。**Azure リソース管理 (ARM)** テンプレートを使用して、すべてのパーツを構成します。
* **モバイル サービス アプリ**。これは、デバイス アプリのユーザー インターフェイス部分の背後にある Web サービスです。この主な仕事は、格納された処理済みデータのデータベースにクエリを行うことです。そのコードは、`src/MobileAppService` の GitHub にあります。
* **Visual Studio with Xamarin** は当社の開発環境です。Xamarin - Visual Studio とスタンドアロン IDE (クロスプラットフォーム デバイス コードをビルドするために使用) の両方のコンポーネントが存在します。iOS コードをビルドするには、Visual Studio から管理されるエージェントとして実行できる必要がある場合でも、OSX コンピューターで Xamarin のインスタンスを実行している必要があります。
* デバイス アプリの**単体テスト**は、Xamarin Test Cloud で実行されます。
* **GitHub** は、すべてのコード、スクリプト、およびテンプレートを保存するリポジトリです。
* **Visual Studio Team Services** は、Web サービスとデバイス アプリの継続的なビルドとテストを管理するために使用するクラウド サービスです。
* **HockeyApp** は、デバイス コードのリリースを配布するために使用されます。配布を管理するだけでなく、クラッシュと使用状況レポートとユーザーからのフィードバックを収集します。
* **Application Insights** は、モバイル Web サービスを監視します。


それでは、すべての設定方法を見てみましょう。手順の多くは省略可能であることに注意してください。

## アカウントにサインアップ

-   [**Visual Studio Dev Essentials**](https://www.visualstudio.com/products/visual-studio-dev-essentials-vs.aspx)。この無料プログラムでは、Visual Studio、Visual Studio Team Services、および Azure を含む、多くの開発者ツールやサービスへの簡単なアクセスを提供し、12 か月間、Azure で毎月 25 ドルのクレジットを提供します。これには、Pluralsight トレーニングと Xamarin University のサブスクリプションも含まれます。[Azure](https://azure.com) と [Visual Studio Team Services](https://www.visualstudio.com/products/visual-studio-team-services-vs.aspx) の Free レベルに別々にサインアップすることもできますが、Azure クレジットを提供することはありません。

-   [**HockeyApp**](https://rink.hockeyapp.net/) (省略可能) は、モバイル アプリのテスト配布の管理とテレメトリの収集用です。

-   [**Xamarin**](https://xamarin.com/) (必須) は、モバイル アプリのビルドと、[Xamarin Test Cloud](https://xamarin.com/test-cloud) でのデバッグとテストの実行用です。

-   [**GitHub**](https://github.com/Azure-Samples/MyDriving/) (省略可能) では、独自のコード用に無料のパブリック リポジトリを作成します (プライベート リポジトリは有料です)。また、プライベート リポジトリに Visual Studio Team Services の基本プランを使用することもできます。

-   [**Power BI**](https://powerbi.microsoft.com/) (省略可能) は、システム全体に対するデータの豊富な視覚化を作成するために使用します。

> [AZURE.NOTE] [https://github.com/Azure-Samples/MyDriving](https://github.com/Azure-Samples/MyDriving) の MyDriving コードにアクセスするために、GitHub アカウントは必要ありません。

## 開発ツールのインストール

次の設定は、Azure バックエンドを使用する iOS、Android および Windows Phone 10 クロスプラットフォーム アプリを含む、完全なソリューションを開発するために行います。

または、Azure バックエンドで作業を行っていない場合は、モバイル アプリを開発するために、Mac または Windows のいずれかで Xamarin Studio を使用できます。

[この設定の詳細については、こちら](https://msdn.microsoft.com/library/mt613162.aspx)を参照してください。

### Windows の開発用コンピューター

Windows 上の中心的なツールは Visual Studio で、Android および Windows 用の MyDriving アプリ、App Service API プロジェクト、およびマイクロサービスの拡張機能と共に動作します。

Xamarin、Git、エミュレーターおよびその他の役立つコンポーネントは、すべて Visual Studio に統合されています。

インストール:

-   [**Visual Studio 2015 with Xamarin**](https://www.visualstudio.com/products/visual-studio-community-vs) (任意のエディション – Community は無料)

-   [**ユニバーサル Windows プラットフォーム用 SQLite**](https://visualstudiogallery.msdn.microsoft.com/4913e7d5-96c9-4dde-a1a1-69820d615936)。Windows Phone 10 コードのビルドに必要です。

-   [Azure SDK for VS2015](https://go.microsoft.com/fwlink/?linkid=518003&clcid=0x409) – Azure でアプリを実行するために SDK と、Azure を管理するためにコマンド ライン ツールを提供します。

-   [Azure Service Fabric SDK](http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric)。[マイクロ サービス](../service-fabric/service-fabric-get-started.md)の拡張機能のビルドに必要です。

Visual Studio 拡張機能: [ツール] の下を確認すると、Android、iOS、Xamarin などが表示されます。表示されない場合は、**[コントロール パネル]**、**[プログラムと機能]**、**[Microsoft** **Visual Studio 2015]**、**[変更]** の順に開きます。**[クロスプラットフォーム開発]** で、**[C#/.Net (Xamarin)]** を選択します。 そこで、**GitHub** がインストールされていることを確認します。

### Mac の開発用コンピューター

iOS を開発する場合は、Mac (Yosemite 以降) が必要です。Windows ではすべてのコードの開発と管理を行うために、Visual Studio with Xamarin を使用していますが、Xamarin では iOS コードをビルドおよび署名するために、Mac にインストールされているエージェントを使用します。

![](./media/iot-solution-build-system/image1.png)

(または、クロスプラットフォーム アプリを開発するために、Mac 上で直接 Xamarin Studio を使用できます。)

ターゲット プラットフォームとして iOS を含めない場合、Mac は必要ありません。

インストール:

-   [**Xamarin Studio** for iOS](https://developer.xamarin.com/guides/ios/getting_started/installation/mac/)。Windows 仮想マシンを実行している Mac 上で、Visual Studio および Xamarin を設定することもできます。MSDN の「[Mac ユーザーのセットアップ、インストール、および検証](https://msdn.microsoft.com/library/mt488770.aspx)」を参照してください。

-   (省略可能) [Azure 開発ツール](https://azure.microsoft.com/downloads/)。

Mac 上でリモート ログインを有効にします。[システム環境設定]、[共有] の順に開いて、[リモート ログイン] を確認します。

Windows の Visual Studio で iOS プロジェクトを開くと、Xamarin プラグインから Mac の ID を要求するプロンプトが表示されます。

## GitHub リポジトリのフェッチ

GitHub、Visual Studio、または別の Git クライアント上の **[ZIP をダウンロード]** ボタンを使用して、<https://github.com/Azure-Samples/MyDriving> のローカル コピーをフェッチします。

C:\\code など、短いパス名のフォルダーにファイルを解凍します。

または、最新の状態に保持するか、コードを投稿する場合は、次のようにリポジトリを複製します。

> git clone <https://github.com/Azure-Samples/MyDriving.git>

## Bing マップの開発者コード

[Bing マップ API キーを登録します](https://msdn.microsoft.com/library/ff428642.aspx)。

`src/MobileApps/MyDriving/MyDriving.Utils/Logger.cs` の 22 行目をこのキーに置き換える必要があります。



## デモ アプリのビルド

Visual Studio で次のソリューションを開きます。

-   src\\MobileApps\\MyDriving.sln
-   src\\MobileAppService\\MyDrivingService.sln
-   src\\Extensions\\ServiceFabric\\VINLookUpApplication\\VINLookUpApplication.sln

次を求めるプロンプトが表示されます。

-   信頼できない可能性があるいくつかのプロジェクトを信頼します。先に進める場合は、これらのプロジェクトを開くことを選択します。

-   新しい Windows 10 コンピューターで作業する場合は、開発者モードを設定します。

-   Xamarin の資格情報を入力します。

-   Xamarin Mac に接続します。Mac を所有していない場合は、Visual Studio で iOS プロジェクトを右クリックして、**[プロジェクトのアンロード]** を選択します。

ソリューションをリビルドします。

### ビルドで問題がある場合

次のような問題が発生する場合があります。

-   VINLookupApplication プロジェクトが読み込まれない: [Azure SDK for VS2015](https://go.microsoft.com/fwlink/?linkid=518003&clcid=0x409) をインストールしていることを確認します。

-   Service Fabric プロジェクトをビルドできない: 最初にインターフェイス プロジェクトをビルドし、Service Fabric SDK をインストールしていることを確認します。

-   Android アプリをビルドできない:

    -   [ツール]、[Android]、[Android SDK Manager] の順に開き、Android 6 (API 23)/SDK プラットフォームがインストールされていることを確認します。

    -   このディレクトリを削除して、次をリビルドします。<br/> `%LocalAppData%\Xamarin\zips`

## コードについて

ソリューションには、以下があります。

-   Azure 拡張機能

    -   Service Fabric

-   HDInsight – Azure で乗車データを処理するためのスクリプト

-   Mobile Apps – デバイス アプリ

-   MobileAppsService/MyDrivingService – Web バックエンド

-   Power BI – Power BI レポートの定義

-   スクリプト

    -   ARM – Azure リソースをビルドするためのテンプレート

    -   PowerShell – ARM テンプレートを実行するためのスクリプト

    -   SQLDatabase – データベースのデバッグ

-   SQLDatabase

    -   CreateTables – スキーマの定義

-   StreamingAnalytics – 受信データ ストリームを変換するクエリ

## 開発モードでアプリを実行する

### バックエンド

MyDrivingService をスタートアップ プロジェクトとして設定し、F5 キーを押して、バックエンド Web サービスを実行します。API の一覧のブラウザー ビューが開きます。

### モバイル クライアント

[モバイル アプリは Xamarin で開発されます](https://developer.xamarin.com/guides/cross-platform/deployment,_testing,_and_metrics/debugging_with_xamarin/)。

-   [Xamarin での Android のデバッグ](http://developer.xamarin.com/guides/android/deployment,_testing,_and_metrics/debugging_with_xamarin_android/)

### iOS

-   [iOS でのデバッグ](http://developer.xamarin.com/guides/ios/deployment,_testing,_and_metrics/debugging_in_xamarin_ios/)

### Windows Phone

-   [Xamarin + Windows Phone](https://developer.xamarin.com/guides/cross-platform/windows/phone/)


## HockeyApp

HockeyApp では、新しいリリースをユーザーに通知して、Android、iOS や Windows の使用しているデバイスのアプリをテスト ユーザーに配布することを管理します。また、有用なクラッシュ レポート、スクリーン ショット付きのユーザーからのフィードバック、および使用状況メトリックも収集します。

ビルド アプリを[アップロードして開始します](http://support.hockeyapp.net/kb/app-management-2/how-to-create-a-new-app)。次に、開発用コンピューターから HockeyApp (<https://rink.hockeyapp.net>) にサインインします。開発者ダッシュボードで、**[新しいアプリ]** をクリックして、ビルド ファイルをウィンドウにドラッグします。(後では、ビルド サービスを自動化してこれを実行できます。)

アプリのダッシュ ボードが開きました。

![](./media/iot-solution-build-system/image2.png)

アプリを実行する各プラットフォームで、このプロセスを繰り返します。

- ダッシュボードから [アプリ ID](http://support.hockeyapp.net/kb/app-management-2/how-to-find-the-app-id) を使用して、アプリからクラッシュ データとフィードバックを送信します。MyDriving で、src/MobileApps/MyDriving/MyDriving.Utils/Logger.cs の ID を更新します。 
-   [**テスト ユーザーを招待します**](http://support.hockeyapp.net/kb/app-management-2/how-to-invite-beta-testers)。URL を取得して、テスト担当者のユーザーを募集します。テスト ユーザーはチームにサインアップして、アプリケーションをダウンロードし、フィードバックを送信することができます。

-   または、よりオープンなベータ リリースを希望する場合は、配布をパブリックに設定します。**[アプリの管理]、[配布]、[ダウンロード = パブリック]** の順にクリックします。 これで、すべてのユーザーがアプリをダウンロードし、フィードバックを送信できます。また、新しいバージョンを投稿したときに、ユーザーに通知が表示されます。また、ユーザーからクラッシュ レポートを受け取る可能性もあります。![](./media/iot-solution-build-system/image3.png)

-   [**クラッシュ レポートと Visual Studio Team Services をリンクさせます**](http://support.hockeyapp.net/kb/third-party-bug-trackers-services-and-webhooks/how-to-use-hockeyapp-with-visual-studio-team-services-vsts-or-team-foundation-server-tfs)。**[アプリの管理]、[Visual Studio Team Services]** の順にクリックします。クラッシュ レポートがある場合や、フィードバックを受信した場合、HockeyApp では Team Services に自動的に作業項目を作成できます。

> 詳細については、<https://hockeyapp.net> を参照してください。

## Xamarin Test Cloud

[Xamarin Test Cloud](https://developer.xamarin.com/guides/testcloud/introduction-to-test-cloud/) では、クラウド内の実際のデバイス上で UI テストを自動化します。NUnit フレームワークを使用して、ユーザー インターフェイスからアプリケーションを実行するテストを作成します。

Xamarin を使用するには、[Xamarin.UITests](https://developer.xamarin.com/guides/testcloud/uitest/intro-to-uitest/) SDK をアプリに組み込みます。これは、NuGet パッケージとして提供されます。デモ アプリで見つけることができ、Xamarin テンプレートで新しいテスト プロジェクトを作成するときに含まれます。

![](./media/iot-solution-build-system/image4.png)

テスト プロジェクトの例は、リポジトリのアプリに含まれます。[MyDriving](https://github.com/Azure-Samples/MyDriving/tree/master/src/MobileAppService) の [src](https://github.com/Azure-Samples/MyDriving/tree/master/src)/MobileApps/[MyDriving](https://github.com/Azure-Samples/MyDriving/tree/master/src/MobileApps/MyDriving)/MyDriving.UITests/ にあります。

Visual Studio Team Services のビルドを使用する場合、簡単に Xamarin UI 単体テストを作成し、ビルドの一部として実行できます。

## Azure サービスのデプロイ

Azure サービスと Team Services Build サービスの自動デプロイを実行するには、**scripts/README.md** の詳細な手順を参照してください。

### デプロイ スクリプトで実行される操作

Microsoft Azure では、クラウド アプリケーションのビルドに使用できるさまざまなサービスを提供します。多くは個別に使用できますが (App Service、Web Apps など)、MyDriving で使用するなど、統合システムを形成するために相互接続すると、最適な状態で使用できます。

Azure サービスを手動で作成および相互接続することができますが、Azure Resource Manager (ARM) テンプレートを使用すると、より早く信頼性が向上します。[ARM](../resource-group-overview.md) では、ソリューションのリソースのデプロイとリソース間の相互接続を自動化します。

[Scripts/ARM](https://github.com/Azure-Samples/MyDriving/tree/master/scripts/ARM) の GitHub リポジトリで、MyDriving システムのテンプレートを見つけます。このテンプレートでは、アーキテクチャのさまざまなサービスがどのように相互接続されているかについて、非常に包括的かつ簡潔なビューを提供します。これらすべての詳細については、「[MyDriving リファレンス ガイド](http://aka.ms/mydrivingdocs)」で説明しますが、テンプレート自体を読むだけで多くを把握することができます。

> [AZURE.NOTE] ほとんどの Azure サービスには、価格レベルに応じて関連するコストがあります。Azure を初めて使用する場合、[無料で試す](https://azure.microsoft.com/free/)ことができます。ただし、MyDriving システムで特定のコンポーネントを使用する予定がない場合は、コストが発生することを回避するために、それらのコンポーネントを削除してください。一般的なサービス費用の概要については、以下の「*運用コストの見積もり*」を参照してください。

#### テンプレートの編集

デプロイをカスタマイズするために、不要なコンポーネントを削除したり、その他を追加したりするには、変更を加える scenario\_complete.params.json および scenario\_complete.json のコピーを最初に作成します。

scenario\_complete.params.json ファイルでは、次の表に示すようなサービス SKU またはストレージ レプリケーションの種類など、さまざまな既定値を上書きできます。既定値は、最低コストのオプションが選択されます。

| **パラメーター** | **説明** | **既定値** |
|--------|---------|-------|
| IoT Hub SKU | Azure IoT Hub サービスのレベル | F1 |
| Storage Account Type | ストレージ レプリケーションの種類 | 標準 LRS |
| SQL Service Objective | 使用される同時実行スロット数 | DW100 |
| Hosting Plan SKU | App Service のサービス プラン | F1 |

scenario\_complete.json での操作:

-   "baseName" を検索し、使用する名前に変更します。

-   "Create" で検索 – これらの各セクションでリソースを作成します。

-   sqlServerAdminLogin および sqlServerAdminPassword に適した値を設定します。

-   リソースを作成するセクションを削除する前に、その名前がファイル内の別の場所に存在しないか検索し、依存関係があるかどうかを確認します。サービスを作成する各セクションには、依存関係を一覧する *dependsOn* セクションが含まれることに注意してください。

ここにテンプレートの構成内容を示します。詳細については、「[リファレンス ガイド](http://aka.ms/mydrivingdocs)」を参照してください。

| **サービス** | **説明と詳細**  
|---|----
| ストレージ アカウント | テンプレートでは、次の 3 つのアカウントを作成します。                                                                                                                                                                       
||- Stream Analytics から集計されたテレメトリを受信し、API エンドポイントを使用してこのデータを公開する Azure App Service テーブルのバッキング ストアとして機能する SQL データベース。                      
| | - HDInisight によって処理される、別の Stream Analytics ジョブから履歴データを蓄積する Blob ストレージ。                                                                                         
| | - Power BI で使用する HDInsight で処理された結果を受信する SQL データベース。                                                                                                                 
| IoT Hub | 接続されている各デバイスとの双方向接続を確立します。MyDriving ソリューションで、モバイル アプリは IoT Hub にデータを送信するフィールド ゲートウェイとして機能します。その後、IoT Hub は Stream Analytics への入力として機能します。 |
| イベント ハブ | 出力を Azure Service Fabric で作成しされた拡張機能のキューに登録する Stream Analytics ジョブの出力。                                                                                               
| SQL Data Warehouse |                                                                                                                                                                                                            
| Stream Analytics (ASA) ジョブ | App Service API、Machine Learning、拡張機能、および Power BI のリアルタイムと履歴データの両方を集計するために使用するクエリでの入力と出力の接続。                               
| Machine Learning ワークスペース | 実験、R コード、API サービスが含まれます。                                                                                                                                                              
| Data Factory   | スケジュールされた Machine Learning の再トレーニング。                                                                                                                                                                     
| Service Fabric ホスティング プラン | 拡張機能に使用します。                                                                                                                                                                                            
| App Service (“モバイル アプリ”) | モバイル アプリのエンドポイントを提供する Mobile Apps API プロジェクトをホストします。API コードは、Visual Studio から App Service にデプロイされる必要があります。                                                         
| アラート ルール | アプリの応答でエラーが示される場合に、電子メールを送信します。                                                                                                                                            
| Application Insights | App Service で API のパフォーマンスを監視します。Visual Studio での接続を構成する必要があります。                                                                                          
| Key Vault | Web サービス クラスターの証明書を保存します。                                                                                                                                                                

#### テンプレートの実行

**scripts/README.md に詳細な手順**があります。

スクリプトを使用して自分の Azure アカウントのすべてのサービスをプロビジョニングするには、次のいずれかの操作を行います。

-   PowerShell を使用する

    ```

    cd scripts/PowerShell;
    deploy.ps1 *location* *resourceGroupName*
    ```

 -   *location* は、'North Europe' や 'west us' などの [Azure の場所](https://azure.microsoft.com/regions/)です。`Get-AzureLocation` を使用して、利用可能な場所の一覧を検索します。

 -   *resourceGroupName* は、すべてのリソースが属するグループを指定する名前です。リソースが完了したときに、このグループを削除すると、それらをすべてまとめて削除することができます。

-   Bash で DeploymentScripts/Bash/deploy.sh を実行します。

-   Visual Studio ソリューションの DeploymentScripts/VS/DeployARM.sln を開き、ビルドします。

テンプレートを実行するたびに、一連の新しいリソースが新しい名前で作成されることに注意してください。リソースを削除するには、ポータルに移動して、リソース グループを削除します。

スクリプトが何らかの理由で失敗した場合、もう一度実行することができます。

スクリプトでは、Visual Studio Team Services の継続的な統合を構成するオプションを提供しています。Team Services プロジェクトを設定した場合、URL https://yourAccountName.visualstudio.com があります。要求されたら、完全な URL を入力します。Team Services プロジェクトに新規または既存の名前を付けることができます。



## Visual Studio Team Services

ほとんどの場合、当社では機能をビルドおよびテストするために、このプロジェクトの Team Services を使用します。ただし、かんばんボードのあるタスク管理、タスクやソース管理と統合されたコード レビュー、ゲート ビルドなど、コラボレーション機能も提供されます。これは、GitHub、Xamarin、HockeyApp、および Visual Studio などのその他のツールとうまく統合されます。いつでも Web インターフェイスまたは Visual Studio のいずれかより便利な方を使用して、Team Services にアクセスできます。

ビルドとリリースの定義の手順では、Team Services [Marketplace](https://marketplace.visualstudio.com/VSTS) で利用可能なさまざまなプラグイン サービスを使用します。コマンドラインの実行やファイルのコピーを行う基本的なユーティリティだけでなく、Xamarin、Android およびその他のベンダーによるビルドを呼び出したり、HockeyApp とインターフェイスで接続したりするサービスがあります。

![](./media/iot-solution-build-system/image5.png)

### ビルド定義

メイン ターゲットごとにビルド定義があり、機能と回帰テストにはバリエーションがあります。次が提供されます。

-   MyDriving.Services – モバイル アプリのバックエンド Web アプリ

-   MyDriving.Xamarin.Android

    -   MyDriving.Xamarin.Android-Feature

    -   MyDriving.Xamarin.Android-Regression

-   MyDriving.Xamarin.iOS

    -   MyDriving.Xamarin.iOS-Feature

    -   MyDriving.Xamarin.iOS-Regression

-   MyDriving.Xamarin.UWP

    -   MyDriving.Xamarin.UWP-Feature

    -   MyDriving.Xamarin.UWP-Regression

この構成の完全な詳細情報を表示する場合は、「[MyDriving リファレンス ガイド](http://aka.ms/mydrivingdocs)」のセクション 4.7、「*ビルドとリリースの構成*」を参照してください。 これらは、次の同じ一般的なパターンに従います。

1.  NuGet パッケージの復元。リポジトリにコンパイルされたコードを保持しないため、各ビルドの最初の手順で必要な NuGet パッケージを復元します。

2.  ライセンスのアクティブ化。ビルドはクラウドで実行されるため、ライセンスが必要な場合は (特に、Xamarin ビルド サービス)、現在のビルド コンピューターでライセンスをアクティブ化する必要があります。その後、別のコンピューターで使用できるように、当社がすぐにライセンスを非アクティブ化します。

3.  適切なサービスを使用してビルドする。モバイル アプリには Xamarin ビルドを、バックエンド Web サービスには Visual Studio ビルドを使用します。

4.  テストをビルドする。

5.  テストを実行する。Xamarin Test Cloud でモバイル アプリ テストを実行します。

6.  格納場所にビルドの結果を発行する。

メイン ビルドのトリガーは [継続的インテグレーション] に設定されています。つまり、ビルドは、コードがマスター分岐にチェックインするたびに実行されます。

![](./media/iot-solution-build-system/image6.png)

### リリース定義

リリース定義は、ほぼ同じように設定されます。

Web サービスでは、Azure Web アプリとしてデプロイを設定します。

![](./media/iot-solution-build-system/image7.png)

リリース トリガーを継続なデプロイに設定します。つまり、チェックインの後にビルドが成功するたびに、Web アプリが更新されます。

![](./media/iot-solution-build-system/image8.png)

モバイル アプリの場合、HockeyApp にデプロイします。![](./media/iot-solution-build-system/image9.png)

## Application Insights

[Application Insights](../application-insights/app-insights-overview.md) では、パフォーマンスと Web サービスの使用状況に関するテレメトリを収集します。Application Insights SDK では、サービスから Azure の Application Insights リソースにテレメトリを送信します。

テンプレートで設定する Application Insights リソースを参照します。そこで、[Mobile App Service プロジェクト](https://github.com/Azure-Samples/MyDriving/tree/master/src/MobileAppService)のパフォーマンスのグラフを調べることができます。グラフには、サーバーの要求と応答時間、エラー、および例外の数が表示されます。依存関係の応答時間のグラフもあります。つまり、Machine Learning としてデータベースと REST API への呼び出しがあります。パフォーマンスに問題がある場合は、問題の原因となっているシステムの部分を表示することができます。

![パフォーマンス グラフの例](./media/iot-solution-build-system/image11.png)

手動で設定した Web サービスがある場合は、同じグラフを簡単に取得できます。

![Web サービス ブレードで、[ツール]、[拡張機能]、[追加] の順に選択します。Application Insights を選択します。](./media/iot-solution-build-system/image12.png)

この機能は、Application Insights SDK を使用してアプリケーションをインストルメント化して動作します。

開発時に [Application Insights SDK を追加して](../application-insights/app-insights-asp-net.md)、カスタム テレメトリを追加できます (または、Azure の外部で実行されているアプリケーションをインストルメント化できます)。これは、ユーザーの平均乗車距離や合計走行距離など、アプリケーションによって異なるメトリックを記録するために便利です。

![Visual Studio でプロジェクトを右クリックし、[Application Insights の追加] を選択します](./media/iot-solution-build-system/image10.png)

Application Insights では、異常な数のエラー応答を確認した場合に、アラート メールを送信します。また、応答時間などのさまざまなメトリックに独自のアラートを設定することもできます。

Web サービスが常に確実に起動および実行されるようにするために、世界中のさまざまな場所から自分のサイトに 15 分ごとに ping する、[可用性テスト](../application-insights/app-insights-monitor-web-app-availability.md)を設定できます。ここでも、問題がある可能性がある場合は、電子メールを受信します。



## 運用コストの見積もり

このようなアプリの小規模の実行は、非常に低価格で行うことができます。ほとんどのサービスにはエントリ レベルの Free レベルがあるため、開発と小規模の運用コストはほんのわずかです。当然ながら、自分のアプリで MyDriving で示したすべての機能を使用する必要はありません。

MyDriving の開発構成を設定するコストの大まかな見積もりを、次に示します。

使用し*なかった*代替手段についても示します。#

次を想定しています。

-   5 チーム以下 (+ 観察する利害関係者)。

-   約 1 か月の実行。

-   1 日あたり 4 回乗車するユーザー 100 人。

>[AZURE.NOTE] Azure を初めて使用する場合、[無料試用版アカウント](https://azure.microsoft.com/free/)があります。

| **サービス/コンポーネント** | **メモ** | **コスト/月** |
|--------|--------|----------------|
| [Visual Studio 2015 Community](https://www.visualstudio.com/products/visual-studio-community-vs) with [Xamarin](https://visualstudiogallery.msdn.microsoft.com/dcd5b7bd-48f0-4245-80b6-002d22ea6eee) <br/>クロスプラットフォームの開発環境。| Visual Studio Community。 <br/>(# 単一のコード ベースからクロスプラットフォームを設計するには、[Xamarin.Forms](https://xamarin.com/forms) に [VS Pro](https://www.visualstudio.com/vs-2015-product-editions) が必要です。) | 0 ドル |
| [Azure IoT Hub](https://azure.microsoft.com/pricing/details/iot-hub/) <br/>デバイスへの双方向データ接続。 | 8,000 メッセージ + 0.5 KB/メッセージが無料 | 0 ドル |
| [Stream Analytics](https://azure.microsoft.com/pricing/details/stream-analytics/) <br/> 大量のストリーム データ処理。 | 有効な場合に、1 時間あたりのストリーミング ユニットごとに 0.031 ドルを課金します。必要なストリーミング ユニット数を選択します。さらに必要な場合はスケールアップします。 | 23 ドル |
| [Azure Machine Learning](https://azure.microsoft.com/documentation/services/machine-learning/)<br/> 適応型の応答。 | 10 ドル/シート/月 <br/> + 3 時間の実験 * 1 ドル/実験時間 <br/> + 3.5 時間の API CPU * 2 ドル/実稼働 CPU 時間 <br/> API CPU 時間は 5 分/日の再トレーニングを想定していますが、入力データが増えると上昇します。<br/> + 400 回の乗車/日の処理に 2 分/日のスコアリング。 | 20 ドル |
| [Azure App Service](https://azure.microsoft.com/pricing/details/app-service/) <br/> ホスト モバイル バックエンド | レベル B1 – 運用 Web アプリ | 56 ドル |
| [Visual Studio Team Services ](https://azure.microsoft.com/pricing/details/visual-studio-team-services/) <br/> ビルド、単体テストおよびリリースの管理。タスク管理。 | プライベート Agents、ユーザー 5 人| 0 ドル |
| [Application Insights](https://azure.microsoft.com/pricing/details/application-insights/) <br/>Web サービスとサイトのパフォーマンスと使用状況の監視。| Free レベル | 0 ドル |
| [HockeyApp](http://hockeyapp.net/pricing/) <br/> ベータ アプリの配布、フィードバック、使用状況とクラッシュ データの収集。 | 新規ユーザー用に 2 つの無料アプリ。<br/> その後は 30 ドル/月。 | 0 ドル |
| [Xamarin](https://store.xamarin.com/)<br/> 複数のデバイスの統合されたプラットフォーム上のコード。 | 無料試用版。<br/>その後は 25 ドル/月。| 0 ドル |
| Azure App Service の [SQL インスタンス](https://azure.microsoft.com/pricing/details/sql-database/)| Basic レベル、Single Database モデル | 5 ドル |
| [Service Fabric](../service-fabric/services/service-fabric.md) (省略可能) | ローカル クラスターを実行する | 0 ドル |
| [Power BI](https://powerbi.microsoft.com/pricing/)<br/> 多用途のディスプレイおよびストリーミングされたデータと静的データの調査。| Free レベル: 1GB、10,000 行/時間、毎日更新 <br/> # [上限引き上げ](https://powerbi.microsoft.com/documentation/powerbi-power-bi-pro-content-what-is-it/)の場合は 10 ドル/ユーザー/月、接続オプションの追加、コラボレーション | 0 ドル |
| [Storage](https://azure.microsoft.com/pricing/details/storage/) | L (ローカル冗長) < 100G 0.024 ドル/GB | 3 ドル |
| [Data Factory](https://azure.microsoft.com/pricing/details/data-factory/) | 0\.60 ドル/アクティビティ * (8 - 5 FOC)| 2 ドル |
| [HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/) (オンデマンド クラスター) <br/> 毎日の再トレーニングに使用 | 毎日 1 時間 X 31 日の場合、0.32 ドル/時間で A3 ノード 3 個 | 30 ドル |
| [Event Hubs](https://azure.microsoft.com/pricing/details/event-hubs/) | 11 ドル/月のスループット ユニットの基本 + 0.028 ドルのイングレス | 11 ドル |
| OBD ドングル || 12 ドル |
| **合計**| | **157 ドル** |

関連項目:

-   [Azure サービスのクォータと制限](../azure-subscription-service-limits/#iot-hub-limits)の概要

-   Azure [料金計算ツール](https://azure.microsoft.com/pricing/calculator/)



## リファレンス ガイド

システムとそのコンポーネントの設計の包括的な説明である「[MyDriving リファレンス ガイド](http://aka.ms/mydrivingdocs)」をお勧めします。


## フィードバック 

独自の IoT システムをすぐに開始できる MyDriving を作成しましたので、問題なくく動作したかどうかなど、ご意見をお寄せください。 問題や課題がある場合、自分のシナリオにより適応させるために拡張できる点がある場合、特定のニーズを達成するためにより効率的な方法を見つけた場合、または MyDriving またはこのドキュメントを改善するためにその他の提案がある場合は、お知らせください。

フィードバックを送るには、[GitHub 上の問題] を作成するか、以下にコメントを残してください (ja-JP 版)。

ご意見をお待ちしています。

<!----HONumber=AcomDC_0406_2016-->