---
title: AzCopy v10 の構成設定 (Azure Storage) | Microsoft Docs
description: この記事では、AzCopy V10 の構成設定に関するリファレンス情報を提供します。
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 04/02/2021
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 7c3ad407b0c412e3cf4dbb5cc1635376c718a747
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128605812"
---
# <a name="azcopy-v10-configuration-settings-azure-storage"></a>AzCopy v10 の構成設定 (Azure Storage)

AzCopy は、ストレージ アカウント間の BLOB またはファイル コピーに利用できるコマンドライン ユーティリティです。 この記事には、AzCopy v10 を構成するために使用できる環境変数の一覧が含まれています。

> [!NOTE]
> AzCopy の使用の開始に役立つコンテンツを探している場合は、「[AzCopy を使ってみる](storage-use-azcopy-v10.md)」を参照してください。

## <a name="azcopy-v10-environment-variables"></a>AzCopy v10 の環境変数

次の表では、各環境変数について説明し、その変数の使用に役立つコンテンツへのリンクを示します。

| 環境変数 | 説明 |
|--|--|
| AWS_ACCESS_KEY_ID | アマゾン ウェブ サービスのアクセス キー。 アマゾン ウェブ サービスで承認を行うためのキーを提供します。「[AzCopy を使用して Amazon S3 から Azure Storage にデータをコピーする](storage-use-azcopy-s3.md)」 |
| AWS_SECRET_ACCESS_KEY | アマゾン ウェブ サービスのシークレット アクセス キーによって、アマゾン ウェブ サービスで承認を行うための秘密鍵が提供されます。 [AzCopy を使用して Amazon S3 から Azure Storage にデータをコピーする](storage-use-azcopy-s3.md) |
| AZCOPY_ACTIVE_DIRECTORY_ENDPOINT | 利用する Azure Active Directory エンドポイント。 この変数は自動ログインにのみ使用されます。login コマンドを呼び出すときは、代わりにコマンド ライン フラグを使用してください。 |
| AZCOPY_AUTO_LOGIN_TYPE | この変数を `DEVICE`、`MSI`、または `SPN` に設定します。 この変数を使用すると、`azcopy login` コマンドを使用せずに承認できます。 このメカニズムは、お使いのオペレーティング システムに Linux の "*キーリング*" などのシークレット ストアがない場合に役立ちます。 「[シークレット ストアなしで承認する](storage-use-azcopy-authorize-azure-active-directory.md#authorize-without-a-secret-store)」を参照してください。 |
| AZCOPY_BUFFER_GB | ファイルをダウンロードおよびアップロードするときに AzCopy で使用するシステム メモリの最大量を指定します。 この値はギガバイト (GB) 単位で指定します。 「[メモリ使用量を最適化する](storage-use-azcopy-optimize.md#optimize-memory-use)」を参照してください。 |
| AZCOPY_CACHE_PROXY_LOOKUP | 既定で、Windows 上の AzCopy では、プロキシ サーバーの参照はホスト名レベルでキャッシュされます (URL パスは考慮されません)。 このキャッシュを無効にするには、"true" 以外の任意の値に設定してください。 |
| AZCOPY_CONCURRENCY_VALUE | 同時に行える要求の数を指定します。 この変数を使用すると、スループットを向上させることができます。 コンピューターの CPU が 5 個未満の場合、この変数の値は `32` に設定されます。 それ以外の場合、既定値は CPU の数に 16 を掛けた数です。 この変数の最大の既定値は `3000` ですが、この値は手動で高い値または低い値に設定できます。 「[コンカレンシーを向上させる](storage-use-azcopy-optimize.md#increase-concurrency)」を参照してください。 |
| AZCOPY_CONCURRENT_FILES | 同時に転送を開始するファイルの数を制御することによって、同時に進行中となるファイルの (概算の) 数をオーバーライドします。 |
| AZCOPY_CONCURRENT_SCAN | スキャン中に使用される並列処理の (最大) 次数を制御します。 Azure Files または BLOB とローカル ファイル システムを含む、並列化された列挙子にのみ影響します。 |
| AZCOPY_CONTENT_TYPE_MAP  | オペレーティング システムによって定義されている 1 つ以上の既定の MIME の種類のマッピングをオーバーライドします。 この変数を、いずれかのマッピングを定義する JSON ファイルのパスに設定します。  JSON ファイルの例の内容を次に示します。 <br><br> {<br>&nbsp;&nbsp;"MIMETypeMapping": { <br>&nbsp;&nbsp;&nbsp;&nbsp;".323": "text/h323",<br>&nbsp;&nbsp;&nbsp;&nbsp;".aaf": "application/octet-stream",<br>&nbsp;&nbsp;&nbsp; ".aca": "application/octet-stream",<br>&nbsp;&nbsp;&nbsp;&nbsp;".accdb": "application/msaccess",<br>&nbsp;&nbsp;&nbsp;&nbsp;  }<br>}
|
| AZCOPY_DEFAULT_SERVICE_API_VERSION | AzCopy が Azure Stack などのカスタム環境に対応できるように、サービス API のバージョンをオーバーライドします。 |
| AZCOPY_DISABLE_HIERARCHICAL_SCAN | Azure BLOB がコピー元の場合にのみ適用されます。 同時スキャンの方が高速ですが、階層型一覧表示 API が使用されるため、IO またはコストが増加する可能性があります。 パフォーマンスを犠牲にしてコストを節約するには、"true" を指定します。 |
| AZCOPY_JOB_PLAN_LOCATION | ディスクがいっぱいにならないように、(進行状況の追跡と再開に使用される) ジョブ プラン ファイルの格納場所をオーバーライドします。 |
| AZCOPY_LOG_LOCATION | ディスクがいっぱいにならないように、ログ ファイルの格納場所をオーバーライドします。 |
| AZCOPY_MSI_CLIENT_ID | ユーザー割り当てマネージド ID のクライアント ID。 `AZCOPY_AUTO_LOGIN_TYPE` が `MSI` に設定されている場合に使用します。 「[シークレット ストアなしで承認する](storage-use-azcopy-authorize-azure-active-directory.md#authorize-without-a-secret-store)」を参照してください。 |
| AZCOPY_MSI_OBJECT_ID | ユーザー割り当てマネージド ID のオブジェクト ID。 `AZCOPY_AUTO_LOGIN_TYPE` が `MSI` に設定されている場合に使用します。 「[シークレット ストアなしで承認する](storage-use-azcopy-authorize-azure-active-directory.md#authorize-without-a-secret-store)」を参照してください。 |
| AZCOPY_MSI_RESOURCE_STRING | ユーザー割り当てマネージド ID のリソース ID。 「[シークレット ストアなしで承認する](storage-use-azcopy-authorize-azure-active-directory.md#authorize-without-a-secret-store)」を参照してください。 |
| AZCOPY_PACE_PAGE_BLOBS | ページ BLOB のスループットを、サービスの制限に合わせて自動的に調整する必要があるかどうか。 既定値は true です。 無効にするには "false" に設定します |
| AZCOPY_PARALLEL_STAT_FILES | ローカル ファイル システムをスキャンするときに、AzCopy によって並列 "スレッド" のファイル プロパティが検索されます。  スレッドは、AZCOPY_CONCURRENT_SCAN によって定義されたプールから取得されます。  これを true に設定すると、Linux でのスキャンのパフォーマンスが向上する場合があります。  Windows では不要であり、推奨もされていません。 |
| AZCOPY_SHOW_PERF_STATES | これを何らかに設定すると、画面上の出力には、状態別のチャンクの数が含まれます。 |
| AZCOPY_SPA_APPLICATION_ID | サービス プリンシパルのアプリ登録のアプリケーション ID。 `AZCOPY_AUTO_LOGIN_TYPE` が `SPN` に設定されている場合に使用します。 「[シークレット ストアなしで承認する](storage-use-azcopy-authorize-azure-active-directory.md#authorize-without-a-secret-store)」を参照してください。 |
| AZCOPY_SPA_CERT_PASSWORD | 証明書のパスワード。 `AZCOPY_AUTO_LOGIN_TYPE` が `SPN` に設定されている場合に使用します。 「[シークレット ストアなしで承認する](storage-use-azcopy-authorize-azure-active-directory.md#authorize-without-a-secret-store)」を参照してください。 |
| AZCOPY_SPA_CERT_PATH | 証明書ファイルへの相対または完全修飾パス。 `AZCOPY_AUTO_LOGIN_TYPE` が `SPN` に設定されている場合に使用します。 「[シークレット ストアなしで承認する](storage-use-azcopy-authorize-azure-active-directory.md#authorize-without-a-secret-store)」を参照してください。 |
| AZCOPY_SPA_CLIENT_SECRET | クライアント シークレット。 `AZCOPY_AUTO_LOGIN_TYPE` が `SPN` に設定されている場合に使用します。 「[シークレット ストアなしで承認する](storage-use-azcopy-authorize-azure-active-directory.md#authorize-without-a-secret-store)」を参照してください。 |
| AZCOPY_TENANT_ID | OAuth デバイス対話型ログインに使用する Azure Active Directory テナント ID。 この変数は自動ログインにのみ使用されます。login コマンドを呼び出すときは、代わりにコマンド ライン フラグを使用してください。 |
| AZCOPY_TUNE_TO_CPU | (ベンチマーク コマンドなどで) コンカレンシー レベルを自動調整するときに AzCopy で CPU 使用率が考慮されないようにするには、false に設定します。 |
| AZCOPY_USER_AGENT_PREFIX | 既定の AzCopy ユーザー エージェントにプレフィックスを追加します。これはテレメトリの目的で使用されます。 スペースが自動的に挿入されます。 |
| GOOGLE_APPLICATION_CREDENTIALS | サービス アカウント キー ファイルへの絶対パスによって、Google Cloud Storage での承認を行うためのキーが提供されます。 [AzCopy を使用して Google Cloud Storage から Azure Storage にデータをコピーする (プレビュー)](storage-ref-azcopy-configuration-settings.md) |
| HTTPS_PROXY | AzCopy のプロキシ設定を構成します。 この変数は、プロキシ IP アドレスとプロキシ ポート番号に設定します。 たとえば、「 `xx.xxx.xx.xxx:xx` 」のように入力します。 Windows で AzCopy を実行すると、AzCopy によって自動的にプロキシ設定が検出されるため、Windows でこの設定を使用する必要はありません。 Windows でこの設定を使用することを選択した場合は、自動検出がオーバーライドされます。 「[プロキシ設定の構成](#configure-proxy-settings)」を参照してください。 |

## <a name="configure-proxy-settings"></a>プロキシ設定の構成

AzCopy v10 のプロキシ設定を構成するには、`HTTPS_PROXY` 環境変数を設定します。 Windows で AzCopy を実行すると、AzCopy によって自動的にプロキシ設定が検出されるため、Windows でこの設定を使用する必要はありません。 Windows でこの設定を使用することを選択した場合は、自動検出がオーバーライドされます。

| オペレーティング システム | コマンド  |
|--------|-----------|
| **Windows** | コマンド プロンプトでは、`set HTTPS_PROXY=<proxy IP>:<proxy port>` を使用します<br> PowerShell では、`$env:HTTPS_PROXY="<proxy IP>:<proxy port>"` を使用します|
| **Linux** | `export HTTPS_PROXY=<proxy IP>:<proxy port>` |
| **macOS** | `export HTTPS_PROXY=<proxy IP>:<proxy port>` |

現在のところ、AzCopy は、NTLM または Kerberos による認証を必要とするプロキシをサポートしていません。

### <a name="bypassing-a-proxy"></a>プロキシのバイパス

Windows で AzCopy を実行していて、プロキシを使用 *しない* ように (設定の自動検出ではなく) 指定する場合、次のコマンドを使用します。 これらの設定を使用すると、AzCopy はプロキシを検索したり、使用したりしません。

| オペレーティング システム | 環境 | コマンド  |
|--------|-----------|----------|
| **Windows** | コマンド プロンプト (CMD) | `set HTTPS_PROXY=dummy.invalid` <br>`set NO_PROXY=*`|
| **Windows** | PowerShell | `$env:HTTPS_PROXY="dummy.invalid"` <br>`$env:NO_PROXY="*"`<br>|

他のオペレーティング システムでは、プロキシを使用しない場合は HTTPS_PROXY 変数の設定を解除してください。

## <a name="see-also"></a>関連項目

- [AzCopy を使ってみる](storage-use-azcopy-v10.md)
- [Azure Storage で AzCopy v10 のパフォーマンスを最適化する](storage-use-azcopy-optimize.md)
- [ログ ファイルを使用した Azure Storage での AzCopy V10 の問題のトラブルシューティング](storage-use-azcopy-configure.md)
