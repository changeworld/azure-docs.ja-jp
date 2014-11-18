<properties urlDisplayName="Autoscaling" pageTitle="オートスケーリング アプリケーション ブロックの使用 (.NET) - Azure" metaKeywords="Azure autoscaling, Azure autoscaling C#, Azure autoscaling .NET" description="Azure でオートスケーリング アプリケーションを使用する方法について説明します。コード サンプルは C# で記述され、.NET API を利用しています。" metaCanonical="" services="cloud-services" documentationCenter=".NET" title="オートスケーリング アプリケーション ブロックの使用方法" authors="timlt" solutions="" manager="timlt" editor="" />

<tags ms.service="cloud-services" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="timlt" />

# オートスケーリング アプリケーション ブロックの使用方法

このガイドでは、[Microsoft Enterprise Library 5.0 Integration Pack for Azure][Microsoft Enterprise Library 5.0 Integration Pack for Azure]
 のオートスケーリング アプリケーション ブロックを使用する一般的なシナリオの実行方法を
示します。サンプルは C\\
 で記述され、.NET API を利用しています。紹介するシナリオは、**ブロックのホスティング**、
**制約規則の使用**、および**リアクティブ規則の使用**です。オートスケーリング アプリケーション ブロックの詳細については、
「[次のステップ][次のステップ]」を参照してください。

## 目次

[オートスケーリング アプリケーション ブロックとは][オートスケーリング アプリケーション ブロックとは]
 [概念][概念]
 [ターゲット Azure アプリケーションからのパフォーマンス カウンター データの収集][ターゲット Azure アプリケーションからのパフォーマンス カウンター データの収集]
 [オートスケーリング アプリケーション ブロックのホスト アプリケーションのセットアップ][オートスケーリング アプリケーション ブロックのホスト アプリケーションのセットアップ]
 [方法: オートスケーラーをインスタンス化および実行する][方法: オートスケーラーをインスタンス化および実行する] [方法: サービス モデルを定義する][方法: サービス モデルを定義する]
 [方法: オートスケーリング規則を定義する][方法: オートスケーリング規則を定義する]
 [方法: オートスケーリング アプリケーション ブロックを構成する][方法: オートスケーリング アプリケーション ブロックを構成する]
 [次のステップ][次のステップ]

## <span id="WhatIs"></span> </a>オートスケーリング アプリケーション ブロックとは

オートスケーリング アプリケーション ブロックでは、
アプリケーション用に個別に定義した規則に基づいて、
Microsoft Azure アプリケーションの規模を自動的に調整できます。これらの規則を使用すると、
Azure アプリケーションがワークロードの変化に対応してスループットを維持できるようにすることができます。
同時に、Azure での
アプリケーションのホストに関連するコストを制御することもできます。ブロックは、
アプリケーション内のロール インスタンスの数を増減するスケーリングだけでなく、
アプリケーションの特定の機能の調整、
カスタム定義の操作の使用など、
その他のスケーリング操作も利用できるようにします。

ブロックを Azure ロールでホストするか、
内部設置型アプリケーションでホストするかを選択できます。

オートスケーリング アプリケーション ブロックは、[Microsoft Enterprise Library 5.0 Integration Pack for Azure][Microsoft Enterprise Library 5.0 Integration Pack for Azure] の一部です。

## <span id="Concepts"></span> </a>概念

次の図では、緑の線が、2 日間にわたる
Azure ロールの実行インスタンスの数を示しています。インスタンスの数は、
オートスケーリング規則のセットに応じて、
時間の経過と共に自動的に変化します。

![サンプル オートスケーリングの図][サンプル オートスケーリングの図]

ブロックは、アプリケーションのオートスケーリングの動作を定義するために、
次の 2 種類の規則を使用します。

-   **制約規則:** インスタンス数の上限と下限を設定する場合、
    たとえば毎朝 8:00 から 10:00 まで最小で 4 個、
    最大で 6 個のインスタンスを必要とする場合、
    **制約規則**を使用します。図では、赤と青の線が制約規則を
    表しています。たとえば、
    図の **A** の時点では、
    この時間に予想されるアプリケーションのワークロードの増加に対応するために、
    ロール インスタンスの最小数が 2 から 4 に増えています。図の **B** の時点では、
    アプリケーションのランニング コストを制御するために、
    ロール インスタンスの数が 5 個を超えないように抑えられています。

-   **リアクティブ規則:** 予測できない需要の変化に応じて
    ロール インスタンスの数を変更できるようにするには、
    **リアクティブ規則**を使用します。図の **C** の時点では、
    ワークロードの減少に応じて、
    ブロックが自動的にロール インスタンスの数を 4 から 3 に減らしています。**D** の時点では、
    ブロックがワークロードの増加を検出し、
    実行するロール インスタンスの数を自動的に 3 から 4 に増やしています。

ブロックは、構成設定を次の 2 つのストアに格納します。

-   **規則ストア:** 規則ストアは、ビジネス構成を保持します。
    つまり、Azure アプリケーション用に定義した
    すべてのオートスケーリング規則の一覧が格納されます。このストアは、通常は、
    オートスケーリング アプリケーション ブロックが読み取れる場所 (たとえば、Azure BLOB ストレージやローカル ファイル内)
     にある XML ファイルです。

-   **サービス情報ストア:** サービス情報ストアは、運用構成を格納します。
    これは、Azure アプリケーションの
    サービス モデルです。このサービス モデルには、
    ブロックがターゲット Azure アプリケーションから
    データ ポイントを収集してスケーリング操作を行うために必要な、
    Azure アプリケーションについてのすべての情報 (ロール名、ストレージ アカウントの詳細など)
    が含まれます。

## <span id="PerfCounter"></span> </a>ターゲット Azure アプリケーションからのパフォーマンス カウンター データの収集

リアクティブ規則は、ロールのパフォーマンス カウンター データを
規則定義の一部として使用できます。たとえば、リアクティブ規則は Azure ロールの CPU 使用率を監視して、
ブロックがスケーリング操作を
開始する必要があるかどうかを判断できます。ブロックは、パフォーマンス カウンター データを
Azure Storage の
**WADPerformanceCountersTable** という名前の Azure 診断テーブルから読み取ります。

既定では、Azure はパフォーマンス カウンター データを
 Azure Storage の Azure 診断テーブルに書き込みません。そのため、
パフォーマンス カウンター データの収集対象のロールで、
このデータを保存するための変更を行う必要があります。アプリケーションでパフォーマンス カウンターを有効にする方法の詳細については、
[Azure でのパフォーマンス カウンターの使用に関するページ][Azure でのパフォーマンス カウンターの使用に関するページ]を参照してください。

## <span id="CreateHost"></span> </a>オートスケーリング アプリケーション ブロックのホスト アプリケーションのセットアップ

オートスケーリング アプリケーション ブロックは、
Azure ロールまたは内部設置型アプリケーションでホストできます。通常、オートスケーリング アプリケーション ブロックは、
自動的に規模を調整するターゲット アプリケーションとは
別のアプリケーションでホストされます。このセクションでは、
ホスト アプリケーションの構成方法に関するガイドラインを示します。

### オートスケーリング アプリケーション ブロック NuGet パッケージの取得

Visual Studio プロジェクトでオートスケーリング アプリケーション ブロックを使用する前に、
オートスケーリング アプリケーション ブロック バイナリを取得し、
それへの参照をプロジェクトに追加する必要があります。NuGet Visual Studio 拡張機能を使用すると、
Visual Studio および Visual Web Developer での
ライブラリやツールのインストールと更新を簡単に行うことができます。オートスケーリング アプリケーション ブロック NuGet パッケージは、
オートスケーリング アプリケーション ブロック API を
取得するための最も簡単な方法です。**NuGet** の詳細と、
**NuGet** Visual Studio 拡張機能のインストールおよび使用の方法については、[NuGet][NuGet]
Web サイトを参照してください。

NuGet パッケージ マネージャーのインストール後、
アプリケーションにオートスケーリング NuGet パッケージをインストールするには、次の手順を実行します。

1.  **NuGet パッケージ マネージャー コンソール**のウィンドウを開きます。**[ツール]**
     メニューで **[ライブラリ パッケージ マネージャー]**、
    **[パッケージ マネージャー コンソール]** の順に選択します。

2.  NuGet パッケージ マネージャー コンソールのウィンドウに
    次のコマンドを入力します。

        PM> Install-Package EnterpriseLibrary.WindowsAzure.Autoscaling

NuGet パッケージをインストールすると、
オートスケーリング アプリケーション ブロックを使うために必要なすべてのアセンブリおよび参照によって、
プロジェクトが更新されます。これで、プロジェクトには
オートスケーリング規則定義とオートスケーリング サービス情報の XML スキーマ ファイルが含まれるようになります。
また、オートスケーリング アプリケーション ブロックについての
重要な情報が記載された readme ファイルも含まれます。

![オートスケーリング NuGet パッケージによって構成されたファイル][オートスケーリング NuGet パッケージによって構成されたファイル]

### ターゲット フレームワークを .NET Framework 4 に設定

プロジェクトは .NET Framework 4 をターゲットにする必要があります。
ターゲット フレームワークを変更または確認するには、次の手順を実行します。

1.  ソリューション エクスプローラーでプロジェクト名を右クリックし、
    **[プロパティ]** を選択します。

2.  [プロパティ] ウィンドウの **[アプリケーション]** タブで、ターゲット フレームワークが **.NET Framework 4** に設定されていることを確認します。

    ![image][image]

### 名前空間参照の追加

プログラムを使用して
オートスケーリング アプリケーション ブロックにアクセスするすべての C\# ファイルの冒頭部分に、
名前空間を宣言する次のコードを追加します。

    using Microsoft.Practices.EnterpriseLibrary.Common.Configuration;
    using Microsoft.Practices.EnterpriseLibrary.WindowsAzure.Autoscaling;

## <span id="Instantiate"></span> </a>方法: オートスケーラーをインスタンス化および実行する

**IServiceLocator.GetInstance** メソッドを使用して
オートスケーラーをインスタンス化し、**Autoscaler.Start** メソッドを呼び出して
**オートスケーラー**を実行します。

    Autoscaler scaler =
        EnterpriseLibraryContainer.Current.GetInstance<Autoscaler>();
    scaler.Start();

## <span id="DefineServiceModel"></span> </a>方法: サービス モデルを定義する

通常は、サービス モデル
(サブスクリプション、ホステッド サービス、ロール、およびストレージ アカウントの情報を含む Microsoft Azure 環境の記述) を、
XML ファイルに格納します。この XML ファイル用のスキーマのコピーは、
プロジェクトの
**AutoscalingServiceModel.xsd** ファイルにあります。Visual Studio で
サービス モデル XML ファイルを編集するときに、
このスキーマが Intellisense および検証を提供します。

プロジェクトに **services.xml** という名前の新しい XML ファイルを作成します。

Visual Studio で、サービス モデル ファイルが
出力フォルダーにコピーされていることを確認する必要があります。これを行うには、次の手順を実行します。

1.  ファイルを右クリックし、**[プロパティ]** を選択します。

2.  プロパティ ウィンドウで **[出力ディレクトリにコピー]** の値を
    **[常にコピーする]** に設定します。

    ![[出力ディレクトリにコピー] の値を設定する][]

    次のコード サンプルは、**services.xml** ファイル内のサービス モデルの例を示しています。

    <servicemodel xmlns="http://schemas.microsoft.com/practices/2011/entlib/autoscaling/serviceModel">
     <subscriptions>
     <subscription name="[subscriptionname]" certificatethumbprint="[managementcertificatethumbprint]" subscriptionid="[subscriptionid]" certificatestorelocation="CurrentUser" certificatestorename="My">
     <services>
     <service dnsprefix="[hostedservicednsprefix]" slot="Staging">
     <roles>
     <role alias="AutoscalingApplicationRole" rolename="[targetrolename]" wadstorageaccountname="targetstorage"></role>
     </roles>
     </service>
     </services>
     <storageaccounts>
     <storageaccount alias="targetstorage" connectionstring="DefaultEndpointsProtocol=https;AccountName=[storageaccountname];AccountKey=[storageaccountkey]">
     </storageaccount>
     </storageaccounts>
     </subscription>
     </subscriptions>
    </servicemodel>

角かっこ内の値は、環境とターゲット アプリケーションの固有の値に
置き換える必要があります。それらの値の多くを見つけるには、
[Azure 管理ポータル][Azure 管理ポータル]にログインする必要があります。

管理ポータルにサインインします。

-   **[subscriptionname]:** オートスケーリングを使うアプリケーションを含む
    Azure サブスクリプションを
    参照する表示名を選択します。

-   **[subscriptionid]:** オートスケーリングを使う
    アプリケーションを含む
    Azure サブスクリプションの一意の ID。

    1.  Azure 管理ポータルで、
        **[クラウド サービス]** をクリックします。

    2.  クラウド サービスの一覧で、オートスケーリングを使うアプリケーションを
        ホストするサービスをクリックします。右側の [概要] ウィンドウに
        **[サブスクリプション ID]**
         が表示されます。

        ![image][1]

    -   **[ホステッド サービス DNS プレフィックス]:** オートスケーリングを使うホステッド サービスの DNS プレフィックス。

    1.  Azure 管理ポータルで、
        **[クラウド サービス]** をクリックします。

    2.  クラウド サービスの一覧で、オートスケーリングを使うアプリケーションを
        ホストするサービスを見つけます。クラウド サービスの
        名前は **DNS Prefix** です。

        ![image][2]

    -   **[ターゲット ロール名]:** オートスケーリング規則のターゲットであるロールの名前。

    1.  Azure 管理ポータルで、
        **[クラウド サービス]** をクリックします。

    2.  クラウド サービスの一覧で、
        オートスケーリングを使うアプリケーションをホストするサービスをクリックし、
        **[インスタンス]** をクリックします。[\*\*ロール\*] 列に
        ターゲット ロールの名前が表示されます。

        ![image][3]

    -   **[ストレージ アカウント名]** および **[ストレージ アカウント キー]:** ターゲット Azure アプリケーションで使用している Azure ストレージ アカウントの名前。

    1.  Azure 管理ポータルで、
        **[ストレージ]** をクリックします。

    2.  ストレージ アカウントの一覧で、使用しているストレージ アカウントを選択します。
        **[名前]** 列 に**名前**が表示されます。

    3.  画面の下部の **[キーの管理]** ボタンをクリックして
        プライマリ アクセス キーを取得します。

        ![image][4]

    -   **[管理証明書サムプリント]:** ターゲット アプリケーションへのスケーリング要求をセキュリティで保護するためにブロックが使用する管理証明書の**サムプリント**。

    1.  Azure 管理ポータルで、
        **[設定]** をクリックします。

    2.  **[サムプリント]** 列に**サムプリント**が表示されます。

        ![image][5]

サービス モデル ファイルの内容の詳細については、
[サービス情報データの格納][サービス情報データの格納]に関するページを参照してください。

## <span id="DefineAutoscalingRules"></span> </a>方法: オートスケーリング規則を定義する

通常は、ターゲット アプリケーションのロール インスタンスの数を制御する
オートスケーリング規則を、XML ファイルに格納します。この XML ファイル用のスキーマのコピーは、
プロジェクトの **AutoscalingRules.xsd**
 ファイルにあります。Visual Studio で XML ファイルを編集するときに、
このスキーマが Intellisense および検証を提供します。

プロジェクトに **rules.xml** という名前の新しい XML ファイルを作成します。

Visual Studio で、規則ファイルが
出力フォルダーにコピーされていることを確認する必要があります。これを行うには、次の手順を実行します。

1.  ファイルを右クリックし、**[プロパティ]** を選択します。

2.  プロパティ ウィンドウで **[出力ディレクトリにコピー]** の値を
    **[常にコピーする]** に設定します。

次のコード サンプルは、**rules.xml**
 ファイル内の規則セットの例を示しています。

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
          performanceCounterName="\Processor(_Total)\% Processor Time"
          source ="AutoscalingApplicationRole"
          timespan="00:05:00" aggregate="Average"/>
      </operands>
    </rules>

この例では、3 つのオートスケーリング規則
(**制約規則**が 1 つと、**リアクティブ規則**が 2 つ) があります。それらは
**AutoscalingApplicationRole** という名前のターゲットに対して適用されます。このターゲット名は、
**サービス モデル**で定義されているロールのエイリアスです。

-   制約規則は常にアクティブで、
    ロール インスタンスの最小数を 2、
    ロール インスタンスの最大数を 6 に設定しています。

-   両方のリアクティブ規則は、
    **WebRoleA\_CPU\_Avg\_5m** という名前の
    **オペランド**を使用します。このオペランドは、
    **AutoscalingApplicationRole** という名前の Azure ロールでの直前の 5 分間の平均 CPU 使用率を計算します。このロールは、
    **サービス モデル**で 定義されています。

-   **ScaleUpOnHighUtilization** という名前のリアクティブ規則は、
    直前の 5 分間の平均 CPU 使用率が
    60% 以上である場合に、
    ターゲット ロールのインスタンス数を 1 つ増やします。

-   **ScaleDownOnLowUtilization** という名前のリアクティブ規則は、
    直前の 5 分間の平均 CPU 使用率が 60% より低い場合に、
    ターゲット ロールのインスタンス数を 1 つ減らします。

## <span id="Configure"></span> </a>方法: オートスケーリング アプリケーション ブロックを構成する

サービス モデルおよびオートスケーリング規則を定義したら、
次に、それらを使用するオートスケーリング アプリケーション ブロックを構成する必要があります。この運用構成情報は、
アプリケーション構成ファイルに
格納されます。

既定では、オートスケーリング アプリケーション ブロックは、
オートスケーリング規則およびサービス モデルが BLOB に格納されていることを想定しています。この例では、
それらをローカル ファイル システムから読み込むように
ブロックを構成します。

### ホスト アプリケーションでのオートスケーリング アプリケーション ブロックの構成

1.  ソリューション エクスプローラーで **App.config** ファイルを右クリックし、
    **[構成ファイルの編集]** をクリックします。

2.  **[ブロック]** メニューの **[オートスケーリングの設定の追加]** をクリックします。
    ![画像][画像]

3.  **[オートスケーリングの設定]** を展開し、
    **[データ ポイント格納ストレージ アカウント]** の横にある省略記号 (...) をクリックして、
    ブロックによって収集されたデータ ポイントの格納先となる Azure ストレージ アカウントの **[アカウント名]** および
    **[アカウント キー]** を追加します。これらの値の確認方法がわからない場合は、
    「[方法: サービス モデルを定義する][方法: サービス モデルを定義する]」を参照してください。
    その後、**[OK]** をクリックします。

    ![image][6]

4.  **[オートスケーリングの設定]** セクションを展開して、
    **[規則ストア]** および **[サービス情報ストア]** セクションを表示します。既定では、これらは
     Azure BLOB ストレージを使用するように構成されています。
    ![画像][7]

5.  **[規則ストア]** の横のプラス記号 (+) をクリックし、
    **[規則ストアの設定]** をポイントして、
    **[ローカル ファイル規則ストアの使用]**、**[はい]** の順にクリックします。

6.  **[ファイル名]** ボックスで「 **rules.xml**」と入力します。これは、
    オートスケーリング規則を含むファイルの名前です。
    ![画像][8]

7.  **[サービス情報ストア]** の横のプラス記号 (+) をクリックし、
    **[サービス情報ストアの設定]** をポイントして、
    **[ローカル ファイル サービス情報ストアの使用]**、**[はい]** の順にクリックします。

8.  **[ファイル名]** ボックスで「**services.xml**」と入力します。これは、
    オートスケーリング規則を含むファイルの名前です。
    ![画像][9]

9.  Enterprise Library の構成ウィンドウで、**[ファイル]** メニューの **[保存]** をクリックして、
    構成の変更を保存します。次に、Enterprise Library の構成ウィンドウで、
    **[ファイル]** メニューの **[終了]** を
    クリックします。

オートスケーリング アプリケーション ブロックが行っている操作についての
詳細な情報を取得するには、
書き込まれているログ メッセージをキャプチャする必要があります。たとえば、ブロックをコンソール アプリケーションでホストしている場合は、
Visual Studio の出力ウィンドウで
ログ メッセージを見ることができます。次のセクションでは、
この動作の構成方法を説明します。

### オートスケーリング アプリケーション ブロックのホスト アプリケーションでのログの構成

1.  Visual Studio のソリューション エクスプローラーで **App.config**
     ファイルをダブルクリックして、エディターで開きます。次に、以下の 例で示されているように、
    **system.diagnostics** セクションを追加します。

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

これで、オートスケーリング アプリケーション ブロックの
ホスト コンソール アプリケーションを実行し、
オートスケーリング規則がターゲット Azure アプリケーションでどのように機能するかを観察できるようになりました。ホスト コンソール アプリケーションを実行すると、
Visual Studio の出力ウィンドウに
次のようなメッセージが表示されます。これらのログ メッセージを見ると、
ブロックの動作を理解しやすくなります。たとえば、メッセージによって、どの規則が該当し、
どのような操作をブロックが行ったかが示されます。

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

## <span id="NextSteps"></span> </a> 次のステップ

これで、オートスケーリング アプリケーション ブロックの使用の基本を学習できました。
より複雑なオートスケーリング シナリオを実装する方法については、
次のリンク先を参照してください。

-   [ワーカー ロールでのオートスケーリング アプリケーション ブロックのホスト][ワーカー ロールでのオートスケーリング アプリケーション ブロックのホスト]
-   [調整動作の実装][調整動作の実装]
-   [規則の順位と調停について][規則の順位と調停について]
-   [オートスケーリング アプリケーション ブロックの拡張と変更][オートスケーリング アプリケーション ブロックの拡張と変更]
-   [高周波振動を防ぎ、コストを最適化するための最適化スタビライザーの使用][高周波振動を防ぎ、コストを最適化するための最適化スタビライザーの使用]
-   [通知と手動スケーリングの使用][通知と手動スケーリングの使用]
-   [スケール グループの定義][スケール グループの定義]
-   [Windows PowerShell でブロックを操作するための WASABiCmdlets の使用][Windows PowerShell でブロックを操作するための WASABiCmdlets の使用]
-   [Developer's Guide to the Enterprise Library 5.0 Integration Pack for Azure (Enterprise Library 5.0 Integration Pack for Azure 開発者ガイド)][Developer's Guide to the Enterprise Library 5.0 Integration Pack for Azure (Enterprise Library 5.0 Integration Pack for Azure 開発者ガイド)]
-   [How Sage Reduces Azure Hosting Costs Using Autoscaling (Sage がオートスケーリングを使って Azure のホスティング コストを削減した方法)][How Sage Reduces Azure Hosting Costs Using Autoscaling (Sage がオートスケーリングを使って Azure のホスティング コストを削減した方法)]
-   [Reducing TechNet and MSDN hosting costs and environmental impact with autoscaling on Azure (Azure のオートスケーリングによる TechNet と MSDN のホスティング コストと環境への影響の軽減)][Reducing TechNet and MSDN hosting costs and environmental impact with autoscaling on Azure (Azure のオートスケーリングによる TechNet と MSDN のホスティング コストと環境への影響の軽減)]

  [Microsoft Enterprise Library 5.0 Integration Pack for Azure]: http://go.microsoft.com/fwlink/?LinkID=235134
  [次のステップ]: #NextSteps
  [オートスケーリング アプリケーション ブロックとは]: #WhatIs
  [概念]: #Concepts
  [ターゲット Azure アプリケーションからのパフォーマンス カウンター データの収集]: #PerfCounter
  [オートスケーリング アプリケーション ブロックのホスト アプリケーションのセットアップ]: #CreateHost
  [方法: オートスケーラーをインスタンス化および実行する]: #Instantiate
  [方法: サービス モデルを定義する]: #DefineServiceModel
  [方法: オートスケーリング規則を定義する]: #DefineAutoscalingRules
  [方法: オートスケーリング アプリケーション ブロックを構成する]: #Configure
  [サンプル オートスケーリングの図]: ./media/cloud-services-dotnet-autoscaling-application-block/autoscaling01.png
  [Azure でのパフォーマンス カウンターの使用に関するページ]: http://www.windowsazure.com/ja-jp/develop/net/common-tasks/performance-profiling/
  [NuGet]: http://nuget.org/
  [オートスケーリング NuGet パッケージによって構成されたファイル]: ./media/cloud-services-dotnet-autoscaling-application-block/auotscaling02.png
  [image]: ./media/cloud-services-dotnet-autoscaling-application-block/autoscaling03.png
  [[出力ディレクトリにコピー] の値を設定する]: ./media/cloud-services-dotnet-autoscaling-application-block/autoscaling04.png
  [Azure 管理ポータル]: http://manage.windowsazure.com
  [1]: ./media/cloud-services-dotnet-autoscaling-application-block/autoscaling05.png
  [2]: ./media/cloud-services-dotnet-autoscaling-application-block/autoscaling06.png
  [3]: ./media/cloud-services-dotnet-autoscaling-application-block/autoscaling07.png
  [4]: ./media/cloud-services-dotnet-autoscaling-application-block/autoscaling08.png
  [5]: ./media/cloud-services-dotnet-autoscaling-application-block/autoscaling09.png
  [サービス情報データの格納]: http://msdn.microsoft.com/ja-jp/library/hh680878(PandP.50).aspx
  [画像]: ./media/cloud-services-dotnet-autoscaling-application-block/autoscaling10.png
  [6]: ./media/cloud-services-dotnet-autoscaling-application-block/autoscaling11.png
  [7]: ./media/cloud-services-dotnet-autoscaling-application-block/autoscaling12.png
  [8]: ./media/cloud-services-dotnet-autoscaling-application-block/autoscaling13.png
  [9]: ./media/cloud-services-dotnet-autoscaling-application-block/autoscaling14.png
  [ワーカー ロールでのオートスケーリング アプリケーション ブロックのホスト]: http://msdn.microsoft.com/ja-jp/library/hh680914(PandP.50).aspx
  [調整動作の実装]: http://msdn.microsoft.com/ja-jp/library/hh680896(PandP.50).aspx
  [規則の順位と調停について]: http://msdn.microsoft.com/ja-jp/library/hh680923(PandP.50).aspx
  [オートスケーリング アプリケーション ブロックの拡張と変更]: http://msdn.microsoft.com/ja-jp/library/hh680889(PandP.50).aspx
  [高周波振動を防ぎ、コストを最適化するための最適化スタビライザーの使用]: http://msdn.microsoft.com/ja-jp/library/hh680951(PandP.50).aspx
  [通知と手動スケーリングの使用]: http://msdn.microsoft.com/ja-jp/library/hh680885(PandP.50).aspx
  [スケール グループの定義]: http://msdn.microsoft.com/ja-jp/library/hh680902(PandP.50).aspx
  [Windows PowerShell でブロックを操作するための WASABiCmdlets の使用]: http://msdn.microsoft.com/ja-jp/library/hh680938(PandP.50).aspx
  [Developer's Guide to the Enterprise Library 5.0 Integration Pack for Azure (Enterprise Library 5.0 Integration Pack for Azure 開発者ガイド)]: http://msdn.microsoft.com/ja-jp/library/hh680949(PandP.50).aspx
  [How Sage Reduces Azure Hosting Costs Using Autoscaling (Sage がオートスケーリングを使って Azure のホスティング コストを削減した方法)]: http://msdn.microsoft.com/ja-jp/library/jj838716(PandP.50).aspx
  [Reducing TechNet and MSDN hosting costs and environmental impact with autoscaling on Azure (Azure のオートスケーリングによる TechNet と MSDN のホスティング コストと環境への影響の軽減)]: http://msdn.microsoft.com/ja-jp/library/jj838718(PandP.50).aspx
