使用して、接続が成功したことを確認することができます、 [az ネットワークの vpn 接続の表示](/cli/azure/network/vpn-connection#show)コマンド。 例では、'--名 ' のテストを実行する接続の名前を示します。 接続が確立されている処理中に、接続状態は '接続' を示します。 接続が確立されると、その状態は '接続' に変わります。

```azurecli
az network vpn-connection show --name VNet1toSite2 --resource-group TestRG1
```

