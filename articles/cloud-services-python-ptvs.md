<properties linkid="develop-python-cloud-services-with-ptvs" urlDisplayName="Python Web and Worker Roles with Python Tools 2.1 for Visual Studio" pageTitle="Python Tools 2.1 for Visual Studio による Python Web ロールと Python worker ロール" metaKeywords="Azure python, web role, worker role, PTVS, cloud service" description="Overview of using Python Tools for Visual Studio to create Azure cloud services including web roles and worker roles." metaCanonical="" services="" documentationCenter="Python" title="Python Web and Worker Roles with Python Tools 2.1 for Visual Studio" authors="huvalo" solutions="" manager="wpickett" editor="" />

<tags ms.service="cloud-services" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="python" ms.topic="article" ms.date="10/10/2014" ms.author="huvalo" />




# Python Tools 2.1 for Visual Studio による Python Web ロールと Python worker ロール

このガイドでは、[Python Tools for Visual Studio][] で Python Web ロールと Python worker ロールを扱う方法について概説します。

+ [前提条件](#prerequisites)
+ [Python Web ロールと Python worker ロールについて](#what-are-python-web-and-worker-roles)
+ [プロジェクトの作成](#project-creation)
+ [ローカルで実行する](#run-locally)
+ [Azure に発行する](#publish-to-azure)
+ [次のステップ](#next-steps)

##<a name="prerequisites"></a>前提条件

 - Visual Studio 2012 または 2013
 - [Python Tools 2.1 for Visual Studio][]
 - [Azure SDK Tools for VS 2013][] または [Azure SDK Tools for VS 2012][]
 - [Python 2.7 (32 ビット)][] または [Python 3.4 (32 ビット)][]

[WACOM.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

##<a name="what-are-python-web-and-worker-roles"></a>Python Web ロールと Python worker ロールについて

Azure にはアプリケーションを実行するためのコンピューティング モデルが 3 種類あります。[Azure Web Sites][execution model-web sites]、[Azure Virtual Machines][execution model-vms]、[Azure Cloud Services][execution model-cloud services] です。これら 3 つのモデルはすべて、Python をサポートしています。クラウド サービスには、Web ロールと worker ロールが含まれ、*サービスとしてのプラットフォーム (PaaS)* を提供します。クラウド サービス内で、Web ロールは、フロントエンド Web アプリケーションのホスト専用のインターネット インフォメーション サービス (IIS) Web サーバーを提供します。ワーカー ロールは、ユーザーの操作や入力とは関係なく、長期間または恒久的な非同期タスクを実行できます。

詳細については、「[クラウド サービスとは]」を参照してください。

<div class="dev-callout"><strong>単純な Web サイトを構築する場合</strong>
<p>実現するシナリオが単純な Web サイトのフロントエンドにのみ関係している場合は、軽量の Azure の Web サイトを使用することを検討してください。Web サイトの規模が増大し、要件が変化したときには、容易にクラウド サービスにアップグレードできます。Azure の Web サイトの開発に関する記事については、 <a href="/ja-jp/develop/python/">Python デベロッパー センター</a> を参照してください。</p>
</div>
<br />


##<a name="project-creation"></a>プロジェクトの作成

Visual Studio で、**[新しいプロジェクト]** ダイアログの **[Python]** から **[Azure クラウド サービス]** を選択します。 

![New Project Dialog](./media/cloud-services-python-ptvs/new-project-cloud-service.png)

Azure クラウド サービス ウィザードでは、新規作成の対象として Web ロールまたは worker ロールを選択できます。

![Azure Cloud Service Dialog](./media/cloud-services-python-ptvs/new-service-wizard.png)

worker ロール テンプレートには、Azure ストレージ アカウントまたは Service Bus に接続するためのスケルトン コードが含まれています。

![Cloud Service Solution](./media/cloud-services-python-ptvs/worker.png)

Web ロールまたは worker ロールは、既存のクラウド サービスにいつでも追加することができます。既存のプロジェクトをソリューションに追加するか、または新たに作成するかを選択できます。 

![Add Role Command](./media/cloud-services-python-ptvs/add-new-or-existing-role.png)

クラウド サービスには、異なる言語で実装されたロールを含めることができます。たとえば、Django を使用して実装された Python Web ロールを、Python worker ロールや C# worker ロールと共存させることができます。ロール間のやり取りは、Service Bus キューまたはストレージ キューを使用することで簡単に行うことができます。

##<a name="run-locally"></a>ローカルで実行する

クラウド サービス プロジェクトをスタートアップ プロジェクトとして設定し、F5 キーを押した場合、ローカルの Azure エミュレーター内でクラウド サービスが実行されます。

PTVS (Python Tools for Visual Studio) はエミュレーターでの起動をサポートしていますが、デバッグ操作 (ブレークポイントなど) は機能しません。

Web ロールまたは worker ロールをデバッグするには、対象となるロール プロジェクトをスタートアップ プロジェクトに設定したうえで、デバッグするようにしてください。複数のスタートアップ プロジェクトを設定することもできます。ソリューションを右クリックし、**[スタートアップ プロジェクトの設定]** を選択します。

![Solution Startup Project Properties](./media/cloud-services-python-ptvs/startup.png)

##<a name="publish-to-azure"></a>Azure に発行する

クラウド サービス プロジェクトを発行するには、対象のクラウド サービス プロジェクトをソリューション内で右クリックし、**[発行]** を選択します。

![Microsoft Azure Publish Sign In](./media/cloud-services-python-ptvs/publish-sign-in.png)

[設定] ページで、発行するクラウド サービスを選択します。

![Microsoft Azure Publish Settings](./media/cloud-services-python-ptvs/publish-settings.png)

そのクラウド サービスがまだない場合は、新しいクラウド サービスを作成できます。

![Create Cloud Service Dialog](./media/cloud-services-python-ptvs/publish-create-cloud-service.png)

エラーをデバッグする際の利便性を高めるために、コンピューターへのリモート デスクトップ接続を有効にすることもできます。

![Remote Desktop Configuration Dialog](./media/cloud-services-python-ptvs/publish-remote-desktop-configuration.png)

設定が済んだら、**[発行]** をクリックします。

出力ウィンドウにいくつかの進行状況が表示された後、[Microsoft Azure のアクティビティ ログ] ウィンドウが表示されます。

![Microsoft Azure Activity Log Window](./media/cloud-services-python-ptvs/publish-activity-log.png)

数分経過するとデプロイメントが完了し、Web ロールまたは worker ロールが Azure 上で稼働状態となります。

##<a name="next-steps"></a>次のステップ

Python Tools for Visual Studio で Web ロールまたは worker ロールを扱う方法について、さらに詳しい情報については、次の PTVS 関連ドキュメントを参照してください。

- [クラウド サービス プロジェクト][]

Web ロールまたは worker ロールから、Azure Storage、Service Bus などの Azure サービスを使用する方法の詳細については、次のガイドを参照してください。
 
- [BLOB サービス][]
- [Table サービス][]
- [Queue サービス][]
- [Service Bus キュー][]
- [Service Bus トピック][]


<!--Link references-->

[クラウド サービスとは]: /ja-jp/manage/services/cloud-services/what-is-a-cloud-service/
[execution model-web sites]: /ja-jp/documentation/articles/fundamentals-application-models/#WebSites
[execution model-vms]: /ja-jp/documentation/articles/fundamentals-application-models/#VMachine
[execution model-cloud services]: /ja-jp/documentation/articles/fundamentals-application-models/#CloudServices
[Python デベロッパー センター]: /ja-jp/develop/python/

[BLOB サービス]: /ja-jp/documentation/articles/storage-python-how-to-use-blob-storage/
[Queue サービス]: /ja-jp/documentation/articles/storage-python-how-to-use-queue-storage/
[Table サービス]: /ja-jp/documentation/articles/storage-python-how-to-use-table-storage/
[Service Bus キュー]: /ja-jp/documentation/articles/service-bus-python-how-to-use-queues/
[Service Bus トピック]: /ja-jp/documentation/articles/service-bus-python-how-to-use-topics-subscriptions/


<!--External Link references-->

[Python Tools for Visual Studio]: http://pytools.codeplex.com
[Python Tools for Visual Studio Documentation (Python Tools for Visual Studio のドキュメント)]: http://pytools.codeplex.com/documentation 
[クラウド サービス プロジェクト]: http://pytools.codeplex.com/wikipage?title=Features%20Cloud%20Project

[Python Tools 2.1 for Visual Studio]: http://go.microsoft.com/fwlink/?LinkId=517189
[Azure SDK Tools for VS 2013]: http://go.microsoft.com/fwlink/?LinkId=323510
[Azure SDK Tools for VS 2012]: http://go.microsoft.com/fwlink/?LinkId=323511
[Python 2.7 (32 ビット)]: http://go.microsoft.com/fwlink/?LinkId=517190 
[Python 3.4 (32 ビット)]: http://go.microsoft.com/fwlink/?LinkId=517191

<!--HONumber=35_1-->
