<properties 
	pageTitle="Azure Storage への接続文字列の構成 |Microsoft Azure"
	description="Azure ストレージ アカウントへの接続文字列を構成します。接続文字列には、アプリケーションがストレージ アカウントに対し、そのアクセスが信頼できるものであることを実行時に証明するうえで必要な情報が格納されます。"
	services="storage"
	documentationCenter=""
	authors="tamram"
	manager="carmonm"
	editor="tysonn"/>

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/12/2016"
	ms.author="tamram"/>

# Azure Storage の接続文字列を構成する

## 概要

アプリケーションから実行時に Azure ストレージ アカウント内のデータにアクセスするために必要な認証情報は、接続文字列に指定します。接続文字列は、次の目的に構成できます。

- Azure ストレージ エミュレーターに接続する。
- Azure のストレージ アカウントにアクセスする。
- Shared Access Signature (SAS) を介して Azure の特定のリソースにアクセスする。

[AZURE.INCLUDE [storage-account-key-note-include](../../includes/storage-account-key-note-include.md)]

## 接続文字列の保存

アプリケーションは、その要求が信頼できるものであることを Azure Storage に対して証明するために、実行時に接続文字列にアクセスする必要があります。接続文字列の保存にはいくつかのオプションがあります。

- デスクトップまたはデバイスで実行するアプリケーションの場合は、`app.config ` ファイルまたは `web.config` に接続文字列を保存できます。この接続文字列は、**AppSettings** セクションに追加します。
- アプリケーションが Azure クラウド サービスで実行している場合、接続文字列は [Azure サービスの構成スキーマ (.cscfg) ファイル](https://msdn.microsoft.com/library/ee758710.aspx)に保存できます。接続文字列をサービス構成ファイルの **ConfigurationSettings** セクションに追加します。
- 直接コード内に接続文字列を記述することもできますが、通常は、構成ファイルに保存するようお勧めします。

構成ファイル内に接続文字列を保存すると、接続文字列を簡単に更新してストレージ エミュレーターとクラウドの Azure ストレージ アカウントを切り替えることができます。必要な作業は、対象となる環境に合わせて接続文字列を編集するだけです。

[Microsoft Azure Configuration Manager](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager/) クラスを使用すると、アプリケーションの実行場所に関係なく、実行時に接続文字列にアクセスできます。

## ストレージ エミュレーターへの接続文字列を作成する

[AZURE.INCLUDE [storage-emulator-connection-string-include](../../includes/storage-emulator-connection-string-include.md)]

ストレージ エミュレーターについて詳しくは、「[開発とテストのための Azure のストレージ エミュレーター使用](storage-use-emulator.md)」を参照してください。

## Azure ストレージ アカウントへの接続文字列を作成する

Azure ストレージ アカウントへの接続文字列を作成するには、次の接続文字列の形式を使用します。HTTPS (推奨) と HTTP のどちらを使用してストレージ アカウントに接続するかを指定し、`myAccountName` をストレージ アカウントの名前に、`myAccountKey` をアカウント アクセス キーに置き換えます。

    DefaultEndpointsProtocol=[http|https];AccountName=myAccountName;AccountKey=myAccountKey

たとえば、接続文字列は次のサンプルの接続文字列のようになります。

	DefaultEndpointsProtocol=https;AccountName=storagesample;AccountKey=<account-key>

> [AZURE.NOTE] Azure Storage では、HTTP と HTTPS の両方の接続文字列をサポートします。ただし、HTTPS の使用を強くお勧めします。

## Shared Access Signature を使用して接続文字列を作成する

Shared Access Signature (SAS) の URL を所有している場合は、その SAS を接続文字列の中で使用できます。SAS では、要求を認証するために必要な情報が URI に追加されます。リソースにアクセスするために必要な資格情報、サービス エンドポイント、プロトコルは、SAS の URI によって得られます。

共有アクセス署名を含む接続文字列を作成するには、文字列を次の形式で指定します。

    BlobEndpoint=myBlobEndpoint;
	QueueEndpoint=myQueueEndpoint;
	TableEndpoint=myTableEndpoint;
	FileEndpoint=myFileEndpoint;
	SharedAccessSignature=sasToken

サービス エンドポイントはいずれも省略可能ですが、少なくとも 1 つは、接続文字列に存在する必要があります。

ベスト プラクティスとして、SAS は HTTPS と組み合わせて使用することをお勧めします。共有アクセス署名の詳細については、「[Shared Access Signature、第 1 部: SAS モデルについて](storage-dotnet-shared-access-signature-part-1.md)」を参照してください。

>[AZURE.NOTE] 構成ファイル内で接続文字列に SAS を指定する場合、URL に含まれる特殊文字のエンコードが必要になる場合があります。

### サービス SAS の例

Blob Storage のサービス SAS を含んだ接続文字列の例を次に示します。

	BlobEndpoint=https://storagesample.blob.core.windows.net;
	SharedAccessSignature=sv=2015-04-05&sr=b&si=tutorial-policy-635959936145100803&sig=9aCzs76n0E7y5BpEi2GvsSv433BZa22leDOZXX%2BXXIU%3D

そしてこちらが、同じ接続文字列に URL エンコードを適用した例です。

	BlobEndpoint=https://storagesample.blob.core.windows.net;
	SharedAccessSignature=sv=2015-04-05&amp;sr=b&amp;si=tutorial-policy-635959936145100803&amp;sig=9aCzs76n0E7y5BpEi2GvsSv433BZa22leDOZXX%2BXXIU%3D

### アカウント SAS の例

Blob Storage と File Storage に使用されるアカウントの SAS を含んだ接続文字列の例を次に示します。両方のサービスのエンドポイントが指定されていることに注意してください。

	BlobEndpoint=https://storagesample.blob.core.windows.net;
	FileEndpoint=https://storagesample.file.core.windows.net;
	SharedAccessSignature=sv=2015-07-08&sig=iCvQmdZngZNW%2F4vw43j6%2BVz6fndHF5LI639QJba4r8o%3D&spr=https&st=2016-04-12T03%3A24%3A31Z&se=2016-04-13T03%3A29%3A31Z&srt=s&ss=bf&sp=rwl

そしてこちらが、同じ接続文字列に URL エンコードを適用した例です。

	BlobEndpoint=https://storagesample.blob.core.windows.net;
	FileEndpoint=https://storagesample.file.core.windows.net;
	SharedAccessSignature=sv=2015-07-08&amp;sig=iCvQmdZngZNW%2F4vw43j6%2BVz6fndHF5LI639QJba4r8o%3D&amp;spr=https&amp;st=2016-04-12T03%3A24%3A31Z&amp;se=2016-04-13T03%3A29%3A31Z&amp;srt=s&amp;ss=bf&amp;sp=rwl

## 明示的なストレージ エンドポイントへの接続文字列を作成する

接続文字列の中で明示的に (既定のエンドポイントを使用せずに) サービス エンドポイントを指定することができます。明示的なエンドポイントを指定する接続文字列を作成するには、次に示す形式を使用して、プロトコル仕様 (HTTPS (推奨) または HTTP) を含む完全なサービス エンドポイントをサービスごとに指定します。

	DefaultEndpointsProtocol=[http|https];
	BlobEndpoint=myBlobEndpoint;
	QueueEndpoint=myQueueEndpoint;
	TableEndpoint=myTableEndpoint;
	FileEndpoint=myFileEndpoint;
	AccountName=myAccountName;
	AccountKey=myAccountKey

たとえば Blob Storage のエンドポイントをカスタム ドメインにマップしてある場合、明示的なエンドポイントの指定が必要になります。その場合、Blob Storage のカスタム エンドポイントを接続文字列の中で指定したうえで、他のサービス用の既定のエンドポイントをアプリケーションで使用する場合は、それらを必要に応じて指定できます。

BLOB サービスのエンドポイントを明示的に指定する有効な接続文字列の例を次に示します。

	# Blob endpoint only
	DefaultEndpointsProtocol=https;
	BlobEndpoint=www.mydomain.com;
	AccountName=storagesample;
	AccountKey=account-key

	# All service endpoints
	DefaultEndpointsProtocol=https;
	BlobEndpoint=www.mydomain.com;
	FileEndpoint=myaccount.file.core.windows.net;
	QueueEndpoint=myaccount.queue.core.windows.net;
	TableEndpoint=myaccount;
	AccountName=storagesample;
	AccountKey=account-key

接続文字列に示されているエンドポイント値を使用することで、BLOB サービスへの要求 URI が作成され、コードに返される URI の形式も決定されます。

サービス エンドポイントを接続文字列に指定しなかった場合、コードからその接続文字列を使用して、サービスのデータにアクセスすることはできません。

### エンドポイント サフィックスを含む接続文字列を作成する

Azure China、Azure Governance など、別のエンドポイント サフィックスを持つリージョンまたはインスタンスのストレージ サービスの接続文字列を作成するには、次の接続文字列の形式を使用します。HTTP または HTTPS のどちらでストレージ アカウントに接続するかを示し、`myAccountName` をストレージ アカウントの名前、`myAccountKey` をアカウント アクセス キー、`mySuffix` を URI サフィックスに置き換えます。


	DefaultEndpointsProtocol=[http|https];
	AccountName=myAccountName;
	AccountKey=myAccountKey;
	EndpointSuffix=mySuffix;


たとえば、接続文字列は次の接続文字列のように表示されます。

	DefaultEndpointsProtocol=https;
	AccountName=storagesample;
	AccountKey=<account-key>;
	EndpointSuffix=core.chinacloudapi.cn;

## 接続文字列の解析

[AZURE.INCLUDE [storage-cloud-configuration-manager-include](../../includes/storage-cloud-configuration-manager-include.md)]


## 次のステップ

- [開発とテストのための Azure のストレージ エミュレーター使用](storage-use-emulator.md)
- [Azure ストレージ エクスプローラー](storage-explorers.md)

<!---HONumber=AcomDC_0420_2016-->