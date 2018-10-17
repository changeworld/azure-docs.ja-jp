---
title: インクルード ファイル
description: インクルード ファイル
services: data-factory
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 10/01/2018
ms.author: jingwang
ms.custom: include file
ms.openlocfilehash: 4b209953e957d0c2892bc5c6bca7a577992c5dee
ms.sourcegitcommit: 6f59cdc679924e7bfa53c25f820d33be242cea28
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/05/2018
ms.locfileid: "48842961"
---
## <a name="prerequisites"></a>前提条件

### <a name="azure-subscription"></a>Azure サブスクリプション
Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/) を作成してください。

### <a name="azure-roles"></a>Azure ロール
Data Factory インスタンスを作成するには、Azure へのサインインに使用するユーザー アカウントが、"*共同作成者*" ロールまたは "*所有者*" ロールのメンバーであるか、Azure サブスクリプションの "*管理者*" である必要があります。 サブスクリプションで自分が持っているアクセス許可を表示するには、Azure portal で右上隅にあるユーザー名を選択し、**[アクセス許可]** を選択します。 複数のサブスクリプションにアクセスできる場合は、適切なサブスクリプションを選択します。 

データセット、リンクされたサービス、パイプライン、トリガー、および統合ランタイムを含む Data Factory の子リソースを作成および管理するには、次の要件が適用されます。
- Azure portal で子リソースを作成および管理するには、リソース グループ レベル以上で **Data Factory 共同作成者**ロールに属している必要があります。
- PowerShell または SDK を使用して子リソースを作成および管理する場合は、リソース レベル以上での**共同作成者**ロールで十分です。

ロールにユーザーを追加する方法に関するサンプル手順については、[ロールの追加](../articles/billing/billing-add-change-azure-subscription-administrator.md)に関する記事を参照してください。

詳細については、次の記事を参照してください。
- [Data Factory 共同作成者ロール](../articles/role-based-access-control/built-in-roles.md#data-factory-contributor)
- [Azure Data Factory のロールとアクセス許可](../articles/data-factory/concepts-roles-permissions.md)

### <a name="azure-storage-account"></a>Azure ストレージ アカウント
このクイックスタートでは、"*ソース*" データ ストアと "*コピー先*" データ ストアの両方に汎用の Azure ストレージ アカウント (具体的には BLOB ストレージ) を使用します。 汎用の Azure ストレージ アカウントがない場合、作成方法については、「[ストレージ アカウントの作成](../articles/storage/common/storage-quickstart-create-account.md)」を参照してください。 

#### <a name="get-the-storage-account-name-and-account-key"></a>ストレージ アカウント名とアカウント キーの取得
このクイック スタートには、Azure Storage アカウントの名前とキーが必要です。 以下の手順に従って、ご利用のストレージ アカウントの名前とキーを取得してください。 

1. Web ブラウザーで、[Azure Portal](https://portal.azure.com) に移動します。 Azure のユーザー名とパスワードを使用してサインインします。 
2. 左側のメニューの **[すべてのサービス]** を選択し、"**ストレージ**" というキーワードでフィルター処理して、**[ストレージ アカウント]** を選択します。

   ![ストレージ アカウントを検索](media/data-factory-quickstart-prerequisites/search-storage-account.png)
3. ストレージ アカウントの一覧で、ご利用のストレージ アカウントを (必要に応じて) フィルターで抽出し、該当するストレージ アカウントを選択します。 
4. **[ストレージ アカウント]** ページのメニューで **[アクセス キー]** を選択します。

   ![ストレージ アカウントの名前とキーを取得](media/data-factory-quickstart-prerequisites/storage-account-name-key.png)
5. **[ストレージ アカウント名]** ボックスと **[Key1]** ボックスの値をクリップボードにコピーします。 それらをメモ帳または他のエディターに貼り付けて保存します。 このクイックスタートの後の方で使用します。   

#### <a name="create-the-input-folder-and-files"></a>入力フォルダーとファイルの作成
このセクションでは、**adftutorial** という名前の BLOB コンテナーを Azure Blob Storage に作成します。 そのコンテナーに **input** という名前のフォルダーを作成し、input フォルダーにサンプル ファイルをアップロードします。 

1. **[ストレージ アカウント]** ページで **[概要]** に切り替え、**[BLOB]** を選択します。 

   ![BLOB オプションを選択する](media/data-factory-quickstart-prerequisites/select-blobs.png)
2. **[Blob service]** ページのツール バーで、**[+ コンテナー]** を選択します。 

   ![コンテナーの追加ボタン](media/data-factory-quickstart-prerequisites/add-container-button.png)    
3. **[新しいコンテナー]** ダイアログ ボックスで、名前に「**adftutorial**」と入力し、**[OK]** を選択します。 

   ![コンテナー名を入力する](media/data-factory-quickstart-prerequisites/new-container-dialog.png)
4. コンテナーの一覧で **[adftutorial]** を選択します。 

   ![コンテナーを選択する](media/data-factory-quickstart-prerequisites/seelct-adftutorial-container.png)
5. **[コンテナー]** ページのツール バーで、**[アップロード]** を選択します。  

   ![[アップロード] ボタン](media/data-factory-quickstart-prerequisites/upload-toolbar-button.png)
6. **[BLOB のアップロード]** ページで、**[詳細設定]** を選択します。

   ![[詳細設定] リンクの選択](media/data-factory-quickstart-prerequisites/upload-blob-advanced.png)
7. **メモ帳**を起動し、**emp.txt** という名前のファイルを作成して次の内容を入力します。 それを **c:\ADFv2QuickStartPSH** フォルダーに保存します。 まだ存在しない場合、**ADFv2QuickStartPSH** フォルダーを作成します。
    
   ```
   John, Doe
   Jane, Doe
   ```    
8. Azure Portal の **[BLOB のアップロード]** ページの **[ファイル]** ボックスで、**emp.txt** ファイルを探して選択します。 
9. **[アップロード先のフォルダー]** ボックスの値として、「**input**」と入力します。 

    ![BLOB のアップロードの設定](media/data-factory-quickstart-prerequisites/upload-blob-settings.png)    
10. フォルダーが **input** で、ファイルが **emp.txt** であることを確認し、**[アップロード]** を選択します。
    
    一覧に **emp.txt** ファイルとアップロードの状態が表示されます。 
12. 隅の **[X]** をクリックして、**[BLOB のアップロード]** ページを閉じます。 

    ![BLOB のアップロード ページを閉じる](media/data-factory-quickstart-prerequisites/close-upload-blob.png)
1. **[コンテナー]** ページを開いたままにしておきます。 このクイックスタートの最後で、このページを使用して出力を確認します。