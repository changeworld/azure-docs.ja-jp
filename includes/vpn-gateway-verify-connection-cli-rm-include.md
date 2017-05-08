接続に成功したことを確認するには、次の CLI コマンドを使用します。 実際の値と一致するように値を構成します。 この例の  -n は、テストする作成済みの接続の名前を示します。

```azurecli
az network vpn-connection show -n VNet1toSite2 -g TestRG1
```

接続がまだ確立中の場合は、接続状態は "'Connecting'" と表示されます。 接続が確立されると、状態が "Connected" に変更され、受信バイトと送信バイトを確認できます。