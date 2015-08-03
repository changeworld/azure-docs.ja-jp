<properties
   pageTitle="オペレーション インサイトにサポートされる構成"
   description="オペレーション インサイトに必要な構成に関して説明します"
   services="operational-insights"
   documentationCenter=""
   authors="bandersmsft"
   manager="jwhit"
   editor="tysonn" />
<tags
   ms.service="operational-insights"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/02/2015"
   ms.author="banders" />

# オペレーション インサイトにサポートされる構成

[AZURE.INCLUDE [operational-insights-note-moms](../../includes/operational-insights-note-moms.md)]

オペレーション インサイトの使用に必要なもの。 オペレーション インサイトを準備するために次の情報を確認します。


## System Center 2012 の Operations Manager の構成

オペレーション インサイトは、System Center 2012 R2 や System Center 2012 SP1 R2 の Operations Manager に関連付けられているサービスとして使用することができます。これにより、Operations Manager のオペレーション コンソールを使用してオペレーション インサイトのアラートと構成情報を表示することができます。オペレーション インサイトを Operations Manager に関連付けられているサービスとして使用する場合、エージェントは、管理サーバーと直接通信し、次にオペレーション インサイト サービスを通信します。

関連付けられているサービスとしてオペレーション インサイトを使用するには、次の前提条件が必要です。


- System Center 2012 SP1 Operations Manager と オペレーション インサイトの統合には、[Operational Insights Connector for Operations Manager](https://www.microsoft.com/ja-jp/download/details.aspx?id=38199) に含まれている更新された管理パックが必要です。[Operational Insights Connector for Operations Manager](https://www.microsoft.com/ja-jp/download/details.aspx?id=38199) から管理パックをダウンロードして、インストールできます。

- System Center 2012 sp 1: Operations Manager 更新プログラム ロールアップ 6、ただし、更新プログラム ロールアップ 7 をお勧めします。この更新プログラムは、オペレーション インサイトが関連付けられているサービスのシナリオとして、管理サーバー、エージェント、オペレーション コンソールに適用されている必要があります。

- System Center 2012 sp 2: Operations Manager 更新プログラム ロールアップ 2、ただし、更新プログラム ロールアップ 3 をお勧めします。この更新プログラムは、オペレーション インサイトが関連付けられているサービスのシナリオとして、管理サーバー、エージェント、オペレーション コンソールに適用されている必要があります。

- 容量管理データを表示するには、Operations Manager と Virtual Machine Manager (VMM) との接続を有効にする必要があります。システムの接続の詳細については、「[VMM を Operations Manager と接続する方法](https://technet.microsoft.com/ja-jp/library/hh882396.aspx)」をご覧ください。

インストールと構成の手順の詳細については、「[オペレーション インサイトのアラートの表示](http://go.microsoft.com/fwlink/?LinkID=293793)」をご覧ください。

SharePoint Server 2010、Lync Server 2013、Lync Server 2010、System Center 2012 SP1 – Virtual Machine Manager に関するオペレーション インサイトのアラートを表示するには、これらのワークロードに使用する実行アカウントを構成する必要があります。実行アカウントの設定の詳細については、「[Operations Manager とオペレーション インサイトの連携に関する考慮事項](operational-insights-operations-manager.md)」を参照してください。


### Operations Manager のオペレーティング システム

Operations Manager エージェントは、さまざまなコンピューターでサポートされています。エージェントのサポートの詳細については、「[システム要件: System Center 2012 R2 Operations Manager](https://technet.microsoft.com/library/dn249696.aspx)」をご覧ください。

### Operations Manager に必要なソフトウェア

容量管理データを表示するには、Operations Manager と VMM との接続を有効にする必要があります。システムの接続の詳細については、「[VMM を Operations Manager と接続する方法](https://technet.microsoft.com/ja-jp/library/hh882396.aspx)」をご覧ください。

## オペレーション インサイトに直接接続されているエージェント

サービスに直接接続するために使用するエージェントは、Microsoft Monitoring Agent です。システム要件の一覧は、[Microsoft ダウンロード センター](https://www.microsoft.com/ja-jp/download/details.aspx?id=40316&e6b34bbe-475b-1abd-2c51-b5034bcdd6d2=True) ページに表示されています。

## ブラウザー

オペレーション インサイトは、次のブラウザーのいずれでも使用できます。

- Windows Internet Explorer 11、Internet Explorer 10、Internet Explorer 9、Internet Explorer 8、Internet Explorer 7

- Mozilla Firefox 3.5 以降

使用するブラウザーに関係なく、Microsoft Silverlight 4 をインストール必要があります。

## 分析できるテクノロジ

オペレーション インサイトの構成評価は、以下のワークロードを分析します。

- Windows Server 2012 と Microsoft HYPER-V Server 2012

- 以下を含む Windows Server 2008 と Windows Server 2008 R2
    - Active Directory
	- HYPER-V ホスト
	- 汎用オペレーティング システム

- SQL Server 2012、SQL Server 2008 R2、SQL Server 2008
    - SQL Server データベース エンジン

- Microsoft SharePoint 2010

- Microsoft Exchange Server 2010 と Microsoft Exchange Server 2013

- Microsoft Lync Server 2013 と Lync Server 2010

- System Center 2012 SP1 – Virtual Machine Manager

SQL Server では、分析用に次の 32 ビットと 64 ビット エディションがサポートされています。

- SQL Server 2008 と 2008 R2 Enterprise

- SQL Server 2008 と 2008 R2 Standard

- SQL Server 2008 と 2008 R2 Workgroup

- SQL Server 2008 と 2008 R2 Web

- SQL Server 2008 と 2008 R2 Express

また、WOW64 実装で実行する場合は、SQL Server の32 ビット エディションがサポートされています。

<!---HONumber=July15_HO4-->