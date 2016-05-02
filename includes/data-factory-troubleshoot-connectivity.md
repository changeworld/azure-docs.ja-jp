## 接続の問題のトラブルシューティング
接続の問題をトラブルシューティングするには、**Data Management Gateway 構成マネージャー**の **[診断]** タブを使用します。

1. **Data Management Gateway 構成マネージャー**を起動します。"C:\\Program Files\\Microsoft Data Management Gateway\\1.0\\Shared\\ConfigManager.exe" を直接実行することも、次の図で示されているように、**Gateway** を検索し、**Microsoft Data Management Gateway** アプリケーションへのリンクを見つけることもできます。 

	![Search gateway](./media/data-factory-troubleshoot-connectivity/search-gateway.png)
2. **[診断]** タブに切り替えます。

	![Gateway diagnostics](./media/data-factory-troubleshoot-connectivity/data-factory-gateway-diagnostics.png) 
3. データ ストアの**種類** (リンクされたサービス) を選択します。 
4. **[認証]** を指定し、**資格情報**を入力します。または、**接続文字列**を入力して、データ ストアに接続します。 
5. **[接続テスト]** をクリックして、データ ストアへの接続をテストします。 

<!---HONumber=AcomDC_0420_2016-->