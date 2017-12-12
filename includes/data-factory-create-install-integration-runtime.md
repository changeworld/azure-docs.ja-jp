## <a name="create-a-self-hosted-ir"></a>セルフホステッド IR を作成する

このセクションでは、セルフホステッド統合ランタイムを作成し、SQL Server データベースがあるオンプレミスのマシンに関連付けます。 セルフホステッド統合ランタイムは、マシンの SQL Server から Azure BLOB ストレージにデータをコピーするコンポーネントです。 

1. 統合ランタイムの名前に使用する変数を作成します。 一意の名前を使用し、その名前を書き留めます。 このチュートリアルの後の方で、それを使用します。 

    ```powershell
   $integrationRuntimeName = "ADFTutorialIR"
    ```
1. セルフホステッド統合ランタイムを作成します。 

   ```powershell
   Set-AzureRmDataFactoryV2IntegrationRuntime -Name $integrationRuntimeName -Type SelfHosted -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName
   ```

   出力例を次に示します。

   ```json
    Id                : /subscriptions/<subscription ID>/resourceGroups/ADFTutorialResourceGroup/providers/Microsoft.DataFactory/factories/onpremdf0914/integrationruntimes/myonpremirsp0914
    Type              : SelfHosted
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : onpremdf0914
    Name              : myonpremirsp0914
    Description       :
    ```
 

2. 次のコマンドを実行して、作成された統合ランタイムの状態を取得します。 **[状態]** プロパティの値が **[NeedRegistration]** に設定されていることを確認します。 

   ```powershell
   Get-AzureRmDataFactoryV2IntegrationRuntime -name $integrationRuntimeName -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Status
   ```

   出力例を次に示します。

   ```json
   Nodes                     : {}
   CreateTime                : 9/14/2017 10:01:21 AM
   InternalChannelEncryption :
   Version                   :
   Capabilities              : {}
   ScheduledUpdateDate       :
   UpdateDelayOffset         :
   LocalTimeZoneOffset       :
   AutoUpdate                :
   ServiceUrls               : {eu.frontend.clouddatahub.net, *.servicebus.windows.net}
   ResourceGroupName         : <ResourceGroup name>
   DataFactoryName           : <DataFactory name>
   Name                      : <Integration Runtime name>
   State                     : NeedRegistration
   ```

3. 次のコマンドを実行して、クラウドの Data Factory サービスにセルフホステッド統合ランタイムを登録するための**認証キー**を取得します。 

   ```powershell
   Get-AzureRmDataFactoryV2IntegrationRuntimeKey -Name $integrationRuntimeName -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName | ConvertTo-Json
   ```

   出力例を次に示します。

   ```json
   {
       "AuthKey1":  "IR@0000000000-0000-0000-0000-000000000000@xy0@xy@xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx=",
       "AuthKey2":  "IR@0000000000-0000-0000-0000-000000000000@xy0@xy@yyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyy="
   }
   ```    
4. 次の手順でマシンにインストールするセルフホステッド統合ランタイムを登録するために、いずれかのキーをコピーします (二重引用符は除外)。  

## <a name="install-integration-runtime"></a>統合ランタイムのインストール
1. 既にマシンに **Microsoft Integration Runtime** がインストールされている場合は、**[プログラムの追加と削除]** を使用してこれをアンインストールします。 
2. セルフホステッド統合ランタイムをローカルの Windows マシンに[ダウンロード](https://www.microsoft.com/download/details.aspx?id=39717)し、インストールを実行します。 
3. **[Welcome to Microsoft Integration Runtime Setup Wizard]\(Microsoft Integration Runtime セットアップ ウィザードへようこそ\)** で **[次へ]** をクリックします。  
4. **[使用許諾契約書]** ページで使用条件とライセンス契約に同意し、**[次へ]** をクリックします。 
5. **[インストール先フォルダー]** ページで **[次へ]** をクリックします。 
6. **[Ready to install Microsoft Integration Runtime]\(Microsoft Integration Runtime のインストール準備完了\)** で **[インストール]** をクリックします。 
7. 構成中のコンピューターが、使用されていないときはスリープ状態または休止状態に移行する旨の警告メッセージが表示された場合は、**[OK]** をクリックしてください。 
8. **[電源オプション]** ウィンドウが表示される場合は、それを閉じて、セットアップ ウィンドウに切り替えます。 
9. **[Completed the Microsoft Integration Runtime Setup Wizard]\(Microsoft Integration Runtime セットアップ ウィザードの完了\)** ページで **[完了]** をクリックします。
10. **[統合ランタイム (セルフホスト) の登録]** ページで、前のセクションで保存したキーを貼り付け、**[登録]** をクリックします。 

   ![統合ランタイムの登録](media/data-factory-create-install-integration-runtime/register-integration-runtime.png)
2. セルフホステッド統合ランタイムが正常に登録されると、次のメッセージが表示されます。

   ![正常に登録](media/data-factory-create-install-integration-runtime/registered-successfully.png)

3. **[新しい統合ランタイム (セルフホスト) ノード]** ページで **[次へ]** をクリックします。 

    ![[新しい統合ランタイム ノード] ページ](media/data-factory-create-install-integration-runtime/new-integration-runtime-node-page.png)
4. **[イントラネット通信チャネル]** で **[スキップ]** をクリックします。 複数ノードの統合ランタイム環境では、イントラノード通信に使用する TLS/SSL 証明書を選択することができます。 

    ![[イントラネット通信チャネル] ページ](media/data-factory-create-install-integration-runtime/intranet-communication-channel-page.png)
5. **[統合ランタイム (セルフホスト) の登録]** ページで **[構成マネージャーの起動]** をクリックします。 
6. ノードがクラウド サービスに接続されると、次のページが表示されます。

   ![ノード接続済み](media/data-factory-create-install-integration-runtime/node-is-connected.png)
7. ここで、SQL Server データベースへの接続をテストします。

    ![[診断] タブ](media/data-factory-create-install-integration-runtime/config-manager-diagnostics-tab.png)   

    - **[構成マネージャー]** ウィンドウで、**[診断]** タブに切り替えます。
    - **[データ ソースの種類]** として **[SqlServer]** を選択します。
    - **サーバー**名を入力します。
    - **データベース**名を入力します。 
    - **認証**モードを選択します。 
    - **ユーザー**名を入力します。 
    - そのユーザー名の**パスワード**を入力します。
    - **[テスト]** をクリックして、統合ランタイムから SQL Server に接続できることを確認します。 接続が成功すると、緑色のチェック マークが表示されます。 それ以外の場合は、失敗に関連したエラー メッセージが表示されます。 問題を修正し、統合ランタイムから SQL Server に接続できるようにします。    

    > [!NOTE]
    > これらの値 (認証の種類、サーバー、データベース、ユーザー、パスワード) を書き留めます。 このチュートリアルの後の方で使用します。 
    
