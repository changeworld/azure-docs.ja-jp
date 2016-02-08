<properties
   pageTitle="Visual Studio を使用した Azure プロジェクトの作成 | Microsoft Azure"
   description="Visual Studio を使用した Azure プロジェクトの作成"
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="01/22/2016"
   ms.author="tarcher" />

# Visual Studio を使用した Azure プロジェクトの作成

Azure Tools for Visual Studio には、Azure でクラウド サービスを作成するためのテンプレートが用意されています。このツールは、クラウド サービスを構成、デバッグし、Azure にデプロイするときにも役立ちます。

Azure クラウド サービスのソリューションには、以下の種類のプロジェクトが含まれています。

- **Azure プロジェクト**

    Azure プロジェクトは、ソリューション内のロール プロジェクトに関連付けられています。また、サービス定義ファイルとサービス構成ファイルが含まれます。サービス定義ファイルは、必要なロール、エンドポイント、仮想マシンのサイズを含む、アプリケーションのランタイム設定を定義します。サービス構成ファイルでは、実行されるロールのインスタンス数とロールに定義されている設定の値を構成します。これらの設定の詳細については、「[方法: Visual Studio を使用した Azure クラウド サービスのロールの構成](vs-azure-tools-configure-roles-for-cloud-service.md)」を参照してください。

- **Web ロール プロジェクト**

    worker ロールは、バック グラウンド処理を実行します。worker ロールは、ストレージ サービスや他のインターネット ベースのサービスと通信できます。worker ロールは、任意の数の HTTP、HTTPS、TCP エンドポイントを含むことができます。

    - **ASP.NET Web ロール** (Web フロントエンドを持つ ASP.NET アプリケーションの構築用)
    - **ASP.NET MVC5 Web ロール**
    - **ASP.NET MVC4 Web ロール**
    - **ASP.NET MVC3 Web ロール**
    - **WCF サービス Web ロール** (WCF サービスの構築用)
    - **Silverlight ビジネス アプリケーション Web ロール** (Visual Studio 2012 が必要)

- **キャッシュ Worker ロール**

    アプリケーションに専用のキャッシュを提供するロール。

- **Service Bus キューを使用する Worker ロール**

    Service Bus キューは、worker プロセスと通信するためのメッセージのキュー機能を提供します。詳細については、「[Service Bus キューの使用方法](http://go.microsoft.com/fwlink/?LinkId=260560)」を参照してください。

## Visual Studio で Azure クラウド サービス プロジェクトを作成するには

1. Microsoft Visual Studio を管理者として起動します。

1. メニュー バーで、**[ファイル]**、**[新規作成]**、**[プロジェクト]** の順に選択します。

1. **[プロジェクトの種類]** ウィンドウで、[Visual C#] または [Visual Basic] プロジェクト テンプレート ノードの **[クラウド]** を選択します。

1. **[テンプレート]** ウィンドウで、**[Azure クラウド サービス]** をクリックします。

1. プロジェクトの開発に使用する .NET Framework のバージョンを指定します。

1. プロジェクトの名前と場所、およびソリューションの名前を入力します。**[OK]** をクリックします。

1. **[新しい Azure プロジェクト]** ダイアログ ボックスで、追加するロールを選択し、右矢印ボタンをクリックしてそのロールをソリューションに追加します。ロールは、必要なだけ追加することができます。

1. プロジェクトに追加したロールの名前を変更するには、**[新しい Azure プロジェクト]** ダイアログ ボックスでそのロールの上にマウス ポインターを置き、ロールの右側にある **[名前の変更]** アイコンをクリックします。追加後も、ソリューション内でロールの名前を変更できます。

<!---HONumber=AcomDC_0128_2016-->