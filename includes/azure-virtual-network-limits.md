<table cellspacing="0" border="1">
<tr>
   <th align="left" valign="middle">リソース</th>
   <th align="left" valign="middle">既定の制限</th>
   <th align="left" valign="middle">上限</th>
</tr>
<tr>
   <td valign="middle"><p>サブスクリプションあたりの<a href="http://msdn.microsoft.com/library/azure/jj156007.aspx">仮想ネットワーク数</a><sup>1</sup></p></td>
   <td valign="middle"><p>10</p></td>
   <td valign="middle"><p>100</p></td>
</tr>
<tr>
   <td valign="middle"><p>仮想ネットワークあたりの合計マシン数<sup>2</sup></p></td>
   <td valign="middle"><p>2048</p></td>
   <td valign="middle"><p>2048</p></td>
</tr>
<tr>
   <td valign="middle"><p>仮想マシンまたはロール インスタンスの同時 TCP 接続数</p></td>
   <td valign="middle"><p>500K</p></td>
   <td valign="middle"><p>500K</p></td>
</tr>
<tr>
   <td valign="middle"><p>エンドポイントあたりのアクセス制御リスト (ACL) 数<sup>3</sup></p></td>
   <td valign="middle"><p>50</p></td>
   <td valign="middle"><p>50</p></td>
</tr>
<tr>
   <td valign="middle"><p>仮想ネットワークあたりのローカル ネットワーク サイト数</p></td>
   <td valign="middle"><p>10</p></td>
   <td valign="middle"><p>10</p></td>
</tr>
</table>

<sup>1</sup>それぞれの仮想ネットワークでサポートされる[仮想ネットワーク ゲートウェイ](http://msdn.microsoft.com/library/azure/jj156210.aspx)は 1 つだけです。

<sup>2</sup>合計マシン数には、Virtual Machines と Web/worker ロール インスタンスが含まれます。

<sup>3</sup>ACL は、Virtual Machines の入力エンドポイントでサポートされます。Web/Worker ロールの場合は、入力エンドポイントとインスタンスの入力エンドポイントでサポートされます。

<!---HONumber=62-->