---
title: "Azure Data Factory を使用して SFTP サーバーからデータをコピーする | Microsoft Docs"
description: "SFTP サーバーからシンクとしてサポートされているデータ ストアに データをコピーできる Azure Data Factory の MySQL コネクタについて説明します。"
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/12/2018
ms.author: jingwang
ms.openlocfilehash: 55379add493224770ca7e0e26fd607cd0a2cf892
ms.sourcegitcommit: b32d6948033e7f85e3362e13347a664c0aaa04c1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/13/2018
---
# <a name="copy-data-from-sftp-server-using-azure-data-factory"></a>Azure Data Factory を使用して SFTP サーバーからデータをコピーする
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [バージョン 1 - 一般公開](v1/data-factory-sftp-connector.md)
> * [バージョン 2 - プレビュー](connector-sftp.md)

この記事では、Azure Data Factory のコピー アクティビティを使用して、SFTP サーバーからデータをコピーする方法について説明します。 この記事は、コピー アクティビティの概要を示している[コピー アクティビティの概要](copy-activity-overview.md)に関する記事に基づいています。

> [!NOTE]
> この記事は、現在プレビュー段階にある Data Factory のバージョン 2 に適用されます。 一般公開 (GA) されている Data Factory サービスのバージョン 1 を使用している場合は、[V1 の SFTP コネクタ](v1/data-factory-sftp-connector.md)を参照してください。

## <a name="supported-capabilities"></a>サポートされる機能

SFTP サーバーから、サポートされている任意のシンク データ ストアにデータをコピーできます。 コピー アクティビティによってソースまたはシンクとしてサポートされているデータ ストアの一覧については、[サポートされているデータ ストア](copy-activity-overview.md#supported-data-stores-and-formats)に関する記事の表をご覧ください。

具体的には、この SFTP コネクタは以下をサポートします。

- **Basic** または **SshPublicKey** 認証を使用したファイルのコピー。
- ファイルをそのままコピーするか、[サポートされているファイル形式と圧縮コーデック](supported-file-formats-and-compression-codecs.md)を使用したファイルの解析。

## <a name="get-started"></a>作業開始

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

次のセクションでは、SFTP に固有の Data Factory エンティティの定義に使用されるプロパティについて詳しく説明します。

## <a name="linked-service-properties"></a>リンクされたサービスのプロパティ

SFTP のリンクされたサービスでは、次のプロパティがサポートされます。

| プロパティ | [説明] | 必須 |
|:--- |:--- |:--- |
| 型 | type プロパティを **Sftp** に設定する必要があります。 |[はい] |
| host | SFTP サーバーの名前または IP アドレス。 |[はい] |
| ポート | SFTP サーバーがリッスンしているポート。<br/>使用可能な値: 整数。既定値は **22**。 |いいえ  |
| skipHostKeyValidation | ホスト キーの検証をスキップするかどうかを指定します。<br/>使用可能な値: **true**、**false** (既定値)。  | いいえ  |
| hostKeyFingerprint | ホスト キーの指紋を指定します。 | はい ("SkipHostKeyValidation" が false に設定されている場合)。  |
| authenticationType | 認証の種類を指定します。<br/>使用可能な値: **Basic** および **SshPublicKey**。 プロパティと JSON サンプルの詳細については、「[基本認証を使用する](#using-basic-authentication)」および「[SSH 公開キー認証を使用する](#using-ssh-public-key-authentication)」をそれぞれ参照してください。 |[はい] |
| connectVia | データ ストアに接続するために使用される[統合ランタイム](concepts-integration-runtime.md)。 Azure 統合ランタイムまたは自己ホスト型統合ランタイム (データ ストアがプライベート ネットワークにある場合) を使用できます。 指定されていない場合は、既定の Azure 統合ランタイムが使用されます。 |いいえ  |

### <a name="using-basic-authentication"></a>基本認証を使用する

基本認証を使用するには、"authenticationType" を **Basic** に設定し、前のセクションで導入した一般的な SFTP コネクタ プロパティのほかに、次のプロパティを指定します。

| プロパティ | [説明] | 必須 |
|:--- |:--- |:--- |
| userName | SFTP サーバーにアクセスできるユーザー。 |[はい] |
| password | ユーザー (userName) のパスワード。 このフィールドを SecureString としてマークして Data Factory に安全に保管するか、[Azure Key Vault に格納されているシークレットを参照](store-credentials-in-key-vault.md)します。 | [はい] |

**例:**

```json
{
    "apiVersion": "2017-09-01-preview",
    "name": "SftpLinkedService",
    "type": "linkedservices",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "<sftp server>",
            "port": 22,
            "skipHostKeyValidation": false,
            "hostKeyFingerPrint": "ssh-rsa 2048 xx:00:00:00:xx:00:x0:0x:0x:0x:0x:00:00:x0:x0:00",
            "authenticationType": "Basic",
            "userName": "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="using-ssh-public-key-authentication"></a>SSH 公開キー認証を使用する

SSH 公開キー認証を使用するには、"authenticationType" を **SshPublicKey** に設定し、前のセクションで導入した一般的な SFTP コネクタ プロパティのほかに、次のプロパティを指定します。

| プロパティ | [説明] | 必須 |
|:--- |:--- |:--- |
| userName | SFTP サーバーにアクセスできるユーザー |[はい] |
| privateKeyPath | 統合ランタイムがアクセスできる秘密キー ファイルへの絶対パスを指定します。 セルフホステッド統合ランタイムが "connectVia" で指定されている場合にのみ適用されます。 | `privateKeyPath` または `privateKeyContent` を指定します。  |
| privateKeyContent | Base64 にエンコードされた SSH 秘密キーのコンテンツ。 SSH 秘密キーは、OpenSSH 形式にする必要があります。 このフィールドを SecureString としてマークして Data Factory に安全に保管するか、[Azure Key Vault に格納されているシークレットを参照](store-credentials-in-key-vault.md)します。 | `privateKeyPath` または `privateKeyContent` を指定します。 |
| passPhrase | キー ファイルがパス フレーズで保護されている場合は、パス フレーズ/パスワードを指定して、秘密キーを復号化します。 このフィールドを SecureString としてマークして Data Factory に安全に保管するか、[Azure Key Vault に格納されているシークレットを参照](store-credentials-in-key-vault.md)します。 | はい (秘密キー ファイルがパス フレーズで保護されている場合)。 |

> [!NOTE]
> SFTP コネクタは RSA/DSA OpenSSH キーをサポートします。 キー ファイルの内容が "-----BEGIN [RSA/DSA] PRIVATE KEY-----" で始まることを確認します。 秘密キー ファイルが ppk 形式である場合は、Putty ツールを使用して .ppk から OpenSSH 形式に変換してください。 

**例 1: 秘密キー ファイルの filePath を使用した SshPublicKey 認証**

```json
{
    "apiVersion": "2017-09-01-preview",
    "name": "SftpLinkedService",
    "type": "Linkedservices",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "<sftp server>",
            "port": 22,
            "skipHostKeyValidation": true,
            "authenticationType": "SshPublicKey",
            "userName": "xxx",
            "privateKeyPath": "D:\\privatekey_openssh",
            "passPhrase": {
                "type": "SecureString",
                "value": "<pass phrase>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**例 2: 秘密キーのコンテンツを使用した SshPublicKey 認証**

```json
{
    "apiVersion": "2017-09-01-preview",
    "name": "SftpLinkedService",
    "type": "Linkedservices",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "<sftp server>",
            "port": 22,
            "skipHostKeyValidation": true,
            "authenticationType": "SshPublicKey",
            "userName": "<username>",
            "privateKeyContent": {
                "type": "SecureString",
                "value": "<base64 string of the private key content>"
            },
            "passPhrase": {
                "type": "SecureString",
                "value": "<pass phrase>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>データセットのプロパティ

データセットを定義するために使用できるセクションとプロパティの完全な一覧については、データセットに関する記事をご覧ください。 このセクションでは、SFTP データセット でサポートされるプロパティの一覧を示します。

SFTP からデータをコピーするには、データセットの type プロパティを **FileShare** に設定します。 次のプロパティがサポートされています。

| プロパティ | [説明] | 必須 |
|:--- |:--- |:--- |
| 型 | データセットの type プロパティは **FileShare** に設定する必要があります。 |[はい] |
| folderPath | フォルダーへのパス。 例: folder/subfolder/ |[はい] |
| fileName | 特定のファイルからコピーする場合は、**folderPath** にファイルの名前を指定します。 このプロパティの値を設定しない場合、データセットはフォルダー内のすべてのファイルをソースとしてポイントします。 |いいえ  |
| fileFilter | すべてのファイルではなく、folderPath 内のファイルのサブセットを選択するために使用するフィルターを指定します。 fileName が指定されていない場合にのみ適用されます。 <br/><br/>使用可能な値: `*` (複数の文字) および `?` (単一の文字)。<br/>- 例 1: `"fileFilter": "*.log"`<br/>- 例 2: `"fileFilter": 2017-09-??.txt"` |いいえ  |
| format | ファイルベースのストア間で**ファイルをそのままコピー** (バイナリ コピー) する場合は、入力と出力の両方のデータセット定義で format セクションをスキップします。<br/><br/>ファイルを特定の形式で解析する場合、次のファイル形式がサポートされます。**TextFormat**、**JsonFormat**、**AvroFormat**、**OrcFormat**、**ParquetFormat**。 形式の **type** プロパティをいずれかの値に設定します。 詳細については、[Text Format](supported-file-formats-and-compression-codecs.md#text-format)、[Json Format](supported-file-formats-and-compression-codecs.md#json-format)、[Avro Format](supported-file-formats-and-compression-codecs.md#avro-format)、[Orc Format](supported-file-formats-and-compression-codecs.md#orc-format)、[Parquet Format](supported-file-formats-and-compression-codecs.md#parquet-format) の各セクションを参照してください。 |いいえ (バイナリ コピー シナリオのみ) |
| compression | データの圧縮の種類とレベルを指定します。 詳細については、[サポートされるファイル形式と圧縮コーデック](supported-file-formats-and-compression-codecs.md#compression-support)に関する記事を参照してください。<br/>サポートされる種類は、**GZip**、**Deflate**、**BZip2**、および **ZipDeflate** です。<br/>サポートされるレベルは、**Optimal** と **Fastest** です。 |いいえ  |

**例:**

```json
{
    "apiVersion": "2017-09-01-preview",
    "name": "SFTPDataset",
    "type": "Datasets",
    "properties": {
        "type": "FileShare",
        "linkedServiceName":{
            "referenceName": "<SFTP linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "folderPath": "folder/subfolder/",
            "fileName": "myfile.csv.gz",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ",",
                "rowDelimiter": "\n"
            },
            "compression": {
                "type": "GZip",
                "level": "Optimal"
            }
        }
    }
}
```

## <a name="copy-activity-properties"></a>コピー アクティビティのプロパティ

アクティビティの定義に利用できるセクションとプロパティの完全な一覧については、[パイプライン](concepts-pipelines-activities.md)に関する記事を参照してください。 このセクションでは、SFTP ソースでサポートされるプロパティの一覧を示します。

### <a name="sftp-as-source"></a>ソースとしての SFTP

SFTP からデータをコピーするには、コピー アクティビティのソース タイプを **FileSystemSource** に設定します。 コピー アクティビティの **source** セクションでは、次のプロパティがサポートされます。

| プロパティ | [説明] | 必須 |
|:--- |:--- |:--- |
| 型 | コピー アクティビティのソースの type プロパティを **FileSystemSource** に設定する必要があります。 |[はい] |
| recursive | データをサブ フォルダーから再帰的に読み取るか、指定したフォルダーからのみ読み取るかを指定します。 recursive が true に設定され、シンクがファイル ベースのストアである場合、空のフォルダー/サブフォルダーはシンクでコピー/作成されないことに注意してください。<br/>使用可能な値: **true** (既定値)、**false** | いいえ  |

**例:**

```json
"activities":[
    {
        "name": "CopyFromSFTP",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SFTP input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "FileSystemSource",
                "recursive": true
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```


## <a name="next-steps"></a>次の手順
Azure Data Factory のコピー アクティビティによってソースおよびシンクとしてサポートされるデータ ストアの一覧については、[サポートされるデータ ストア](copy-activity-overview.md##supported-data-stores-and-formats)の表をご覧ください。
