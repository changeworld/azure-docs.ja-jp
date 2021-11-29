---
title: Azure Blob Storage でのセキュア ファイル転送プロトコル (SFTP) サポート (プレビュー) | Microsoft Docs
description: BLOB ストレージでは、セキュア ファイル転送プロトコル (SFTP) がサポートされるようになりました。
author: normesta
ms.subservice: blobs
ms.service: storage
ms.topic: conceptual
ms.date: 11/15/2021
ms.custom: references_regions
ms.author: normesta
ms.reviewer: ylunagaria
ms.openlocfilehash: 83a0ffc2b12c884a4d5cba4147cd02365f6fde0a
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132720513"
---
# <a name="secure-file-transfer-protocol-sftp-support-for-azure-blob-storage-preview"></a>Azure Blob Storage でのセキュア ファイル転送プロトコル (SFTP) サポート (プレビュー)

BLOB ストレージでは、セキュア ファイル転送プロトコル (SFTP) がサポートされるようになりました。 このサポートにより、SFTP エンドポイント経由で Blob Storage アカウントに安全に接続する機能が提供され、ファイル アクセス、ファイル転送、ファイル管理のために SFTP を利用できます。  

> [!IMPORTANT]
> SFTP のサポートは、現在プレビュー段階であり、[これらのリージョン](secure-file-transfer-protocol-support.md#regional-availability)で使用できます。
>
> ベータ版、プレビュー版、または一般提供としてまだリリースされていない Azure の機能に適用される法律条項については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。
>
> プレビューに登録するには、[こちらのフォーム](https://forms.office.com/r/gZguN0j65Y)を参照してください。

Azure では、Azure Blob サービスの REST API、Azure SDK、および AzCopy などのツールを使用して、Blob Storage アカウントに安全にデータを転送できます。 ただし、レガシ ワークロードでは、多くの場合、SFTP などの従来のファイル転送プロトコルが使用されます。 カスタム アプリケーションを更新して REST API と Azure SDK を使用することもできますが、コードを大幅に変更する必要があります。

この機能がリリースされる前は、SFTP を使用してデータを Azure Blob Storage に転送する場合は、サード パーティ製品を購入するか、独自のソリューションを調整する必要がありました。 SFTP サーバーをホストするために Azure で仮想マシン (VM) を作成してから、データをストレージ アカウントに移動する方法を見つける必要があります。 

現在は、Azure Blob Storage の SFTP サポートにより、1 つの設定で Blob Storage アカウントの SFTP エンドポイントを有効にできます。 次に、認証用のローカル ユーザー ID を設定して、追加の作業を行うことなくデータを安全に転送できます。 

この記事では、Azure Blob Storage の SFTP サポートについて説明します。 ストレージ アカウントに対して SFTP を有効にする方法については、「[セキュア ファイル転送 (SFTP) プロトコルを使用して Azure Blob Storage に接続する (プレビュー)](secure-file-transfer-protocol-support-how-to.md)」を参照してください。

## <a name="sftp-and-the-hierarchical-namespace"></a>SFTP と階層型名前空間

SFTP のサポートでは、BLOB を階層型名前空間に編成する必要があります。 階層型名前空間を使用する機能は、Azure Data Lake Storage Gen2 によって導入されました。 これにより、コンピューター上のファイル システムを編成するのと同じ方法で、オブジェクト (ファイル) がディレクトリとサブディレクトリの階層に編成されます。 階層型名前空間は、スケールが線形で、データ容量もパフォーマンスも低下しません。 

階層型名前空間からさまざまなプロトコルが拡張されます。 SFTP は、これらの使用可能なプロトコルの 1 つです。

> [!div class="mx-imgBorder"]
> ![階層型名前空間](./media/secure-file-transfer-protocol-support/hierarchical-namespace-and-sftp-support.png)

## <a name="sftp-permissions-model"></a>SFTP アクセス許可モデル

Azure Storage は、SFTP クライアントに接続するための共有アクセス署名 (SAS) または Azure Active Directory (Azure AD) 認証をサポートしていません。 代わりに、SFTP クライアントは、パスワードまたは Secure Shell (SSH) 秘密キー資格情報のいずれかを使用する必要があります。

接続しているクライアントへのアクセスを許可するには、資格情報に関連付けられている ID がストレージ アカウントに必要です。 その ID はローカル ユーザーと呼ばれます。 ローカル ユーザーは、SFTP サポートで提供される新しい形式の ID 管理です。 1 つのストレージ アカウントに最大で 1,000 人のローカル ユーザーを追加できます。

アクセス許可を設定するには、ローカル ユーザーを作成し、認証方法を選択します。 次に、アカウント内の各コンテナーに対して、そのユーザーに付与するアクセス レベルを指定できます。
 
> [!NOTE]
> データが Azure Storage に取り込まれた後は、Azure のさまざまなストレージ セキュリティ設定を利用できます。 ロールベースのアクセス制御 (RBAC) やアクセス制御リストなどの承認メカニズムは、接続する側の SFTP クライアントを承認する手段としてはサポートされていませんが、これらは、Azure portal、Azure CLI、Azure PowerShell コマンド、AzCopy などの Azure ツールや、Azure SDK、Azure REST API によるアクセスを承認するために使用できます。 
> 
> 詳細については、「[Azure Data Lake Storage Gen2 のアクセス制御モデル](data-lake-storage-access-control-model.md)」を参照してください

## <a name="authentication-methods"></a>認証方法

接続している SFTP クライアントを認証するには、パスワードまたは Secure Shell (SSH) の公開キー/秘密キーの組を使用します。 両方の形式の認証を構成し、接続しているクライアントにどちらを使用するかを選択してもらうことができます。 ただし、認証を成功させるために、有効なパスワードと有効な公開キー/秘密キーのペアの両方が必要な多要素認証はサポートされていません。 

#### <a name="passwords"></a>パスワード

パスワードは自動的に生成されます。 パスワード認証を選択した場合、ローカル ユーザーの構成が完了すると、パスワードが提供されます。 必ずそのパスワードをコピーし、後で見つけられる場所に保存してください。 そのパスワードを Azure から再度取得することはできません。 パスワードを紛失した場合は、新しいパスワードを生成する必要があります。 セキュリティ上の理由から、自分でパスワードを設定することはできません。   

#### <a name="ssh-key-pairs"></a>SSH キーの組

公開キーと秘密キーのペアは、Secure Shell (SSH) の認証の最も一般的な形式です。 秘密キーはシークレットであり、ユーザーしか知りません。 公開キーは Azure に格納されます。 SSH クライアントは、ストレージ アカウントに接続するときに、秘密キーとシグネチャを含むメッセージを送信します。 Azure では、メッセージが検証され、そのユーザーとキーがストレージ アカウントによって認識されることが確認されます。 詳細については、[SSH とキーの概要](../../virtual-machines/linux/ssh-from-windows.md#)に関するページをご覧ください。

秘密キーと公開キーの組で認証することを選択した場合は、それを生成するか、Azure に既に保存されているものを使用するか、既存の公開キーとプライベート キーのペアの公開キーを Azure に提供することができます。 

## <a name="container-permissions"></a>コンテナーのアクセス許可

現在のリリースでは、コンテナー レベルのアクセス許可のみを指定できます。 ディレクトリ レベルのアクセス許可はサポートされていません。 アクセス権を付与するコンテナーと、付与するアクセス レベル (読み取り、書き込み、一覧表示、削除、作成) を選択できます。 これらのアクセス許可は、コンテナー内のすべてのディレクトリとサブディレクトリに適用されます。 各ローカル ユーザーには、最大 100 のコンテナーへのアクセス権を付与できます。 コンテナーのアクセス許可は、ローカル ユーザーの作成後にも更新できます。 次の表では、各アクセス許可について詳しく説明します。

| アクセス許可 | アクセス許可コード | 説明 |
|---|---|---|
| Read | r | <li>ファイルの内容を読み取る</li> |
| Write | 。 | <li>ファイルをアップロードする</li><li>ディレクトリの作成</li><li>ディレクトリをアップロードする</li> |
| List | l | <li>コンテナー内の内容を一覧表示する</li><li>ディレクトリ内の内容を一覧表示する</li> |
| 削除 | d | <li>ファイル/ディレクトリを削除する</li> |
| 作成 | c | <li>アップロード ファイルが存在しない場合は作成する</li><li>ディレクトリが存在しない場合は作成する</li><li>ディレクトリを作成する</li>|

## <a name="home-directory"></a>ホーム ディレクトリ

アクセス許可を構成する場合は、ローカル ユーザーのホーム ディレクトリを設定することができます。 SFTP 接続要求で他のコンテナーが指定されていない場合、これが、ユーザーが既定で接続するディレクトリになります。 たとえば、[Open SSH](/windows-server/administration/openssh/openssh_overview) を使用して次の要求を行ったとします。 この要求では、`sftp` コマンドの一部としてコンテナー名またはディレクトリ名が指定されていません。

```powershell
sftp myaccount.myusername@myaccount.blob.core.windows.net
put logfile.txt
```

ユーザーのホーム ディレクトリを `mycontainer/mydirectory` に設定すると、クライアントはそのディレクトリに接続します。 その後、`logfile.txt` ファイルは `mycontainer/mydirectory` にアップロードされます。 ホーム ディレクトリを設定しなかった場合、接続の試行は失敗します。 代わりに、クライアントを接続するには、要求と共にコンテナーを指定し、SFTP コマンドを使用して、ファイルをアップロードする前にターゲット ディレクトリに移動する必要があります。 次の例はこのことを示します。

```powershell
sftp myaccount.mycontainer.myusername@myaccount.blob.core.windows.net
cd mydirectory
put logfile.txt  
```

## <a name="known-issues-and-limitations"></a>既知の問題と制限事項

SFTP サポートの現在のリリースにおける問題と制限事項の一覧については、[既知の問題](secure-file-transfer-protocol-known-issues.md)に関する記事をご覧ください。

## <a name="regional-availability"></a>リージョン別の提供状況

SFTP サポートは、次のリージョンで使用できます。 

- 米国中北部
- 米国東部 2
- 米国東部 2 EUAP
- 米国中部 EUAP
- カナダ東部
- カナダ中部
- 北ヨーロッパ
- オーストラリア東部
- スイス北部
- ドイツ中西部
- 東アジア
- フランス中部

## <a name="pricing-and-billing"></a>価格と課金

> [!IMPORTANT]
> パブリック プレビュー期間中は、SFTP を使用しても追加料金は発生しません。 ただし、基になる Azure Data Lake Store Gen2 アカウントの標準のトランザクション、ストレージ、およびネットワークの価格は引き続き適用されます。 この機能が一般公開されると、SFTP で追加料金が発生する可能性があります。  

トランザクションとストレージのコストは、ストレージ アカウントの種類や、ストレージ アカウントへのデータ転送に使用するエンドポイントなどの要因に基づいています。 詳細については、「[Azure Blob Storage の詳細な課金モデルを理解する](../common/storage-plan-manage-costs.md#understand-the-full-billing-model-for-azure-blob-storage)」を参照してください。

## <a name="see-also"></a>関連項目

- [セキュア ファイル転送 (SFTP) プロトコルを使用して Azure Blob Storage に接続する (プレビュー)](secure-file-transfer-protocol-support-how-to.md)
- [Azure Blob Storage でのセキュア ファイル転送 (SFTP) プロトコルのサポートに関する既知の問題 (プレビュー)](secure-file-transfer-protocol-known-issues.md)