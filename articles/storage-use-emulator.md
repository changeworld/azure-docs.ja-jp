<properties 
	pageTitle="開発とテストのための Azure のストレージ エミュレーター使用" 
	description="開発やテストに Azure のストレージ エミュレーターを使用する方法について説明します。" 
	services="storage" 
	documentationCenter="" 
	authors="tamram" 
	manager="adinah" 
	editor=""/>
<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/20/2015" 
	ms.author="tamram"/>

# 開発とテストのための Azure のストレージ エミュレーター使用

## 概要
Microsoft Azure ストレージ エミュレーターでは、Azure の BLOB、キュー、テーブル サービスを開発用にエミュレートするローカル環境が利用できます。ストレージ エミュレーターを使用すると、ストレージ サービスに対するアプリケーションのローカル テストが、コストをかけずに実施できます。

> [AZURE.NOTE] ストレージ エミュレーターは、Microsoft Azure SDK に付属しています。また、スタンドアロンのパッケージとしてストレージ エミュレーターをインストールすることも可能です。
ストレージ エミュレーターを構成するには、ローカル コンピューターで管理者権限が与えられている必要があります。 
> ストレージ エミュレーターの特定のバージョンで作成されたデータには、別のバージョンを使用しているとアクセスできない場合があります。データを永続化して長期にわたって保持する必要がある場合、そのデータはストレージ エミュレーターではなく Azure ストレージ アカウントに格納することをお勧めします。
 
ストレージ エミュレーターと Azure のストレージ サービスには、いくつかの違いがあります。これらの違いの詳細については、「[ストレージ エミュレーターと Azure ストレージ サービスとの違い](https://msdn.microsoft.com/library/azure/gg433135.aspx)」をご覧ください。

ストレージ エミュレーターは、Microsoft(r) SQL Server(tm) インスタンスとローカル ファイル システムを使用して、Azure ストレージ サービスをエミュレートします。既定では、ストレージ エミュレーターは、Microsoft(r) SQL Server(tm) 2012 Express LocalDB のデータベース向けに構成されます。LocalDB のインストールを管理するには、SQL Server Management Studio Express をインストールします。ストレージ エミュレーターは、Windows 認証を使用して SQL Server または LocalDB に接続します。ストレージ エミュレーター コマンド ライン ツールのリファレンスを使用して、LocalDB ではなく、SQL Server のローカル インスタンスにアクセスするようにストレージ エミュレーターを構成することも可能です。

## 認証要求

ストレージ エミュレーターでは、単一の固定アカウントと既知の認証キーのみがサポートされます。このアカウントとキーのみ、ストレージ エミュレーターで資格情報として使用できます。次に例を示します。

    Account name: devstoreaccount1
    Account key: Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtrKBHBeksoGMGw==
    
> [AZURE.NOTE] ストレージ エミュレーターでサポートされている認証キーは、クライアント認証コードの機能をテストすることだけを目的としています。セキュリティ機能は果たしません。ストレージ エミュレーターで運用環境のストレージ アカウントとキーを使用することはできません。開発アカウントは運用データで使用できないことにもご注意ください。
 

## ストレージ エミュレーターの起動と初期化
Azure のストレージ エミュレーターを起動するには、[スタート] ボタンをクリックするか、Windows キーを押します。「Azure ストレージ エミュレーター」と入力し、アプリケーションの一覧から [Azure ストレージ エミュレーター] を選択します。 

または、Azure のコンピューティング エミュレーターが既に実行されている場合は、システム トレイ アイコンを右クリックして [[ストレージ エミュレーターの起動]](https://msdn.microsoft.com/library/azure/hh403990.aspx) をクリックすると、ストレージ エミュレーターを起動できます。

ストレージ エミュレーターが起動すると、コマンド ラインが表示されます。このコマンド ラインを使用して、ストレージ エミュレーターを起動したり停止したり、現在の状態の取得やエミュレーターの初期化を実行したりできます。詳細については、「[ストレージ エミュレーター コマンド ライン ツールのリファレンス](https://msdn.microsoft.com/library/azure/gg433005.aspx)」をご覧ください。

コマンド ラインを終了しても、ストレージ エミュレーターは引き続き実行されています。コマンド ラインを再度表示するには、ストレージ エミュレーターを起動する場合と同じように上の手順に従います。


ストレージ エミュレーターを初めて実行すると、ローカル ストレージ環境が初期化されます。ストレージ エミュレーター コマンド ライン ツールを使用して、別のデータベース インスタンスを参照することも、既存のデータベースを再初期化することも可能です。初期化プロセスでは、LocalDB にデータベースが作成され、各ローカル ストレージ サービス用として HTTP ポートが予約されます。この手順には管理者権限が必要です。詳細については、「[ストレージ エミュレーター コマンド ライン ツールのリファレンス](https://msdn.microsoft.com/library/azure/gg433005.aspx)」をご覧ください。

## ストレージ サービスの URI について

Azure ストレージ サービスでリソースをアドレス指定する方法は、リソースが Azure に存在するか ストレージ エミュレーター サービスに存在するかによって異なります。Azure のストレージ リソースをアドレス指定するために使用する URI スキーマと、ストレージ エミュレーターのストレージ リソースをアドレス指定するために使用する URI スキーマは異なります。この違いは、ローカル コンピューターがドメイン名の解決を実行しないために生じます。両方の URI スキーマには、常にアカウント名と要求中のリソースのアドレスが含まれています。

## クラウドにおける Azure のストレージ リソースのアドレス指定

Azure のストレージ リソースをアドレス指定するための URI スキームでは、アカウント名が URI ホスト名の一部になり、リソース パスが URI パスの一部になります。次の基本的なアドレス指定スキームを使用して、ストレージ リソースにアクセスします。

    <http|https>://<account-name>.<service-name>.core.windows.net/<resource-path>


`<account-name>` はストレージ アカウントの名前です。`<service-name>` はアクセスされるサービスの名前で、`<resource-path>` は要求されるリソースへのパスです。各ストレージ サービスの URI スキームを次に示します。

	Blob Service: <http|https>://<account-name>.blob.core.windows.net/<resource-path>
	Queue Service: <http|https>://<account-name>.queue.core.windows.net/<resource-path>
	Table Service: <http|https>://<account-name>.table.core.windows.net/<resource-path>

たとえば、次のアドレスを使用するとクラウドの BLOB にアクセスできます。
    
    http://myaccount.blob.core.windows.net/mycontainer/myblob.txt

> [AZURE.NOTE] カスタム ドメイン名をクラウドの BLOB ストレージ エンドポイントに関連付け、そのカスタム ドメイン名を使用して BLOB とコンテナーを指定することも可能です。詳細 
 
## ストレージ エミュレーターにおけるローカル Azure のストレージ リソースのアドレス指定

ストレージ エミュレーターでは、ローカル コンピューターがドメイン名の解決を実行しないため、アカウント名は URI パスの一部になります。ストレージ エミュレーターで実行されているリソースの URI スキームは、次の形式になります。

    http://<local-machine-address>:<port>/<account-name>/<resource-path>

ストレージ エミュレーターで実行されているリソースをアドレス指定するには、次の形式を使用します。

	Blob Service: http://127.0.0.1:10000/<account-name>/<resource-path>
	Queue Service: http://127.0.0.1:10001/<account-name>/<resource-path>
	Table Service: http://127.0.0.1:10002/<account-name>/<resource-path>

たとえば、ストレージ エミュレーターの BLOB にアクセスするには次のアドレスを使用します。

    http://127.0.0.1:10000/myaccount/mycontainer/myblob.txt

> [AZURE.NOTE] HTTPS は、ローカル ストレージ リソースをアドレス指定するためのプロトコルとしては使用できません。
 
## RA-GRS を使用した、アカウントのセカンダリ拠点のアドレス指定
Version 3.1 以降では、ストレージ エミュレーター アカウントで読み取りアクセスの地理冗長レプリケーション (RA-GRS) がサポートされます。クラウド内のストレージ リソースとローカル エミュレーター内のストレージ リソースの場合、2 次拠点にアクセスするにはアカウント名に -secondary を付加します。たとえば、ストレージ エミュレーターで読み取り専用の 2 次拠点を使用して BLOB にアクセスするには、次のアドレスを使用します。

    http://127.0.0.1:10000/myaccount-secondary/mycontainer/myblob.txt 

> [AZURE.NOTE] ストレージ エミュレーターを使用した、プログラムによるセカンダリへのアクセスには、.NET 用ストレージ クライアント ライブラリの Version 3.2 以降を使用してください。詳細については、「ストレージ クライアント ライブラリ リファレンス」をご覧ください。
 
## コマンド ライン ツールを使用してストレージ エミュレーターを初期化する
既定とは別のデータベース インスタンスを参照するようにストレージ エミュレーターを初期化するには、ストレージ エミュレーター コマンド ライン ツールを使用します。既定の LocalDB データベース インスタンスを使用する場合は、別個の初期化手順を実行する必要はありません。

このツールは、既定で C:\Program Files(x86)\Microsoft SDKs\Azure\Storage Emulator\ ディレクトリにインストールされています。初期化は、エミュレーターを初めて起動する際に自動的に実行されます。

> [AZURE.NOTE] 次に説明する `init` コマンドを実行するには管理者権限が必要です。

1. **[スタート]** ボタンをクリックするか、**[Windows]** キーを押します。`Azure Storage Emulator`と入力し、表示されたらこの文字列を選択します。コマンド プロンプト ウィンドウがポップアップ表示されます。


2. コマンド プロンプト ウィンドウで、次のコマンドを入力します。ここで `<SQLServerInstance>` は、SQL Server インスタンスの名前です。LocalDb を使用するには、SQL Server インスタンスとして `(localdb)\v11.0` を指定します。

    	WAStorageEmulator init /sqlInstance <SQLServerInstance> 
    
次のコマンドを使うこともできます。このコマンドを指定すると、エミュレーターは既定の SQL Server インスタンスを使用します。

    WAStorageEmulator init /server .\\ 

または、データベースを再初期化する次のコマンドを使うこともできます。

    WAStorageEmulator init /forceCreate 

## ストレージ エミュレーター コマンド ライン ツールのリファレンス

バージョン 3.0 からは、ストレージ エミュレーターの起動時にコマンド プロンプトがポップアップ表示されます。このコマンド プロンプトを使用して、エミュレーターを起動や停止し、状態のクエリやその他の操作を実行します。

> [AZURE.NOTE] コンピューティング エミュレーターがインストール済みの場合は、ストレージ エミュレーターの起動時にシステム トレイ アイコンが表示されます。このアイコンを右クリックすると、直感的な方法でストレージ エミュレーターを起動や停止できます。

### コマンド ライン構文

	WAStorageEmulator [/start] [/stop] [/status] [/clear] [/init] [/help]

### オプション
オプションの一覧を表示するには、コマンド プロンプトで「`/help`」と入力します。

## 次のステップ
- [ストレージ エミュレーター コマンド ライン ツールのリファレンス](https://msdn.microsoft.com/library/azure/gg433005.aspx)
- [ストレージ エミュレーターと Azure ストレージ サービスとの違い](https://msdn.microsoft.com/library/azure/gg433135.aspx)
- [ストレージ エミュレーター リリース ノート](https://msdn.microsoft.com/library/azure/dn683879.aspx)

<!--HONumber=47-->
