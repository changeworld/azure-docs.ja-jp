1. ログオン、 [Azure ポータル][Azure portal]です。
2. ポータルの左側のナビゲーション ウィンドウでをクリックして**新規**をクリックし、**エンタープライズ統合**、順にクリック**リレー**です。
3. **名前空間の作成**ダイアログ ボックスで、名前空間の名前を入力します。 システムは、名前が使用可能なかどうかをすぐに確認します。
4. **サブスクリプション**フィールドで名前空間を作成するための Azure サブスクリプションを選択します。
5. **[リソース グループ](../articles/azure-resource-manager/resource-group-portal.md)**フィールドに、既存のリソース グループの名前空間は live、または作成する新しい 1 つを選択します。      
6. **場所**、国または名前空間をホストする地域を選択します。
   
    ![名前空間を作成します。][create-namespace]
7. **[作成]** をクリックします。 システムはこれで、名前空間を作成し、により有効にします。 数分後に、後に、システムは、アカウントのリソースをプロビジョニングします。

### <a name="obtain-the-management-credentials"></a>管理資格情報を取得します。
1. 名前空間の一覧で、新しく作成された名前空間名をクリックします。
2. 名前空間ブレードで、をクリックして**共有アクセス ポリシー**です。
3. **共有アクセス ポリシー**ブレードで、をクリックして**RootManageSharedAccessKey**です。
   
    ![接続情報][connection-info]
4. **ポリシー: RootManageSharedAccessKey**ブレードで、コピーするには、次へのボタンをクリック**接続文字列: プライマリ キー**、接続文字列をクリップボードにコピーして、後で使用します。 この値をメモ帳や他の一時的な場所に貼り付けます。
   
    ![接続文字列][connection-string]

5. コピーと貼り付けの値は、この手順を繰り返します**主キー**後で使用できる一時的な場所にします。  

<!--Image references-->

[create-namespace]: ./media/relay-create-namespace-portal/create-namespace.png
[connection-info]: ./media/relay-create-namespace-portal/connection-info.png
[connection-string]: ./media/relay-create-namespace-portal/connection-string.png
[Azure portal]: https://portal.azure.com
