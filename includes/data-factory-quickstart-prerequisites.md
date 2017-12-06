## <a name="prerequisites"></a>前提条件

### <a name="azure-subscription"></a>Azure サブスクリプション
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料](https://azure.microsoft.com/free/)アカウントを作成してください。

### <a name="azure-roles"></a>Azure ロール
Data Factory インスタンスを作成するには、Azure へのログインに使用するユーザー アカウントが、**共同作成者**または**所有者**ロールのメンバーであるか、Azure サブスクリプションの**管理者**である必要があります。 サブスクリプションで自分が持っているアクセス許可を表示するには、Azure Portal で右上隅にある**ユーザー名**をクリックし、**[アクセス許可]** を選択します。 複数のサブスクリプションへのアクセス権がある場合は、適切なサブスクリプションを選択します。 ロールにユーザーを追加するサンプル手順については、[ロールの追加](../articles/billing/billing-add-change-azure-subscription-administrator.md)に関する記事を参照してください。

### <a name="azure-storage-account"></a>Azure Storage アカウント
このクイックスタートでは、**ソース** データ ストアと**コピー先**データ ストアの両方に汎用の Azure Storage アカウント (具体的には Blob Storage) を使用します。 汎用の Azure Storage アカウントがない場合、作成方法については、「[ストレージ アカウントの作成](../articles/storage/common/storage-create-storage-account.md#create-a-storage-account)」をご覧ください。 

#### <a name="get-storage-account-name-and-account-key"></a>ストレージ アカウント名とアカウント キーの取得
このクイックスタートでは、Azure Storage アカウントの名前とキーを使用します。 以下の手順に従って、ご利用のストレージ アカウントの名前とキーを取得してください。 

1. Web ブラウザーを起動して [Azure Portal](https://portal.azure.com) にアクセスします。 Azure のユーザー名とパスワードを使用してログインします。 
2. 左側のメニューの **[その他のサービス >]** をクリックし、"**ストレージ**" というキーワードでフィルター処理して、**[ストレージ アカウント]** を選択します。

    ![ストレージ アカウントを検索](media/data-factory-quickstart-prerequisites/search-storage-account.png)
3. ストレージ アカウントの一覧で、ご利用のストレージ アカウントを (必要に応じて) フィルターで抽出し、**該当するストレージ アカウント**を選択します。 
4. **[ストレージ アカウント]** ページのメニューから **[アクセス キー]** を選択します。

    ![ストレージ アカウントの名前とキーを取得](media/data-factory-quickstart-prerequisites/storage-account-name-key.png)
5. **[ストレージ アカウント名]** フィールドと **[Key1]** フィールドの値をクリップボードにコピーします。 それらをメモ帳または他のエディターに貼り付けて保存します。 このクイックスタートの後の方で使用します。   

#### <a name="create-input-folder-and-files"></a>入力フォルダーとファイルの作成
このセクションでは、**adftutorial** という名前の BLOB コンテナーを Azure BLOB ストレージに作成します。 次に、そのコンテナーに **input** という名前のフォルダーを作成し、input フォルダーにサンプル ファイルをアップロードします。 

1. **[ストレージ アカウント]** ページで **[概要]** に切り替え、**[BLOB]** をクリックします。 

    ![BLOB オプションを選択する](media/data-factory-quickstart-prerequisites/select-blobs.png)
2. **[Blob service]** ページのツール バーで、**[+ コンテナー]** をクリックします。 

    ![コンテナーの追加ボタン](media/data-factory-quickstart-prerequisites/add-container-button.png)    
3. **[新しいコンテナー]** ダイアログ ボックスで、名前に「**adftutorial**」と入力し、**[OK]** をクリックします。 

    ![コンテナー名を入力する](media/data-factory-quickstart-prerequisites/new-container-dialog.png)
4. コンテナーの一覧で、**[adftutorial]** をクリックします。 

    ![コンテナーを選択する](media/data-factory-quickstart-prerequisites/seelct-adftutorial-container.png)
1. **[コンテナー]** ページのツール バーで、**[アップロード]** をクリックします。  

    ![[アップロード] ボタン](media/data-factory-quickstart-prerequisites/upload-toolbar-button.png)
6. **[BLOB のアップロード]** ページで、**[詳細設定]** をクリックします。

    ![詳細設定リンクをクリックする](media/data-factory-quickstart-prerequisites/upload-blob-advanced.png)
7. **メモ帳**を開き、以下の内容を含む **emp.txt** という名前のファイルを作成します。それを **c:\ADFv2QuickStartPSH** フォルダーに保存します。**ADFv2QuickStartPSH** フォルダーがない場合は作成します。
    
    ```
    John, Doe
    Jane, Doe
    ```    
8. Azure Portal の **[BLOB のアップロード]** ページの **[ファイル]** フィールドで、**emp.txt** ファイルを探して選択します。 
9. **[アップロード先のフォルダー]** フィールドの値として、「**input**」と入力します。 

    ![BLOB のアップロードの設定](media/data-factory-quickstart-prerequisites/upload-blob-settings.png)    
10. フォルダーが **input** で、ファイルが **emp.txt** であることを確認し、**[アップロード]** をクリックします。
11. 一覧に **emp.txt** ファイルとアップロードの状態が表示されます。 
12. 隅の **[X]** をクリックして、**[BLOB のアップロード]** ページを閉じます。 

    ![BLOB のアップロード ページを閉じる](media/data-factory-quickstart-prerequisites/close-upload-blob.png)
1. **[コンテナー]** ページを開いたままにしておきます。 このクイックスタートの最後で、このページを使用して出力を確認します。