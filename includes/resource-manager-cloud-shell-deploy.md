## <a name="deploy-template-from-cloud-shell"></a>Cloud Shell からのテンプレートのデプロイ

テンプレートは、[Cloud Shell](../articles/cloud-shell/overview.md) を使ってデプロイすることができます。 ただし、最初に Cloud Shell のファイル共有にテンプレートを読み込む必要があります。 Cloud Shell の使用経験がない場合は、その設定について [Azure Cloud Shell の概要](../articles/cloud-shell/overview.md)に関するページを参照してください。

1. [Azure Portal](https://portal.azure.com) にログインします。

1. Cloud Shell リソース グループを選択します。 名前のパターンは `cloud-shell-storage-<region>` です。

   ![リソース グループの選択](./media/resource-manager-cloud-shell-deploy/select-cs-resource-group.png)

1. Cloud Shell のストレージ アカウントを選択します。

   ![ストレージ アカウントを選択する](./media/resource-manager-cloud-shell-deploy/select-storage.png)

1. **[ファイル]** を選択します。

   ![Select files](./media/resource-manager-cloud-shell-deploy/select-files.png)

1. Cloud Shell のファイル共有を選択します。 名前のパターンは `cs-<user>-<domain>-com-<uniqueGuid>` です。

   ![ファイル共有を選択する](./media/resource-manager-cloud-shell-deploy/select-file-share.png)

1. **[ディレクトリの追加]** を選択します。

   ![[ディレクトリの追加]](./media/resource-manager-cloud-shell-deploy/select-add-directory.png)

1. **templates** という名前を付け、**[OK]** を選択します。

   ![ディレクトリに名前を付ける](./media/resource-manager-cloud-shell-deploy/name-templates.png)

1. 新しいディレクトリを選択します。

   ![新しいディレクトリを選択する](./media/resource-manager-cloud-shell-deploy/select-templates.png)

1. **[アップロード]**を選択します。

   ![[アップロード] を選択する](./media/resource-manager-cloud-shell-deploy/select-upload.png)

1. テンプレートを見つけてアップロードします。

   ![ファイルをアップロードする](./media/resource-manager-cloud-shell-deploy/upload-files.png)

1. プロンプトを開きます。

   ![Cloud Shell を開く](./media/resource-manager-cloud-shell-deploy/start-cloud-shell.png)
