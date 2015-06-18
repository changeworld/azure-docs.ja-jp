<properties 
	pageTitle="Azure Storage を使用した SQL Server のバックアップと復元の方法 | Azure" 
	description="SQL Server および SQL Database を Azure Storage にバックアップします。SQL データベースを Azure ストレージにバックアップすることのメリットについて、また SQL Server および Azure Storage のどのコンポーネントが必要かについて説明します。" 
	services="sql-database, virtual-machines" 
	documentationCenter="" 
	authors="jeffgoll" 
	manager="jeffreyg" 
	editor="tysonn"/>

<tags 
	ms.service="sql-database" 
	ms.workload="data-management" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/06/2015" 
	ms.author="jeffreyg"/>



# Azure ストレージを使用した SQL Server のバックアップと復元の方法

## 概要

SQL Server のバックアップを Azure BLOB ストレージ サービスに書き込むことができる機能は SQL Server 2012 SP1 CU2 でリリースされました。この機能を使用すると、Azure BLOB サービスとの間で、内部設置型の SQL Server データベースまたは Azure の仮想マシン上の SQL Server データベースをバックアップおよび復元できます。クラウドへのバックアップには、高い可用性、無制限の社外ストレージのジオ (主要地域) レプリケーション、クラウドとの間でのデータ移行の容易さという利点があります。   このリリースでは、T-SQL または SMO を使用して BACKUP または RESTORE ステートメントを発行できます。

## SQL Server のバックアップに Azure BLOB サービスを使用する利点

バックアップには一般的に、ストレージの管理、ストレージ障害のリスク、社外ストレージへのアクセス、デバイスの構成などの課題があります。Azure の仮想マシンで実行されている SQL Server については、そのほかにも、VHD の構成とバックアップ、接続されたドライブの構成などの課題があります。次に示しているのは、SQL Server のバックアップに Azure BLOB ストレージ サービスのストレージを使用する主な利点です。

* 柔軟性と信頼性が高い無制限の社外ストレージ:Azure BLOB サービスにバックアップを保存することで、社外ストレージへのアクセスが柔軟かつ簡単に行え、便利になります。SQL Server のバックアップ用に社外ストレージを作成するのは、既存のスクリプトやジョブを変更するのと同じくらい簡単です。社外ストレージは通常、運用データベースから十分に離れた場所に設置して、一度の災害によって社外ストレージと運用データベースの両方が影響を受けないようにする必要があります。BLOB ストレージのジオ (主要地域) レプリケーションを利用することで、リージョン全体に被害が及ぶような災害に対する防御措置を強化できます。また、バックアップは場所や時間を問わずに使用でき、簡単にアクセスして復元することが可能です。
* バックアップ アーカイブ:Azure BLOB ストレージ サービスは、バックアップ アーカイブによく利用されているテープに代わる、優れた手法を提供しています。テープ ストレージでは、場合によって社外設備への物理的な搬送やメディア保護の対策が必要です。Azure BLOB ストレージへのバックアップの保存では、可用性と持続性に優れた高速アーカイブが可能です。
* ハードウェア管理のオーバーヘッドが不要:Azure サービスではハードウェア管理のオーバーヘッドはありません。Azure サービスのハードウェア管理では、ハードウェア障害に対する冗長性実現と保護のためにジオ (主要地域) レプリケーションが行われます。
* 現在、Azure の仮想マシンで実行されている SQL Server のインスタンスについては、接続されたディスクを作成することで、Azure BLOB ストレージ サービスにバックアップできます。ただし、Azure の仮想マシンに接続できるディスクの数には制限があります。この制限はインスタンス サイズが XL の場合は 16 ディスクです。インスタンス サイズが小さくなるほど、このディスク数は少なくなります。Azure BLOB ストレージに直接バックアップできるようにすることで、この 16 ディスクという制限を回避できます。
* また、Azure BLOB ストレージ サービスに保存されているバックアップ ファイルは、内部設置型の SQL Server でも、Azure の仮想マシンで実行されている別の SQL Server でも直接使用できます。データベースの接続/切断や VHD のダウンロードと接続は不要です。
* コスト面の利点:使用するサービスに対してのみ課金されます。社外ストレージやバックアップ アーカイブ用にこのサービスを選択することで、高い費用対効果を得られます。詳細については、[Azure の料金計算ツール](http://go.microsoft.com/fwlink/?LinkId=277060 "Pricing Calculator")および [Azure の料金の概要](http://go.microsoft.com/fwlink/?LinkId=277059 "Pricing article")を参照してください。

詳細については、「[Azure BLOB ストレージ サービスを使用した SQL Server のバックアップと復元](http://go.microsoft.com/fwlink/?LinkId=271617)」を参照してください。

次の 2 つのセクションでは、Azure BLOB ストレージ サービスのコンポーネントについて、さらに Azure BLOB ストレージ サービスとの間のバックアップまたは復元に使用する SQL Server のコンポーネントについて概要を示します。重要なのは、それらのコンポーネントを理解するだけでなく、コンポーネント間のやり取りも把握してから、Azure BLOB ストレージ サービスとの間でバックアップまたは復元を行うことです。 

Azure アカウントの作成がこのプロセスの最初の手順です。SQL Server では、Azure ストレージ アカウントの名前とアクセス キー値を使用して、ストレージ サービスとの間で認証と BLOB の読み書きを行います。SQL Server 資格情報にこの認証情報が保存され、その資格情報がバックアップまたは復元処理時に使用されます。 

ストレージ アカウントを作成して単純な復元を実行する詳細なチュートリアルについては、「[Getting Started with Azure Storage Service for SQL Server Backup and Restore (Azure BLOB ストレージ サービスへの SQL Server のバックアップと復元の概要)](http://go.microsoft.com/fwlink/?LinkId=271615)」を参照してください。 

## Azure BLOB ストレージ サービスのコンポーネント 

* ストレージ アカウント:ストレージ アカウントはすべてのストレージ サービスの出発点となります。Azure BLOB ストレージ サービスにアクセスするには、まず Azure のストレージ アカウントを作成します。ストレージ アカウントの名前とアクセス キー プロパティが Azure BLOB ストレージ サービスとそのコンポーネントに対する認証に必要です。 
Azure BLOB ストレージ サービスの詳細については、「[How to use the Azure Blob Storage Service (Azure BLOB ストレージ サービスを使用する方法)](http://azure.microsoft.com/develop/net/how-to-guides/blob-storage/)」を参照してください。

* コンテナー:コンテナーは一連の BLOB のグループ化に使用され、格納できる BLOB の数に制限はありません。SQL Server のバックアップを Azure BLOB サービスに書き込むには、少なくとも root コンテナーが作成されている必要があります。 

* BLOB:任意の種類およびサイズのファイルです。Azure BLOB ストレージ サービスに格納できる BLOB には、ブロック BLOB とページ BLOB の 2 種類があります。SQL Server のバックアップでは、BLOB の種類としてページ BLOB を使用します。BLOB は、次の URL 形式を使用してアドレスを指定し、アクセスできます。 `https://<storage account>.blob.core.windows.net/<container>/<blob>`
ページ BLOB の詳細については、「[ブロック BLOB およびページ BLOB について」を参照してください。](http://msdn.microsoft.com/library/azure/ee691964.aspx)

## SQL Server のコンポーネント

* URL:URL は一意のバックアップ ファイルへの Uniform Resource Identifier (URI) を参照します。URL を使用して SQL Server バックアップ ファイルの場所と名前を指定します。この実装では、Azure のストレージ アカウントのページ BLOB を参照する URL のみ有効です。URL は、コンテナーだけでなく実際の BLOB を参照する必要があります。BLOB が存在しない場合は作成されます。既存の BLOB を指定した場合、> WITH FORMAT オプションを指定していないと、BACKUP は失敗します。 
BACKUP コマンドで URL を指定する例を次に示します。 
**`http[s]://ACCOUNTNAME.Blob.core.windows.net/<CONTAINER>/<FILENAME.bak>`

<b>注:</b> HTTPS は必須ではありませんが、使用することをお勧めします。
<b>重要</b>:
バックアップ ファイルをコピーして Azure BLOB ストレージ サービスにアップロードする場合、そのファイルを復元処理に使用する予定があれば、ストレージ オプションの BLOB の種類としてページ BLOB を使用する必要があります。BLOB の種類としてブロック BLOB を使用すると、RESTORE がエラーで失敗します。 

* 資格情報:Azure BLOB ストレージ サービスに対する接続と認証に必要な情報は資格情報として保存されます。SQL Server から Azure BLOB に対してバックアップを書き込んだり復元したりするには、SQL Server 資格情報を作成する必要があります。その資格情報にストレージ アカウントの名前とアクセス キーを保存します。作成した資格情報は、BACKUP/RESTORE ステートメントの発行時に WITH CREDENTIAL オプションで指定する必要があります。 
SQL Server 資格情報を作成する手順の詳細については、「[Azure BLOB ストレージ サービスへの SQL Server のバックアップと復元の概要](http://go.microsoft.com/fwlink/?LinkId=271615)」を参照してください。

## Azure BLOB を使用した SQL Server データベースのバックアップと復元 - 概念とタスク:

**概念、考慮事項、コード サンプル:**

[SQL Server Backup and Restore with Azure Blob Storage Service (Azure BLOB ストレージ サービスを使用した SQL Server のバックアップと復元)](http://go.microsoft.com/fwlink/?LinkId=271617)

**概要チュートリアル:**

[Getting Started with Azure Storage Service for SQL Server Backup and Restore (Azure BLOB ストレージ サービスへの SQL Server のバックアップと復元の概要)](http://go.microsoft.com/fwlink/?LinkID=271615 "Tutorial")

**ベスト プラクティス、トラブルシューティング: **
	
[バックアップと復元に関するベスト プラクティス (Azure BLOB ストレージ サービス)](http://go.microsoft.com/fwlink/?LinkId=272394)




	





<!--HONumber=49--> 