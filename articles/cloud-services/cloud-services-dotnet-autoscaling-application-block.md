<properties 
	pageTitle="自動スケール アプリケーション ブロックの使用 (.NET) | Microsoft Azure"
	description="Azure で自動スケール アプリケーションを使用する方法について説明します。コード サンプルは C# で記述され、.NET API を利用しています。"
	services="cloud-services"
	documentationCenter=".net"
	authors="squillace"
	manager="timlt"
	editor=""/>

<tags 
	ms.service="cloud-services"
	ms.workload="tbd"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="05/18/2015"
	ms.author="rasquill"/>
# 自動スケール アプリケーション ブロックの使用方法

このガイドでは、[Microsoft Enterprise Library 5.0 Integration Pack for Azure][] の自動スケール アプリケーション ブロックを使用する一般的なシナリオの実行方法を示します。サンプルは C# で記述され、.NET API を利用しています。紹介するシナリオは、**ブロックのホスティング**、**制約規則の使用**、および**リアクティブ規則の使用**です。オートスケーリング アプリケーション ブロックの詳細については、「[次のステップ](#next-steps)」セクションを参照してください。

## オートスケーリング アプリケーション ブロックとは

自動スケール アプリケーション ブロックは、アプリケーション用に個別に定義した規則に基づいて、Azure アプリケーションの規模を自動的に調整できます。これらの規則を使用すると、Azure アプリケーションがワークロードの変化に対応してスループットを維持できるようにすることができます。同時に、Azure でのアプリケーションのホストに関連するコストを制御することもできます。ブロックは、アプリケーション内のロール インスタンスの数を増減するスケーリングだけでなく、アプリケーションの特定の機能の調整、カスタム定義の操作の使用など、その他のスケーリング操作も利用できるようにします。

ブロックを Azure ロールでホストするか、オンプレミスのアプリケーションでホストするかを選択できます。

オートスケーリング アプリケーション ブロックは、[Microsoft Enterprise Library 5.0 Integration Pack for Azure][] の一部です。

## 概念

次の図では、緑の線が、2 日間にわたる Azure ロールの実行インスタンスの数を示しています。インスタンスの数は、自動スケール規則のセットに応じて、時間の経過と共に自動的に変化します。

![diagram of sample autoscaling](./media/cloud-services-dotnet-autoscaling-application-block/autoscaling01.png)

ブロックは、アプリケーションの自動スケールの動作を定義するために、次の 2 種類の規則を使用します。

-   **制約規則**: インスタンス数の上限と下限を設定する場合、たとえば毎朝 8:00 から 10:00 まで最小で 4 個、最大で 6 個のインスタンスを必要とする場合、**制約規則**を使用します。図では、赤と青の線が制約規則を表しています。たとえば、図の **A** の時点では、この時間に予想されるアプリケーションのワークロードの増加に対応するために、ロール インスタンスの最小数が 2 個から 4 個に増えています。図の **B** の時点では、アプリケーションのランニング コストを制御するために、ロール インスタンスの数が 5 個を超えないように抑えられています。

-   **リアクティブ規則:** 予測できない需要の変化に応じてロール インスタンスの数を変更できるようにするには、**リアクティブ規則**を使用します。図の **C** の時点では、ワークロードの減少に応じて、ブロックが自動的にロール インスタンスの数を 4 個から 3 個に減らしています。**D** の時点では、ブロックがワークロードの増加を検出し、実行するロール インスタンスの数を自動的に 3 個から 4 個に増やしています。

ブロックは、構成設定を次の 2 つのストアに格納します。

-   **規則ストア:** 規則ストアは、ビジネス構成を保持します。つまり、Azure アプリケーション用に定義したすべてのオートスケーリング規則の一覧が格納されます。このストアは、通常は、自動スケール アプリケーション ブロックが読み取れる場所 (たとえば、Azure BLOB ストレージやローカル ファイル内) にある XML ファイルです。

-   **サービス情報ストア:** サービス情報ストアは、運用構成を格納します。運用構成とは、Azure アプリケーションのサービス モデルです。このサービス モデルには、ブロックがターゲット Azure アプリケーションからデータ ポイントを収集してスケーリング操作を行うために必要な、Azure アプリケーションについてのすべての情報 (ロール名、ストレージ アカウントの詳細など) が含まれます。

## ターゲット Azure アプリケーションからのパフォーマンス カウンター データの収集

リアクティブ規則は、ロールのパフォーマンス カウンター データを規則定義の一部として使用できます。たとえば、リアクティブ規則は Azure ロールの CPU 使用率を監視して、ブロックがスケーリング操作を開始する必要があるかどうかを判断できます。ブロックは、パフォーマンス カウンター データを Azure Storage の **WADPerformanceCountersTable** という名前の Azure 診断テーブルから読み取ります。

既定では、Azure はパフォーマンス カウンター データを Azure Storage の Azure 診断テーブルに書き込みません。そのため、パフォーマンス カウンター データの収集対象のロールで、このデータを保存するための変更を行う必要があります。アプリケーションでパフォーマンス カウンターを有効にする方法の詳細については、[Azure でのパフォーマンス カウンターの使用][]に関するページを参照してください。

## オートスケーリング アプリケーション ブロックのホスト アプリケーションのセットアップ

自動スケール アプリケーション ブロックは、Azure ロールまたはオンプレミスのアプリケーションでホストできます。通常、自動スケール アプリケーション ブロックは、自動的に規模を調整するターゲット アプリケーションとは別のアプリケーションでホストされます。このセクションでは、ホスト アプリケーションの構成方法に関するガイドラインを示します。

### 自動スケール アプリケーション ブロック NuGet パッケージの取得

Visual Studio プロジェクトで自動スケール アプリケーション ブロックを使用する前に、自動スケール アプリケーション ブロック バイナリを取得し、それへの参照をプロジェクトに追加する必要があります。NuGet Visual Studio 拡張機能を使用すると、Visual Studio および Visual Web Developer でのライブラリやツールのインストールと更新を簡単に行うことができます。自動スケール アプリケーション ブロック NuGet パッケージは、自動スケール アプリケーション ブロック API を取得するための最も簡単な方法です。**NuGet** の詳細と、**NuGet** Visual Studio 拡張機能のインストールおよび使用の方法については、[NuGet][] Web サイトを参照してください。

NuGet パッケージ マネージャーのインストール後、アプリケーションに自動スケール NuGet パッケージをインストールするには、次の手順を行います。

1.  **NuGet パッケージ マネージャー コンソール**のウィンドウを開きます。**[ツール]** メニューで **[ライブラリ パッケージ マネージャー]**、**[パッケージ マネージャー コンソール]** の順に選択します。

2.  NuGet パッケージ マネージャー コンソールのウィンドウに次のコマンドを入力します。

        PM> Install-Package EnterpriseLibrary.WindowsAzure.Autoscaling

NuGet パッケージをインストールすると、自動スケール アプリケーション ブロックを使うために必要なすべてのアセンブリおよび参照によって、プロジェクトが更新されます。これで、プロジェクトには自動スケール規則定義と自動スケール サービス情報の XML スキーマ ファイルが含まれるようになります。また、自動スケール アプリケーション ブロックについての重要な情報が記載された readme ファイルも含まれます。

![files configured by autoscaling NuGet package](./media/cloud-services-dotnet-autoscaling-application-block/auotscaling02.png)


### ターゲット フレームワークを .NET Framework 4 に設定

プロジェクトは .NET Framework 4 をターゲットにする必要があります。ターゲット フレームワークを変更または確認するには、次の手順を実行します。

1.  ソリューション エクスプローラーでプロジェクト名を右クリックし、**[プロパティ]** を選択します。

2.  [プロパティ] ウィンドウの **[アプリケーション]** タブで、ターゲット フレームワークが **.NET Framework 4** に設定されていることを確認します。

	![image](./media/cloud-services-dotnet-autoscaling-application-block/autoscaling03.png)


### 名前空間参照の追加

プログラムを使用して自動スケール アプリケーション ブロックにアクセスするすべての C# ファイルの冒頭部分に、名前空間を宣言する次のコードを追加します。

    using Microsoft.Practices.EnterpriseLibrary.Common.Configuration;
    using Microsoft.Practices.EnterpriseLibrary.WindowsAzure.Autoscaling;

## 方法: オートスケーラーをインスタンス化および実行する

**IServiceLocator.GetInstance** メソッドを使用してオートスケーラーをインスタンス化し、**Autoscaler.Start** メソッドを呼び出して**オートスケーラー**を実行します。

    Autoscaler scaler =
        EnterpriseLibraryContainer.Current.GetInstance<Autoscaler>();
    scaler.Start();

## 方法: サービス モデルを定義する

通常は、サービス モデル (サブスクリプション、ホストされるサービス、ロール、およびストレージ アカウントの情報を含む Azure 環境の記述) を、XML ファイルに格納します。この XML ファイル用のスキーマのコピーは、プロジェクトの **AutoscalingServiceModel.xsd** ファイル内にあります。Visual Studio でサービス モデル XML ファイルを編集するときに、このスキーマが Intellisense および検証を提供します。

プロジェクトに **services.xml** という名前の新しい XML ファイルを作成します。

Visual Studio で、サービス モデル ファイルが出力フォルダーにコピーされていることを確認する必要があります。これを行うには、次の手順を実行します。

1.  ファイルを右クリックし、**[プロパティ]** を選択します。

2.  プロパティ ウィンドウで **[出力ディレクトリにコピー]** の値を **[常にコピーする]** に設定します。

    ![[出力ディレクトリにコピー] の値を設定する](./media/cloud-services-dotnet-autoscaling-application-block/autoscaling04.png)


	次のコード サンプルは、**services.xml** ファイル内のサービス モデルの例を示しています。

    <?xml version="1.0" encoding="utf-8" ?> <serviceModel xmlns="http://schemas.microsoft.com/practices/2011/entlib/autoscaling/serviceModel"> <subscriptions> <subscription name="[subscriptionname]"
                      certificateThumbprint="[managementcertificatethumbprint]"
                      subscriptionId="[subscriptionid]"
                      certificateStoreLocation="CurrentUser"
                      certificateStoreName="My"> <services> <service dnsPrefix="[hostedservicednsprefix]" slot="Staging"> <roles> <role alias="AutoscalingApplicationRole"
                      roleName="[targetrolename]"
                      wadStorageAccountName="targetstorage"/> </roles> </service> </services> <storageAccounts> <storageAccount alias="targetstorage"
              connectionString="DefaultEndpointsProtocol=https;AccountName=[storageaccountname];AccountKey=[storageaccountkey]"> </storageAccount> </storageAccounts> </subscription> </subscriptions> </serviceModel>

角かっこ内の値は、環境とターゲット アプリケーションの固有の値に置き換える必要があります。これらの値の多くは、[Azure の管理ポータル][]にログインすることで確認できます。

管理ポータルにサインインします。

-   **[subscriptionname]:** オートスケーリングを使うアプリケーションを 含む Azure サブスクリプションを参照する 表示名を選択します。

-   **[subscriptionid]:** オートスケーリングを使う アプリケーションを含む Azure サブスクリプションの 一意の ID。

    1.  Azure の管理ポータルで、**[クラウド サービス]** をクリックします。

    2.  クラウド サービスの一覧で、自動スケールを使うアプリケーションをホストするサービスをクリックします。右側の [概要] ウィンドウに **[サブスクリプション ID]** が表示されます。

        ![image](./media/cloud-services-dotnet-autoscaling-application-block/autoscaling05.png)

  
	-   **[hostedservicednsprefix]:** オートスケーリングを使うホステッド サービスの DNS プレフィックス。

    1.  Azure の管理ポータルで、**[クラウド サービス]** をクリックします。

    2.  クラウド サービスの一覧で、自動スケールを使うアプリケーションをホストするサービスを見つけます。クラウド サービスの名前は **DNS Prefix** です。

        ![image](./media/cloud-services-dotnet-autoscaling-application-block/autoscaling06.png)
 
	-   **[targetrolename]:** オートスケーリング規則のターゲットであるロールの名前。

    1.  Azure の管理ポータルで、**[クラウド サービス]** をクリックします。

    2.  クラウド サービスの一覧で、オートスケーリングを使用するアプリケーションがホストされるサービスをクリックし、**[インスタンス]** をクリックします。**[ロール]* 列にターゲット ロールの名前が表示されます。

        ![image](./media/cloud-services-dotnet-autoscaling-application-block/autoscaling07.png)


	-   **[storageaccountname]** および **[storageaccountkey]:** ターゲット Azure アプリケーションで使用している Azure ストレージ アカウントの名前。

    1.  Azure の管理ポータルで、**[ストレージ]** をクリックします。

    2.  ストレージ アカウントの一覧で、使用しているストレージ アカウントを選択します。**[名前]** 列に**名前**が表示されます。

    3.  画面の下部の **[キーの管理]** をクリックしてプライマリ アクセス キーを取得します。

        ![image](./media/cloud-services-dotnet-autoscaling-application-block/autoscaling08.png)
  
 
	-   **[managementcertificatethumbprint]:** ターゲット アプリケーションへのスケーリング要求をセキュリティで保護するためにブロックが使用する管理証明書の**サムプリント**。

    1.  Azure の管理ポータルで、[**設定**] をクリックします。

    2.  **[サムプリント]** 列に**サムプリント**が表示されます。

        ![image](./media/cloud-services-dotnet-autoscaling-application-block/autoscaling09.png)
 

サービス モデル ファイルの内容の詳細については、[サービス情報データの格納][]に関するページを参照してください。

## 方法: オートスケーリング規則を定義する

通常は、ターゲット アプリケーションのロール インスタンスの数を制御する自動スケール規則を、XML ファイルに格納します。この XML ファイル用のスキーマのコピーは、プロジェクトの **AutoscalingRules.xsd** ファイル内にあります。Visual Studio で XML ファイルを編集するときに、このスキーマが Intellisense および検証を提供します。

プロジェクトに **rules.xml** という名前の新しい XML ファイルを作成します。

Visual Studio で、規則ファイルが出力フォルダーにコピーされていることを確認する必要があります。これを行うには、次の手順を実行します。

1.  ファイルを右クリックし、**[プロパティ]** を選択します。

2.  プロパティ ウィンドウで **[出力ディレクトリにコピー]** の値を **[常にコピーする]** に設定します。

次のコード サンプルは、**rules.xml** ファイル内の規則セットの例を示しています。

    <?xml version="1.0" encoding="utf-8" ?>
    <rules xmlns="http://schemas.microsoft.com/practices/2011/entlib/autoscaling/rules">
      <constraintRules>
        <rule name="default" enabled="true" rank="1" description="The default constraint rule">
          <actions>
            <range min="2" max="6" target="AutoscalingApplicationRole"/>
          </actions>
        </rule>
      </constraintRules>
      <reactiveRules>
        <rule name="ScaleUpOnHighUtilization" rank="10" description="Scale up the web role" enabled="true" >
          <when>
            <any>
               <greaterOrEqual operand="WebRoleA_CPU_Avg_5m" than="60"/>
            </any>
          </when>
          <actions>
            <scale target="AutoscalingApplicationRole" by="1"/>
          </actions>
        </rule>
        <rule name="ScaleDownOnLowUtilization" rank="10" description="Scale up the web role" enabled="true" >
          <when>
            <all>
              <less operand="WebRoleA_CPU_Avg_5m" than="60"/>
            </all>
          </when>
          <actions>
            <scale target="AutoscalingApplicationRole" by="-1"/>
          </actions>
        </rule>
      </reactiveRules>
      <operands>
        <performanceCounter alias="WebRoleA_CPU_Avg_5m"
          performanceCounterName="\Processor(_Total)% Processor Time"
          source ="AutoscalingApplicationRole"
          timespan="00:05:00" aggregate="Average"/>
      </operands>
    </rules>

この例では、3 つのオートスケーリング規則 (**制約規則**が 1 つと、**リアクティブ規則**が 2 つ) があります。それらは **AutoscalingApplicationRole** という名前のターゲットに対して適用されます。このターゲット名は、**サービス モデル**で定義されているロールのエイリアスです。

-   制約規則は常にアクティブで、ロール インスタンスの最小数を 2、ロール インスタンスの最大数を 6 に設定しています。

-   両方のリアクティブ規則は、**WebRoleA\_CPU\_Avg\_5m** という名前の **オペランド**を使用します。このオペランドは、**AutoscalingApplicationRole** という名前の Azure ロールでの直前の 5 分間の平均 CPU 使用率を計算します。 このロールは、**サービス モデル**で定義されています。

-   **ScaleUpOnHighUtilization** という名前のリアクティブ規則は、直前の 5 分間の平均 CPU 使用率が 60% 以上である場合に、ターゲット ロールのインスタンス数を 1 つ増やします。

-   **ScaleDownOnLowUtilization** という名前のリアクティブ規則は、直前の 5 分間の平均 CPU 使用率が 60% より低い場合に、ターゲット ロールのインスタンス数を 1 つ減らします。

## 方法: オートスケーリング アプリケーション ブロックを構成する

サービス モデルおよび自動スケール規則を定義したら、次に、それらを使用する自動スケール アプリケーション ブロックを構成する必要があります。この運用構成情報は、アプリケーション構成ファイルに格納されます。

既定では、自動スケール アプリケーション ブロックは、自動スケール規則およびサービス モデルが BLOB に格納されていることを想定しています。この例では、それらをローカル ファイル システムから読み込むようにブロックを構成します。

### ホスト アプリケーションでの自動スケール アプリケーション ブロックの構成

1.  ソリューション エクスプローラーで **App.config** ファイルを右クリックし、**[構成ファイルの編集]** をクリックします。

2.  **[ブロック]** メニューの **[オートスケーリングの設定の追加]** をクリックします。
	![image](./media/cloud-services-dotnet-autoscaling-application-block/autoscaling10.png)
  
3.  **[自動スケールの設定]** を展開し、**[データ ポイント格納ストレージ アカウント]** の横にある省略記号 (...) をクリックして、ブロックによって収集されたデータ ポイントの格納先となる Azure ストレージ アカウントの **[アカウント名]** および **[アカウント キー]** を追加します (これらの値の確認方法がわからない場合は、「方法: サービス モデルを定義する」を参照してください)。その後、**[OK]** をクリックします。

	![image](./media/cloud-services-dotnet-autoscaling-application-block/autoscaling11.png)

4.  **[オートスケーリングの設定]** セクションを展開して、**[規則ストア]** および **[サービス情報ストア]** セクションを表示します。既定では、これらは Azure BLOB ストレージを使用するように構成されています。
	![image](./media/cloud-services-dotnet-autoscaling-application-block/autoscaling12.png)


5.  **[規則ストア]** の横のプラス記号 (+) をクリックし、**[規則ストアの設定]** をポイントして、**[ローカル ファイル規則ストアの使用]**、**[はい]** の順にクリックします。

6.  ** [ファイル名]** ボックスで「** rules.xml**」と入力します。これは、オートスケーリング規則を含むファイルの名前です。
	![image](./media/cloud-services-dotnet-autoscaling-application-block/autoscaling13.png)


7.  **[サービス情報ストア]** の横のプラス記号 (+) をクリックし、**[サービス情報ストアの設定]** をポイントして、**[ローカル ファイル サービス情報ストアの使用]**、**[はい]** の順にクリックします。

8.  **[ファイル名]** ボックスで「**services.xml**」と入力します。これは、オートスケーリング規則を含むファイルの名前です。
	![image](./media/cloud-services-dotnet-autoscaling-application-block/autoscaling14.png)


9.  Enterprise Library の構成ウィンドウで、**[ファイル]** メニューの **[保存]** をクリックして、構成の変更を保存します。次に、Enterprise Library の構成ウィンドウで、**[ファイル]** メニューの **[終了]** をクリックします。

自動スケール アプリケーション ブロックが行っている操作についての詳細な情報を取得するには、書き込まれているログ メッセージをキャプチャする必要があります。たとえば、ブロックをコンソール アプリケーションでホストしている場合は、Visual Studio の出力ウィンドウでログ メッセージを見ることができます。次のセクションでは、この動作の構成方法を説明します。

### 自動スケール アプリケーション ブロックのホスト アプリケーションでのログの構成

1.  Visual Studio のソリューション エクスプローラーで **App.config** ファイルをダブルクリックして、エディターで開きます。次に、以下の例で示されているように、**system.diagnostics** セクションを追加します。

        <?xml version="1.0" encoding="utf-8" ?>
        <configuration>
          ...
          <system.diagnostics>
            <sources>
              <sourcename="Autoscaling General" switchName="SourceSwitch" switchType="System.Diagnostics.SourceSwitch" />
              <sourcename="Autoscaling Updates" switchName="SourceSwitch" switchType="System.Diagnostics.SourceSwitch" />
            </sources>
            <switches>
              <addname="SourceSwitch" value="Verbose, Information, Warning, Error, Critical" />
            </switches>
          </system.diagnostics>
        </configuration>

2.  変更を保存します。

これで、自動スケール アプリケーション ブロックのホスト コンソール アプリケーションを実行し、自動スケール規則がターゲット Azure アプリケーションでどのように機能するかを観察できるようになりました。ホスト コンソール アプリケーションを実行すると、Visual Studio の出力ウィンドウに次のようなメッセージが表示されます。これらのログ メッセージを見ると、ブロックの動作を理解しやすくなります。たとえば、メッセージによって、どの規則が該当し、どのような操作をブロックが行ったかが示されます。

    Autoscaling General Verbose: 1002 : Rule match.
    [BEGIN DATA]{"EvaluationId":"6b27dfa0-b671-44a3-adf1-bb1e0b7c3726",
    "MatchingRules":[{"RuleName":"default","RuleDescription":"The default constraint rule",
    "Targets":["AutoscalingApplicationRole"]},{"RuleName":"ScaleUp","RuleDescription":"Scale up the web role",
    "Targets":[]},{"RuleName":"ScaleDown","RuleDescription":"Scale up the web role","Targets":[]}]}

    Autoscaling Updates Verbose: 3001 : The current deployment configuration for a hosted service is about to be checked
    to determine if a change is required (for role scaling or changes to settings).
    [BEGIN DATA]{"EvaluationId":"6b27dfa0-b671-44a3-adf1-bb1e0b7c3726",
    "HostedServiceDetails":{"Subscription":"AutoscalingHowTo","HostedService":"autoscalingtarget",
    "DeploymentSlot":"Staging"},"ScaleRequests":{"AutoscalingApplicationRole":{"Min":2,"Max":6,"AbsoluteDelta":0,
    "RelativeDelta":0,"MatchingRules":"default"}},"SettingChangeRequests":{}}

    Autoscaling Updates Verbose: 3003 : Role scaling requests for hosted service about to be submitted.
    [BEGIN DATA]{"EvaluationId":"6b27dfa0-b671-44a3-adf1-bb1e0b7c3726",
    "HostedServiceDetails":{"Subscription":"AutoscalingHowTo","HostedService":"autoscalingtarget",
    "DeploymentSlot":"Staging"},
    "ScaleRequests":{"AutoscalingApplicationRole":
    {"Min":2,"Max":6,"AbsoluteDelta":0,"RelativeDelta":0,"MatchingRules":"default"}},
    "SettingChangeRequests":{},"InstanceChanges":{"AutoscalingApplicationRole":{"CurrentValue":1,"DesiredValue":2}},
    "SettingChanges":{}}

    Autoscaling Updates Information: 3002 : Role configuration changes for deployment were submitted.
    [BEGIN DATA]{"EvaluationId":"6b27dfa0-b671-44a3-adf1-bb1e0b7c3726",
    "HostedServiceDetails":{"Subscription":"AutoscalingHowTo","HostedService":"autoscalingtarget",
    "DeploymentSlot":"Staging"},"ScaleRequests":{"AutoscalingApplicationRole":{"Min":2,"Max":6,"AbsoluteDelta":0,
    "RelativeDelta":0,"MatchingRules":"default"}},"SettingChangeRequests":{},
    "InstanceChanges":{"AutoscalingApplicationRole":{"CurrentValue":1,"DesiredValue":2}},
    "SettingChanges":{},"RequestID":"f8ca3ada07c24559b1cb075534f02d44"}

## 次のステップ

これで、自動スケール アプリケーション ブロックの使用の基本を学習できました。より複雑な自動スケール シナリオを実装する方法については、次のリンク先を参照してください。

-   [ワーカー ロールでのオートスケーリング アプリケーション ブロックのホスト][]
-   [調整動作の実装][]
-   [規則の順位と調整について][]
-   [オートスケーリング アプリケーション ブロックの拡張と変更][]
-   [頻繁な変動を防ぎ、コストを最適化するための最適化スタビライザーの使用][]
-   [通知と手動スケーリングの使用][]
-   [スケール グループの定義][]
-   [Windows PowerShell でブロックを操作するための WASABiCmdlets の使用][]
-   [Enterprise Library 5.0 Integration Pack for Azure 開発者ガイド][]
-   [Sage によるオートスケーリングを使用した Azure のホスティング コスト削減方法][]
-   [Azure の自動スケールによる TechNet と MSDN のホスティング コストと環境への影響の軽減][]

  [Microsoft Enterprise Library 5.0 Integration Pack for Azure]: http://go.microsoft.com/fwlink/?LinkID=235134
  [Azure でのパフォーマンス カウンターの使用]: http://www.windowsazure.com/develop/net/common-tasks/performance-profiling/
  [NuGet]: http://nuget.org/
  [Azure の管理ポータル]: http://manage.windowsazure.com
  [サービス情報データの格納]: http://msdn.microsoft.com/library/hh680878(PandP.50).aspx
  [ワーカー ロールでのオートスケーリング アプリケーション ブロックのホスト]: http://msdn.microsoft.com/library/hh680914(PandP.50).aspx
  [調整動作の実装]: http://msdn.microsoft.com/library/hh680896(PandP.50).aspx
  [規則の順位と調整について]: http://msdn.microsoft.com/library/hh680923(PandP.50).aspx
  [オートスケーリング アプリケーション ブロックの拡張と変更]: http://msdn.microsoft.com/library/hh680889(PandP.50).aspx
  [頻繁な変動を防ぎ、コストを最適化するための最適化スタビライザーの使用]: http://msdn.microsoft.com/library/hh680951(PandP.50).aspx
  [通知と手動スケーリングの使用]: http://msdn.microsoft.com/library/hh680885(PandP.50).aspx
  [スケール グループの定義]: http://msdn.microsoft.com/library/hh680902(PandP.50).aspx
  [Windows PowerShell でブロックを操作するための WASABiCmdlets の使用]: http://msdn.microsoft.com/library/hh680938(PandP.50).aspx
  [Enterprise Library 5.0 Integration Pack for Azure 開発者ガイド]: http://msdn.microsoft.com/library/hh680949(PandP.50).aspx
  [Sage によるオートスケーリングを使用した Azure のホスティング コスト削減方法]: http://msdn.microsoft.com/library/jj838716(PandP.50).aspx
  [Azure の自動スケールによる TechNet と MSDN のホスティング コストと環境への影響の軽減]: http://msdn.microsoft.com/library/jj838718(PandP.50).aspx
 

<!---HONumber=August15_HO9-->