接続に成功したことを確認するには、[az network vpn-connection show](/cli/azure/network/vpn-connection#show) コマンドを使用します。 実際の値と一致するように値を構成します。 この例では、作成が終わっており、これからテストする接続の名前を "--name" で示しています。

```azurecli
az network vpn-connection show --name VNet1toSite2 --resource-group TestRG1
```

接続がまだ確立中の場合は、接続状態は "'Connecting'" と表示されます。 接続が確立されると、状態が "Connected" に変更され、受信バイトと送信バイトを確認できます。