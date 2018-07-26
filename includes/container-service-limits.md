| Resource | 既定の制限 |
| --- | :--- |
| クラスターごとの最大ノード数 | 100 |
| ノードごとの最大ポッド数 ([Kubenet を使用した基本的なネットワーク][basic-networking]) | 110 |
| ノードごとの最大ポッド数 ([Azure CNI を使用した高度なネットワーク][advanced-networking]) | 30<sup>1</sup> |
| サブスクリプションあたりの最大クラスター数 | 100 |

<sup>1</sup> この値は、ARM テンプレートのデプロイによってカスタマイズすることができます。 [こちら][arm-deployment-example]の例を参照してください。<br />

<!-- LINKS - Internal -->
[basic-networking]: ../articles/aks/networking-overview.md#basic-networking
[advanced-networking]: ../articles/aks/networking-overview.md#advanced-networking

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
[arm-deployment-example]: https://github.com/Azure/AKS/blob/master/examples/vnet/02-aks-custom-vnet.json#L64-L69
