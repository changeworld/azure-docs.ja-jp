<properties 
	pageTitle="Azure 接続文字列の構成" 
	description="Azure のストレージ アカウントへの接続文字列の設定方法を説明します。" 
	services="storage" 
	documentationCenter="" 
	authors="tamram" 
	manager="adinah" 
	editor="cgronlun"/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/20/2015" 
	ms.author="tamram"/>

# Azure 接続文字列の構成

## 概要
接続文字列には、Azure でストレージ アカウントにアクセスするために必要なパラメーターが含まれています。接続文字列は、次の方法で構成できます。

- サービスやアプリケーションをローカルでテストしているときに、Azure ストレージ エミュレーターに接続します。

- ストレージ サービスの既定のエンドポイントを使用して、Azure でストレージ アカウントに接続します。

- ストレージ サービスの明示的なエンドポイントを使用して、Azure でストレージ アカウントに接続します。

アプリケーションが Azure で実行されているクラウド サービスの場合、接続文字列を保存する最も便利な場所は [Azure サービスの構成スキーマ (.cscfg ファイル)](https://msdn.microsoft.com/library/ee758710.aspx) です。アプリケーションが別の環境 (たとえば、デスクトップで) で実行されている場合、接続文字列は app.config ファイルまたは別の設定ファイルに保存すると便利な場合があります。Azure CloudConfigurationManager クラスを使用すれば、実行場所に関係なく、接続文字列にアクセスできます。

## ストレージ エミュレーターへの接続

ストレージ エミュレーターは、既知の名前とキーを持つローカル アカウントです。アカウント名とキーはすべてのユーザーで同じであるため、ショートカット文字列の形式を使用して、接続文字列内のストレージ エミュレーターを参照できます。接続文字列の値は、 `UseDevelopmentStorage=true` に設定します。

また、サービスをストレージ エミュレーターでテストする際に使用する HTTP プロキシを指定することもできます。これは、ストレージ サービスに対する操作をデバッグするとき、HTTP の要求と応答を調べる場合に便利です。プロキシを指定するには、DevelopmentStorageProxyUri オプションを接続文字列に追加し、値としてプロキシ URI を設定します。たとえば、次に示す接続文字列は、ストレージ エミュレーターを指しており、HTTP プロキシを構成しています。

    UseDevelopmentStorage=true;DevelopmentStorageProxyUri=http://myProxyUri

## Azure でのストレージ アカウントへの接続

Azure では、ストレージ アカウントへの接続文字列は次の 2 つの方法のいずれかを使用して定義できます。

- ストレージ サービスの既定のエンドポイントを前提とする方法。これは、接続文字列を作成する最も簡単な方法です。この接続文字列形式を使用する場合は、アカウント名とアカウント キーを指定し、HTTP または HTTPS のどちらを使用してストレージ アカウントに接続するかを指定するだけです。 

- ストレージ サービスのエンドポイントを明示的に指定する方法。この方法では、より複雑な接続文字列を作成できます。この文字列形式を使用すると、カスタム ドメイン名を含むストレージ サービス エンドポイントを指定したり、共有アクセス署名に基づく接続文字列に対して公開する情報を最小限にすることができます。


> [AZURE.NOTE] Azure ストレージ サービスは HTTP と HTTPS の両方をサポートしていますが、HTTPS の使用をお勧めします。

## 既定のエンドポイントを持つ接続文字列の作成

ストレージ サービスの既定のエンドポイントに依存する接続文字列を作成するには、次に示す接続文字列形式を使用します。HTTP と HTTPS のどちらを使用してストレージ アカウントに接続するかを指定し、myAccountName をストレージ アカウントの名前に、myAccountKey をアカウント アクセス キーに置き換えます。

    DefaultEndpointsProtocol=[http|https];AccountName=myAccountName;AccountKey=myAccountKey

たとえば、接続文字列は次のサンプルの接続文字列のように表示されます。

    DefaultEndpointsProtocol=https;AccountName=storagesample;AccountKey=KWPLd0rpW2T0U7K2pVpF8rYr1BgYtR7wYQk33AYiXeUoquiaY6o0TWqduxmPHlqeCNZ3LU0DHptbeIHy5l/Yhg==

## 明示的なエンドポイントを持つ接続文字列の作成

次のような場合は、サービスのエンドポイントを接続文字列で明示的に指定する必要があります。

- BLOB サービスで使用するために、ストレージ アカウントのカスタム ドメイン名を登録している場合。

- ストレージ リソースにアクセスするための共有アクセス署名がある場合。

### カスタム ドメイン名を使用して BLOB エンドポイントを指定する 
BLOB サービスで使用するためにカスタム ドメイン名が登録されている場合は、接続文字列で BLOB エンドポイントを明示的に構成する必要があります。接続文字列に示されているエンドポイント値を使用することで、BLOB サービスへの要求 URI が作成され、コードに返される URI の形式も決定されます。 

明示的なエンドポイントを指定する接続文字列を作成するには、次に示す文字列形式を使用して、プロトコル仕様 (HTTP または HTTPS) を含む完全なサービス エンドポイントをサービスごとに指定します。

    BlobEndpoint=myBlobEndpoint;QueueEndpoint=myQueueEndpoint;TableEndpoint=myTableEndpoint;[credentials]

サービスのエンドポイントを明示的に指定する場合、資格情報の指定方法には 2 つの方法があります。前のセクションで示されているようにアカウント名とアカウント キーを指定するか (`AccountName=myAccountName;AccountKey=myAccountKey`)、「**共有アクセス署名を持つエンドポイントを指定する**」のセクションで示されているように共有アクセス署名を指定ができます。アカウント名とアカウント キーを指定する場合、完全な文字列形式は次のようになります。
    BlobEndpoint=myBlobEndpoint;QueueEndpoint=myQueueEndpoint;TableEndpoint=myTableEndpoint;AccountName=myAccountName;AccountKey=myAccountKey

接続文字列では、BLOB、テーブル、キューのエンドポイントを指定できます。エンドポイントは 1 つ以上指定する必要がありますが、3 つのエンドポイントすべてを指定する必要はありません。たとえば、カスタムの BLOB エンドポイントで使用する接続文字列を作成する場合は、キューとテーブルのエンドポイントの指定は省略できます。接続文字列からキューとテーブルのエンドポイントを除外することを選択した場合、その接続文字列を使用してコードからキュー サービスやテーブル サービスにアクセスすることはできないことにご注意ください。

### 共有アクセス署名を持つエンドポイントを指定する 
明示的なエンドポイントを持つ接続文字列を作成し、共有アクセス署名を介してストレージ リソースにアクセスできます。この場合、アカウント名とアカウント キーの資格情報ではなく、共有アクセス署名を接続文字列に含めて指定できます。共有アクセス署名トークンは、アクセス先のリソース、アクセス可能な期間、付与される権限に関する情報をカプセル化します。共有アクセス署名の詳細については、「[共有アクセス署名を使用したアクセスの委任](https://msdn.microsoft.com/library/ee395415.aspx)」をご覧ください。

共有アクセス署名を含む接続文字列を作成するには、文字列を次の形式で指定します。

    BlobEndpoint=myBlobEndpoint; QueueEndpoint=myQueueEndpoint;TableEndpoint=myTableEndpoint;SharedAccessSignature=base64Signature

エンドポイントには、既定のサービス エンドポイントとカスタム エンドポイントのいずれかを指定できます。 `base64Signature` は、共有アクセス署名の署名部分に対応します。署名は、有効な署名対象文字列とキーを SHA256 アルゴリズムを使用して計算処理した後、その値を Base64 エンコードした HMAC 値です。

<!--HONumber=47-->
