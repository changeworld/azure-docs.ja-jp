接続に成功したことを確認するには、[az network vpn-connection show](/cli/azure/network/vpn-connection#show) コマンドを使用します。 この例では、テストする接続の名前が "--name" で示されています。 接続が確立中の場合は、接続状態は "Connecting" と表示されます。 接続が確立されると、状態は "Connected" に変更されます。

```azurecli
az network vpn-connection show --name VNet1toSite2 --resource-group TestRG1
```

