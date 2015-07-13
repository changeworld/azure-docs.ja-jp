<table cellspacing="0" border="1">
<tr>
   <th align="left" valign="middle">リソース</th>
   <th align="left" valign="middle">既定の制限</th>
   <th align="left" valign="middle">上限</th>
</tr>
<tr>
   <td valign="middle"><p>クラウド サービスあたりの<a href="http://azure.microsoft.com/documentation/services/virtual-machines/">仮想マシン</a>数<sup>1</sup></p></td>
   <td valign="middle"><p>50</p></td>
   <td valign="middle"><p>50</p></td>
</tr>
<tr>
   <td valign="middle"><p>クラウド サービスあたりの入力エンドポイント数<sup>2</sup></p></td>
   <td valign="middle"><p>150</p></td>
   <td valign="middle"><p>150</p></td>
</tr>
</table>

<sup>1</sup>Azure リソース グループの外部で仮想マシンを作成すると、そのマシンをホストするクラウド サービスが自動的に作成されます。その同じ Cloud Service に、複数の仮想マシンを追加することができます。

<sup>2</sup>仮想マシンは、そのホストとなるクラウド サービスの外にあるため、仮想マシンとの通信は、入力エンドポイントを使用して行われます。同じクラウド サービス内の仮想マシンでは、すべての UDP ポートおよび TCP ポート間での通信が内部通信用に自動的に許可されます。

<!---HONumber=62-->