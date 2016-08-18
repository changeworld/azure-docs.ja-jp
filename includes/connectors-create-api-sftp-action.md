トリガーを追加すると、それによって生成されるデータと関係ができます。次の手順に従って、 **[SFTP - extract folder (SFTP - 抽出フォルダー)]** アクションを追加します。定義された条件が満たされた場合、この操作によりファイルの内容が抽出されます。

この操作を設定するためには、次の情報を提供する必要があります。新しいファイルのプロパティの一部の情報として、トリガーによって生成されたデータを簡単に使用できることを確認します。

|SFTP - 抽出フォルダーのプロパティ|説明|
|---|---|
|Source archive file path (ソース アーカイブ ファイルのパス)|これは、展開されているファイルのパスです。以前のアクションからのトークンのいずれかを選択したり、ファイルのパスを検索する SFTP サーバーを参照できます。|
|Destination folder path (抽出先フォルダー パス)|これは、抽出したファイルを配置する場所のパスです。以前のアクションからのトークンのいずれかを抽出先パスとして選択したり、SFTP サーバーを参照したり、パスを選択できます。|
|Overwrite? (上書きを許可)|既存のファイルを上書きすべきかどうか、また抽出先フォルダー パスに抽出されたファイルと同じ名前のファイルがあるかどうかを示します。|

先に定義した条件が *「True」* と評価された場合、ファイルを抽出するアクションを追加します。

1. **[アクションの追加]** を選択します。  
![SFTP アクション条件イメージ 6](./media/connectors-create-api-sftp/condition-6.png)  
- **[SFTP - Extract folder (SFTP - 抽出フォルダー)]** アクションを選択します。  
![SFTP アクション条件イメージ 7](./media/connectors-create-api-sftp/condition-7.png)  
- **[Source archive file path (ソース アーカイブ ファイルのパス)]** を選択します。  
![SFTP アクション条件イメージ 9](./media/connectors-create-api-sftp/condition-9.png)  
- **[ファイル パス]** トークンを選択します。これは、ソースのアーカイブ ファイルのパスとしてトリガーがあるファイルのパスを使用することを示します。  
![SFTP アクション条件イメージ 10](./media/connectors-create-api-sftp/condition-10.png)  
- **[Destination folder path (抽出先フォルダー パス)]** を選択します。  
![SFTP アクション条件イメージ 11](./media/connectors-create-api-sftp/condition-11.png)  
- **[ファイル パス]** トークンを選択します。これは、抽出ファイルの抽出先パスとしてトリガーがあるファイルのパスを使用することを示します。
- **[Destination folder path (抽出先フォルダー パス)]** コントロールで、*「\\ExtractedFile」* と入力します。抽出先フォルダー パス コントロール内でファイルのパス トークンの直後に、これを行います。  
![SFTP アクション条件イメージ 12](./media/connectors-create-api-sftp/condition-12.png)  
- **[Overwrite? (上書きを許可)]* コントロールで、*「True」* と入力し、抽出したファイルと同じ名前がある場合、既存のファイルを上書きするかを示します。  
![SFTP アクション条件イメージ 13](./media/connectors-create-api-sftp/condition-13.png)  
- ワークフローへの変更を保存します。

<!---HONumber=AcomDC_0727_2016-->
