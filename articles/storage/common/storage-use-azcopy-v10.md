---
title: AzCopy v10 を使用して Azure Storage にデータをコピーまたは移動する | Microsoft Docs
description: AzCopy は、ストレージ アカウント間のデータ コピーに利用できるコマンドライン ユーティリティです。 この記事は、AzCopy をダウンロードし、ストレージ アカウントに接続し、ファイルを転送する際に役立ちます。
services: storage
author: normesta
ms.service: storage
ms.topic: article
ms.date: 05/14/2019
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: 7d14de200f5301781130e01e75f1b7813389fa76
ms.sourcegitcommit: 6932af4f4222786476fdf62e1e0bf09295d723a1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/05/2019
ms.locfileid: "66688196"
---
# <a name="get-started-with-azcopy"></a>AzCopy を使ってみる

AzCopy は、ストレージ アカウント間の BLOB またはファイル コピーに利用できるコマンドライン ユーティリティです。 この記事は、AzCopy をダウンロードし、ストレージ アカウントに接続し、ファイルを転送する際に役立ちます。

> [!NOTE]
> AzCopy **V10** が現在サポートされているバージョンの AzCopy です。
>
> AzCopy **v8.1** を使用する必要がある場合、この記事の「[以前のバージョンの AzCopy の使用](#previous-version)」を参照してください。

<a id="download-and-install-azcopy" />

## <a name="download-azcopy"></a>AzCopy をダウンロードする

まず、お使いのコンピューター上の任意のディレクトリに AzCopy V10 実行可能ファイルをダウンロードします。 利便性のため、AzCopy ディレクトリの場所をシステム パスに追加して使いやすくすることを検討してください。

- [Windows](https://aka.ms/downloadazcopy-v10-windows) (zip)
- [Linux](https://aka.ms/downloadazcopy-v10-linux) (tar)
- [MacOS](https://aka.ms/downloadazcopy-v10-mac) (zip)

> [!NOTE]
> [Azure Table Storage](https://docs.microsoft.com/azure/storage/tables/table-storage-overview) サービスとの間でデータをコピーする場合、[AzCopy バージョン 7.3](https://aka.ms/downloadazcopynet) をインストールしてください。

## <a name="run-azcopy"></a>AzCopy を実行する

コマンド プロンプトから、ファイルをダウンロードしたディレクトリに移動します。

コマンドの一覧を表示するには、「`azcopy -h`」と入力し、ENTER キーを押します。

特定のコマンドの情報を知るには、単にコマンドの名前を含めてください (例: `azcopy list -h`)。

![インライン ヘルプ](media/storage-use-azcopy-v10/azcopy-inline-help.png)

AzCopy を使用して意味のある動作を行う前に、ストレージ サービスに認証資格情報を提供する方法を決定する必要があります。

## <a name="choose-how-youll-provide-authorization-credentials"></a>認証資格情報の提供方法を選択する

認証資格情報は、Azure Active Directory (AD) または Shared Access Signature (SAS) トークンを使用して提供できます。

次の表をガイドとして使用してください。

| ストレージの種類 | 現在サポートされている認証方法 |
|--|--|
|**Blob Storage** | Azure AD および SAS |
|**BLOB ストレージ (階層的名前空間)** | Azure AD のみ |
|**File Storage** | SAS のみ |

### <a name="option-1-use-azure-ad"></a>オプション 1:Azure AD の使用

必要な認証レベルは、ファイルをアップロードする予定か、ファイルをダウンロードするだけなのかによって異なります。

#### <a name="authorization-to-upload-files"></a>ファイルをアップロードするための認証

以下のいずれかのロールがご自分の ID に割り当てられていることを確認します。

- [ストレージ BLOB データ共同作成者](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-queue-data-contributor)
- [ストレージ BLOB データ所有者](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner)

これらのロールは、以下のいずれかの範囲でご自分の ID に割り当てることができます。

- コンテナー (ファイル システム)
- ストレージ アカウント
- リソース グループ
- サブスクリプション

ロールを確認し、割り当てる方法については、「[Azure portal で RBAC を使用して Azure BLOB とキューのデータへのアクセスを付与する](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac-portal?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)」を参照してください。

ターゲット コンテナーまたはディレクトリのアクセス制御リスト (ACL) にお使いの ID が追加されている場合は、これらのロールのいずれかが ID に割り当てられている必要はありません。 ACL では、ID には、ターゲット ディレクトリの書き込みアクセス許可と、コンテナーおよび各親ディレクトリの実行アクセス許可が必要になります。

詳細については、[Azure Data Lake Storage Gen2 でのアクセス制御](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)に関するページを参照してください。

#### <a name="authorization-to-download-files"></a>ファイルをダウンロードするための認証

以下のいずれかのロールがご自分の ID に割り当てられていることを確認します。

- [ストレージ BLOB データ閲覧者](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-reader)
- [ストレージ BLOB データ共同作成者](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-queue-data-contributor)
- [ストレージ BLOB データ所有者](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner)

これらのロールは、以下のいずれかの範囲でご自分の ID に割り当てることができます。

- コンテナー (ファイル システム)
- ストレージ アカウント
- リソース グループ
- サブスクリプション

ロールを確認し、割り当てる方法については、「[Azure portal で RBAC を使用して Azure BLOB とキューのデータへのアクセスを付与する](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac-portal?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)」を参照してください。

ターゲット コンテナーまたはディレクトリのアクセス制御リスト (ACL) にお使いの ID が追加されている場合は、これらのロールのいずれかが ID に割り当てられている必要はありません。 ACL では、ID には、ターゲット ディレクトリの読み取りアクセス許可と、コンテナーおよび各親ディレクトリの実行アクセス許可が必要になります。

詳細については、[Azure Data Lake Storage Gen2 でのアクセス制御](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)に関するページを参照してください。

#### <a name="authenticate-your-identity"></a>ID の認証

ID に必要な認証レベルが与えられていることを確認したら、コマンド プロンプトを開き、次のコマンドを入力し、ENTER キーを押します。

```azcopy
azcopy login
```

このコマンドによって、認証コードと Web サイトの URL が返されます。 Web サイトを開き、コードを指定し、 **[次へ]** ボタンを選択します。

![コンテナーを作成する](media/storage-use-azcopy-v10/azcopy-login.png)

サインイン ウィンドウが表示されます。 そのウィンドウで、Azure アカウント資格情報を使用して、Azure アカウントにサインインします。 正常にサインインしたら、ブラウザー ウィンドウを閉じ、AzCopy の使用を開始できます。

### <a name="option-2-use-a-sas-token"></a>オプション 2:SAS トークンを使用する

AzCopy コマンドで使用する各コピー元または各コピー先の URL に SAS トークンを追加できます。

この例のコマンドでは、ローカル ディレクトリから BLOB コンテナーにデータが繰り返しコピーされます。 架空の SAS トークンがコンテナー URL の末尾に追加されます。

```azcopy
azcopy cp "C:\local\path" "https://account.blob.core.windows.net/mycontainer1/?sv=2018-03-28&ss=bjqt&srt=sco&sp=rwddgcup&se=2019-05-01T05:01:17Z&st=2019-04-30T21:01:17Z&spr=https&sig=MGCXiyEzbtttkr3ewJIh2AR8KrghSy1DGM9ovN734bQF4%3D" --recursive=true
```

SAS トークンの詳細とその取得方法については、「[Shared Access Signatures (SAS) の使用](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1)」を参照してください。

## <a name="transfer-files"></a>ファイルの転送

ID を認証し、SAS トークンを取得したら、ファイルの転送を開始できます。

サンプル コマンドは次の記事のいずれかをご覧ください。

- [AzCopy と Blob Storage でデータを転送する](storage-use-azcopy-blobs.md)

- [AzCopy とファイル ストレージでデータを転送する](storage-use-azcopy-files.md)

- [AzCopy と Amazon S3 バケットでデータを転送する](storage-use-azcopy-s3.md)

## <a name="configure-optimize-and-troubleshoot-azcopy"></a>AzCopy の構成、最適化、トラブルシューティング

「[AzCopy の構成、最適化、トラブルシューティング](storage-use-azcopy-configure.md)」を参照してください。

## <a name="use-azcopy-in-storage-explorer"></a>Storage Explorer で AzCopy を使用する

AzCopy のパフォーマンス上の利点を活用するとき、ファイルの操作に、コマンド ラインではなく Storage Explorer を使用する場合は、ストレージ エクスプローラーで AzCopy を有効にします。

Storage Explorer で、 **[プレビュー]** -> の **[Use AzCopy for Improved Blob Upload and Download]\(向上した Blob アップロードおよびダウンロードに AzCopy を使用する\)** に移動します。

![Azure Storage Explorer で転送エンジンとして AzCopy を有効にする](media/storage-use-azcopy-v10/enable-azcopy-storage-explorer.jpg)

> [!NOTE]
> ストレージ アカウントで階層型名前空間を有効にしてある場合、この設定を有効にする必要はありません。 ストレージ エクスプローラーでは、階層型名前空間が与えられているストレージ アカウントで AzCopy が自動的に使用されるためです。  

<a id="previous-version" />

## <a name="use-the-previous-version-of-azcopy"></a>以前のバージョンの AzCopy の使用

前のバージョンの AzCopy (AzCopy v8.1) を使用する必要がある場合は、次のいずれかのリンクを参照してください。

- [Windows での AzCopy (v8)](https://docs.microsoft.com/previous-versions/azure/storage/storage-use-azcopy)
- [Linux での AzCopy (v8)](https://docs.microsoft.com/previous-versions/azure/storage/storage-use-azcopy-linux)

## <a name="next-steps"></a>次の手順

ご質問、問題、一般的なフィードバックは、[GitHub](https://github.com/Azure/azure-storage-azcopy) ページからお送りください。
