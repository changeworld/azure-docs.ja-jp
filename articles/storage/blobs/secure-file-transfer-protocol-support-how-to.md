---
title: SFTP プロトコル (プレビュー) を使用して Azure Blob Storage に接続する | Microsoft Docs
description: SFTP クライアントを使用して Azure Storage アカウントに直接接続できるよう、Azure Blob Storage アカウントの SFTP サポートを有効にする方法について説明します。
author: normesta
ms.subservice: blobs
ms.service: storage
ms.topic: conceptual
ms.date: 11/15/2021
ms.author: normesta
ms.reviewer: ylunagaria
ms.openlocfilehash: b6094d86b8f0e2a75d41312b31d1f829d18601b5
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132720684"
---
# <a name="connect-to-azure-blob-storage-by-using-the-secure-file-transfer-sftp-protocol-preview"></a>セキュア ファイル転送 (SFTP) プロトコルを使用して Azure Blob Storage に接続する (プレビュー)

SFTP クライアントを使用して Azure Storage アカウントの BLOB Storage エンドポイントに安全に接続し、ファイルをアップロードおよびダウンロードできます。 この記事では、SFTP プロトコルのサポートを有効にしてから、SFTP クライアントを使用して Blob Storage に接続する方法について説明します。 

Azure Blob Storage での SFTP プロトコルのサポートの詳細については、「[Azure Blob Storage でのセキュア ファイル転送 (SFTP) プロトコルのサポート](secure-file-transfer-protocol-support.md)」を参照してください。

> [!IMPORTANT]
> SFTP プロトコルのサポートは現在プレビュー中であり、[これらのリージョン](secure-file-transfer-protocol-support.md#regional-availability)で使用できます。
>
> ベータ版、プレビュー版、または一般提供としてまだリリースされていない Azure の機能に適用される法律条項については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。
>
> プレビューに登録するには、[こちらのフォーム](https://forms.office.com/r/gZguN0j65Y)を参照してください。

## <a name="prerequisites"></a>前提条件

- 標準の汎用 v2 または Premium ブロック BLOB ストレージ アカウント。 これらのストレージ アカウントの種類について詳しくは、「[ストレージ アカウントの概要](../common/storage-account-overview.md)」を参照してください。

- ストレージ アカウントのアカウント冗長オプションは、ローカル冗長ストレージ (LRS) またはゾーン冗長ストレージ (ZRS) に設定されます。

- アカウントの階層型名前空間機能を有効にする必要があります。 階層型名前空間機能を有効にするには、「[Azure Data Lake Storage Gen2 機能を使用して Azure Blob Storage をアップグレードする](upgrade-to-data-lake-storage-gen2-how-to.md)」を参照してください。

- オンプレミスのネットワークから接続している場合は、クライアントがポート 22 を介した発信を許可していることを確認します。 SFTP プロトコルでは、そのポートが使用されます。

## <a name="register-the-feature"></a>機能を登録する

SFTP サポートを有効にする前に、サブスクリプションに SFTP 機能を登録する必要があります。

1. [Azure portal](https://portal.azure.com/) にサインインします。

2. サブスクリプションの構成ページを開きます。

3. **[設定]** で、 **[プレビュー機能]** を選択します。

   > [!div class="mx-imgBorder"]
   > ![[プレビュー設定]](./media/secure-file-transfer-protocol-support-how-to/preview-features-setting.png)

4. **[プレビュー機能]** ページで、**AllowSFTP** 機能を選択してから、 **[登録]** を選択します。

### <a name="verify-feature-registration"></a>機能の登録を確認する

この記事の他の手順を続ける前に、機能が登録されていることを確認してください。 

1. サブスクリプションの **[プレビュー機能]** ページを開きます。 

2. **AllowSFTP** 機能を見つけて、 **[登録済み]** が **[状態]** 列に表示されていることを確認します。

## <a name="enable-sftp-support"></a>SFTP サポートを有効にする

1. [Azure portal](https://portal.azure.com/) のストレージ アカウントに移動します。

2. **[設定]** で **[SFTP]** を選択します。

   > [!NOTE]
   > このオプションは、アカウントの階層型名前空間が有効になっている場合にのみ表示されます。 階層型名前空間機能を有効にするには、「[Azure Data Lake Storage Gen2 機能を使用して Azure Blob Storage をアップグレードする](upgrade-to-data-lake-storage-gen2-how-to.md)」を参照してください。

3. **[SFTP を有効にする]** を選択します。 

   > [!div class="mx-imgBorder"]
   > ![[SFTP を有効にする] ボタン](./media/secure-file-transfer-protocol-support-how-to/sftp-enable-option.png)

   >[!NOTE]
   > SFTP 構成ページにローカル ユーザーが表示されない場合は、少なくとも 1 人を追加する必要があります。 ローカル ユーザーを追加するには、次のセクションを参照してください。

## <a name="configure-permissions"></a>権限の構成

Azure Storage は、SFTP エンドポイントにアクセスするための共有アクセス署名 (SAS) または Azure Active Directory (Azure AD) 認証をサポートしていません。 代わりに、Azure で生成されたパスワードまたは Secure Shell (SSH) キー ペアで保護できるローカル ユーザーと呼ばれる ID を使用する必要があります。 接続しているクライアントへのアクセスを許可するには、ストレージ アカウントにパスワードまたはキーの組に関連付けられている ID が必要です。 その ID は *ローカル ユーザー* と呼ばれます。 

このセクションでは、ローカル ユーザーを作成し、認証方法を選択して、そのローカル ユーザーにアクセス許可を割り当てる方法を学習します。 

SFTP アクセス許可モデルの詳細については、「[SFTP アクセス許可モデル](secure-file-transfer-protocol-support.md#sftp-permissions-model)」を参照してください。 

1. [Azure portal](https://portal.azure.com/) のストレージ アカウントに移動します。

2. **[設定]** で、 **[SFTP]** 、 **[ローカル ユーザーの追加]** の順に選択します。 

   > [!div class="mx-imgBorder"]
   > ![[ローカル ユーザーの追加] ボタン](./media/secure-file-transfer-protocol-support-how-to/sftp-local-user.png)

3. **[ローカル ユーザーの追加]** ウィンドウで、ユーザーの名前を追加し、このローカル ユーザーに関連付ける認証方法を選択します。 パスワードおよび/または SSH キーを関連付けできます。 

   > [!IMPORTANT]
   > 両方の形式の認証を有効にできますが、SFTP クライアントはどちらか一方のみを使用して接続できます。 認証を成功させるために、有効なパスワードと有効な公開キーと秘密キーのペアの両方が必要な多要素認証はサポートされていません。

   **[パスワードを使用してセキュリティで保護する]** を選択した場合、 **[ローカル ユーザーの追加]** 構成ペインのすべての手順を完了すると、パスワードが表示されます。

   **[SSH 公開キーを使用してセキュリティで保護する]** を選択した場合は、 **[キー ソースの追加]** を選択してキー ソースを指定します。 

   > [!div class="mx-imgBorder"]
   > ![[ローカル ユーザーの構成] ウィンドウ](./media/secure-file-transfer-protocol-support-how-to/add-local-user-config-page.png)

   次の表では、各キー ソース オプションについて説明します。

   | オプション | ガイダンス |
   |----|----|
   | 新しいキーの組の生成 | 新しい公開キーと秘密キーの組を作成するには、このオプションを使用します。 公開キーは、指定したキー名を使用して Azure に格納されます。 秘密キーは、ローカル ユーザーが正常に追加された後でダウンロードできます。 |
   | Azure に格納されている既存のキーを使用する | Azure に既に格納されている公開キーを使用する場合は、このオプションを使用します。 Azure で既存のキーを検索するには、「[キーの一覧表示](../../virtual-machines/ssh-keys-portal.md#list-keys)」 を参照してください。 SFTP クライアントでは、Azure Blob Storage に接続する場合、それらのクライアントで、この公開キーに関連付けられている秘密キーを指定する必要があります。 |
   | 既存の公開キーを使用します | Azure の外部に格納されている公開キーをアップロードする場合は、このオプションを使用します。 公開キーを持ってはいないが、Azure の外部で公開キーを生成する場合は、「[ssh-keygen でキーを生成する](../../virtual-machines/linux/create-ssh-keys-detailed.md#generate-keys-with-ssh-keygen)」を参照してください。 |

4. **[次へ]** を選択して、構成ウィンドウの **[コンテナーのアクセス許可]** タブを開きます。

5. **[コンテナーのアクセス許可]** タブで、このローカル ユーザーが使用できるコンテナーを選択します。 次に、このローカル ユーザーが実行できる操作の種類を選択します。

   > [!div class="mx-imgBorder"]
   > ![[コンテナーのアクセス許可] タブ](./media/secure-file-transfer-protocol-support-how-to/container-perm-tab.png)

6. **[ホーム ディレクトリ]** 編集ボックスに、このローカル ユーザーに関連付けられている既定の場所になるコンテナーの名前またはディレクトリ パス (コンテナー名を含む) を入力します。 

   ホーム ディレクトリの詳細については、「[ホーム ディレクトリ](secure-file-transfer-protocol-support.md#home-directory)」を参照してください。

7. **[ボタンの追加]** を選択してローカル ユーザーを追加します。

   パスワード認証を有効にした場合、ローカル ユーザーが追加された後、Azure で生成されたパスワードがダイアログ ボックスに表示されます。 

   > [!IMPORTANT]
   > 後でこのパスワードを取得することはできません。そのため、必ずパスワードをコピーして、見つけられる場所に保管してください。

   新しいキーの組を生成することを選択した場合、ローカル ユーザーが追加された後、そのキーの組の秘密キーをダウンロードするように求められます。

## <a name="connect-an-sftp-client"></a>SFTP クライアントを接続する

任意の SFTP クライアントを使用して、ファイルを安全に接続してから転送できます。 次のスクリーンショットは、[Open SSH](/windows-server/administration/openssh/openssh_overview) とパスワード認証を使用して接続し、`logfile.txt` という名前のファイルをアップロードする Windows PowerShell セッションを示しています。  

> [!div class="mx-imgBorder"]
> ![Open SSH を使用した接続](./media/secure-file-transfer-protocol-support-how-to/ssh-connect-and-transfer.png)

> [!NOTE]
> ホスト キーを信頼するように求めるメッセージが表示される場合があります。 パブリック プレビュー中に、有効なホスト キーが[ここに](secure-file-transfer-protocol-host-keys.md)公開されます。  

転送が完了すると、Azure portal でファイルを表示および管理できます。 

> [!div class="mx-imgBorder"]
> ![アップロードされたファイルがストレージ アカウントに表示される](./media/secure-file-transfer-protocol-support-how-to/uploaded-file-in-storage-account.png)

> [!NOTE]
> Azure portal では、Blob REST API と Data Lake Storage Gen2 REST API を使用します。 Azure portal でアップロードされたファイルを操作できるということは、SFTP と REST 間の相互運用性を示しています。

ファイルを接続および転送する方法については、SFTP クライアントのドキュメントを参照してください。

## <a name="see-also"></a>関連項目

- [Azure Blob Storage でのセキュア ファイル転送 (SFTP) プロトコルのサポート](secure-file-transfer-protocol-support.md)
- [Azure Blob Storage でのセキュア ファイル転送 (SFTP) プロトコルのサポートに関する既知の問題](secure-file-transfer-protocol-known-issues.md)