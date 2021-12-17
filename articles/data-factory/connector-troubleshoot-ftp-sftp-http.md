---
title: FTP、SFTP、および HTTP コネクタのトラブルシューティング
titleSuffix: Azure Data Factory & Azure Synapse
description: Azure Data Factory と Azure Synapse Analytics の FTP、SFTP、および HTTP コネクタの問題のトラブルシューティングの方法について説明します。
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.topic: troubleshooting
ms.date: 10/01/2021
ms.author: jianleishen
ms.custom: has-adal-ref, synapse
ms.openlocfilehash: 53c70456f754dd451d3bb3cfa3bed0c6ddac3461
ms.sourcegitcommit: 7bd48cdf50509174714ecb69848a222314e06ef6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/02/2021
ms.locfileid: "129390447"
---
# <a name="troubleshoot-the-ftp-sftp-and-http-connectors-in-azure-data-factory-and-azure-synapse"></a>Azure Data Factory と Azure Synapse の FTP、SFTP、および HTTP コネクタのトラブルシューティング

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

この記事では、Azure Data Factory と Azure Synapse の FTP、SFTP、および HTTP コネクタの一般的な問題のトラブルシューティングのための提案を示します。

## <a name="ftp"></a>FTP

### <a name="error-code-ftpfailedtoconnecttoftpserver"></a>エラー コード:FtpFailedToConnectToFtpServer

- **メッセージ**: `Failed to connect to FTP server. Please make sure the provided server information is correct, and try again.`

- **原因**:FTP サーバーに対し、FTP サーバーへの接続に Secure FTP (SFTP) リンク サービスを使用するなど、リンク サービス タイプとして正しくないものが使用されている可能性があります。

- **推奨事項**:ターゲット サーバーのポートを確認します。 FTP はポート 21 を使用します。

### <a name="error-code-ftpfailedtoreadftpdata"></a>エラー コード: FtpFailedToReadFtpData

- **メッセージ**: `Failed to read data from ftp: The remote server returned an error: 227 Entering Passive Mode (*,*,*,*,*,*).`

- **原因**: データ ファクトリまたは Synapse のパイプラインでサポートされているパッシブ モードでのデータ転送用に 1024 から 65535 のポート範囲が開かれていません。

- **推奨事項**: ターゲット サーバーのファイアウォール設定を確認してください。 ポート 1024 から 65535 または FTP サーバーで指定されたポート範囲を、SHIR または Azure IR の IP アドレスに対して開きます。

## <a name="sftp"></a>SFTP

#### <a name="error-code-sftpoperationfail"></a>エラー コード:SftpOperationFail

- **メッセージ**: `Failed to '%operation;'. Check detailed error from SFTP.`

- **原因**:SFTP 操作に問題があります。

- **推奨事項**:SFTP からエラーの詳細を確認します。


### <a name="error-code-sftprenameoperationfail"></a>エラー コード:SftpRenameOperationFail

- **メッセージ**: `Failed to rename the temp file. Your SFTP server doesn't support renaming temp file, set "useTempFileRename" as false in copy sink to disable uploading to temp file.`

- **原因**:SFTP サーバーでは、一時ファイルの名前変更はサポートされません。

- **推奨事項**:コピー シンクで "useTempFileRename" を false に設定して、一時ファイルへのアップロードを無効にします。


### <a name="error-code-sftpinvalidsftpcredential"></a>エラー コード:SftpInvalidSftpCredential

- **メッセージ**: `Invalid SFTP credential provided for '%type;' authentication type.`

- **原因**:秘密キーのコンテンツは Azure Key Vault または SDK からフェッチされますが、正しくエンコードされていません。

- **推奨事項**:  

    秘密キーのコンテンツが Key Vault からのものである場合、SFTP リンク サービスに直接アップロードすると、元のキー ファイルが機能する可能性があります。

    詳細については、[データ ファクトリまたは Synapse パイプラインを使用した SFTP サーバーとの間でのデータのコピー](./connector-sftp.md#use-ssh-public-key-authentication)に関する記事を参照してください。 秘密キーのコンテンツは、Base64 でエンコードされた SSH 秘密キーのコンテンツです。

    Base64 エンコードを使用して元の秘密キー ファイル "*全体*" をエンコードし、エンコードされた文字列を Key Vault に格納します。 ファイルから **[アップロード]** を選択した場合、元の秘密キー ファイルが、SFTP リンク サービスで使用できるキー ファイルです。

    次に、文字列の生成に使用できるサンプルをいくつか示します。

    - C# コードを使用した場合:

        ```
        byte[] keyContentBytes = File.ReadAllBytes(Private Key Path);
        string keyContent = Convert.ToBase64String(keyContentBytes, Base64FormattingOptions.None);
        ```

    - Python コードを使用した場合:

        ```
        import base64
        rfd = open(r'{Private Key Path}', 'rb')
        keyContent = rfd.read()
        rfd.close()
        print base64.b64encode(Key Content)
        ```

    - サードパーティの Base64 変換ツールを使用します。 [Encode to Base64 format](https://www.base64encode.org/) ツールをお勧めします。

- **原因**:間違ったキー コンテンツ形式が選択されました。

- **推奨事項**:  

    現在、PKCS#8 形式の SSH 秘密キー (先頭が "-----BEGIN ENCRYPTED PRIVATE KEY-----") は、SFTP サーバーへのアクセスではサポートされていません。 

    キーを "-----BEGIN RSA PRIVATE KEY-----" から始まる従来の SSH キー形式に変換するには、次のコマンドを実行します。

    ```
    openssl pkcs8 -in pkcs8_format_key_file -out traditional_format_key_file
    chmod 600 traditional_format_key_file
    ssh-keygen -f traditional_format_key_file -p
    ```

- **原因**:資格情報または秘密キーのコンテンツが無効です。

- **推奨事項**:キー ファイルまたはパスワードが正しいかどうかを確認するには、WinSCP などのツールを使用して再確認します。

### <a name="sftp-copy-activity-failed"></a>SFTP Copy アクティビティが失敗しました

- **現象**: 
  * エラー コード:UserErrorInvalidColumnMappingColumnNotFound 
  * エラー メッセージ: `Column 'AccMngr' specified in column mapping cannot be found in source data.`

- **原因**:ソースには、"AccMngr" という名前の列が含まれていません。

- **解決方法**:"AccMngr" 列が存在するかどうかを判断するには、コピー先のデータセット列をマップして、データセットの構成を再確認します。


### <a name="error-code-sftpfailedtoconnecttosftpserver"></a>エラー コード:SftpFailedToConnectToSftpServer

- **メッセージ**: `Failed to connect to SFTP server '%server;'.`

- **原因**:エラー メッセージに "ソケット読み取り操作が 3 万ミリ秒後にタイムアウトしました" という文字列が含まれている場合、原因の 1 つとして、SFTP サーバーに無効なリンクされたサービス タイプが使用されていることが考えられます。 たとえば、SFTP サーバーへの接続に、FTP リンク サービスを使用している可能性があります。

- **推奨事項**:ターゲット サーバーのポートを確認します。 既定では、SFTP はポート 22 を使用します。

- **原因**:エラー メッセージに "Server response does not contain SSH protocol identification" (サーバーの応答に SSH プロトコルの ID が含まれていません) という文字列が含まれている場合、原因の 1 つとして、SFTP サーバーによって接続が調整されたことが考えられます。 SFTP サーバーから並列でダウンロードするために複数の接続が作成されるため、SFTP サーバーの帯域幅調整が行われる場合があります。 通常、調整が発生すると、サーバーごとに異なるエラーが返されます。

- **推奨事項**:  

    SFTP データセットのコンカレント接続の最大数を 1 として指定し、Copy アクティビティを再実行します。 アクティビティが成功した場合は、調整が原因であることを確認できます。

    低スループットを昇格する場合は、SFTP 管理者に問い合わせて、コンカレント接続数の上限を引き上げるか、次のいずれかを実行できます。

    * セルフホステッド IR を使用している場合は、セルフホステッド IR マシンの IP を許可リストに追加します。
    * Azure IR を使用している場合は、[Azure Integration Runtime IP アドレス](./azure-integration-runtime-ip-addresses.md)を追加します。 SFTP サーバーの許可リストに IP の範囲を追加しない場合は、代わりにセルフホステッド IR を使用してください。


### <a name="error-code-sftppermissiondenied"></a>エラー コード: SftpPermissionDenied

- **メッセージ**: `Permission denied to access '%path;'`

- **原因**: 指定されたユーザーには、操作するときにフォルダーまたはファイルに対する読み取りまたは書き込みのアクセス許可がありません。

- **推奨事項**: SFTP サーバー上のフォルダーまたはファイルに対する読み取りまたは書き込みのアクセス許可をユーザーに付与します。
 
### <a name="error-code-sftpauthenticationfailure"></a>エラー コード: SftpAuthenticationFailure

- **メッセージ**: `Meet authentication failure when connect to Sftp server '%server;' using '%type;' authentication type. Please make sure you are using the correct authentication type and the credential is valid. For more details, see our troubleshooting docs.`

- **原因**: 指定された資格情報 (パスワードまたは秘密キー) が無効です。

- **推奨事項**: 資格情報を確認してください。

- **原因**: 指定された認証の種類は、SFTP サーバーで認証を完了するために許可されていないか、十分ではありません。

- **推奨事項**: 正しい認証の種類を使用するために、次のオプションを適用します。
    - サーバーでパスワードが必要な場合は、"Basic" を使用します。
    - サーバーで秘密キーが必要な場合は、"SSH 公開キー認証" を使用します。
    - サーバーで "パスワード" と "秘密キー" の両方が必要な場合は、"多要素認証" を使用します。

- **原因**: SFTP サーバーで認証に "keyboard-interactive" が必要ですが、"password" が指定されています。

- **推奨事項**: 

    "keyboard-interactive" は特殊な認証方法であり、"password" とは異なります。 つまり、サーバーにログインするときに、パスワードを手動で入力する必要があり、以前に保存したパスワードを使用することはできません。 ただし Azure Data Factory (ADF) はスケジュールされたデータ転送サービスであり、実行時にパスワードを入力するためのポップアップ入力ボックスはありません。 <br/> 
    
    妥協策として、実際の手動入力の代わりにバックグラウンドで入力をシミュレートするオプションが用意されており、これは "keyboard-interactive" を "password" に変更することと同じです。 このセキュリティ上の懸念を受け入れることができる場合は、次の手順に従って有効にします。<br/> 
    1. ADF ポータルで、SFTP リンク サービスにマウス ポインターを移動し、コード ボタンを選択してそのペイロードを開きます。
    1. "typeProperties" セクションに `"allowKeyboardInteractiveAuth": true` を追加します。

## <a name="http"></a>HTTP

### <a name="error-code-httpfilefailedtoread"></a>エラー コード:HttpFileFailedToRead

- **メッセージ**: `Failed to read data from http server. Check the error from http server：%message;`

- **原因**: このエラーは、データ ファクトリまたは Synapse のパイプラインが HTTP サーバーと通信しているが、HTTP 要求操作が失敗した場合に発生します。

- **推奨事項**:エラー メッセージの HTTP ステータス コードを確認し、リモート サーバーの問題を修正します。

## <a name="next-steps"></a>次のステップ

トラブルシューティングのその他のヘルプについては、次のリソースを参照してください。

- [コネクタのトラブルシューティング ガイド](connector-troubleshoot-guide.md)
- [Data Factory ブログ](https://azure.microsoft.com/blog/tag/azure-data-factory/)
- [Data Factory の機能のリクエスト](/answers/topics/azure-data-factory.html)
- [Azure のビデオ](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
- [Microsoft Q&A ページ](/answers/topics/azure-data-factory.html)
- [Data Factory の Stack Overflow フォーラム](https://stackoverflow.com/questions/tagged/azure-data-factory)
- [Data Factory に関する Twitter 情報](https://twitter.com/hashtag/DataFactory)
