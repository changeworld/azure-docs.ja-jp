<table cellspacing="0" border="1">
<tr>
   <th align="left" valign="middle">リソース</th>
   <th align="left" valign="middle">既定の制限</th>
   <th align="left" valign="middle">上限</th>
</tr>
<tr>
   <td valign="middle"><p><a href="http://azure.microsoft.com/documentation/articles/cloud-services-what-is/">デプロイあたりの Web/worker ロール数<sup>1</sup></a></p></td>
   <td valign="middle"><p>25</p></td>
   <td valign="middle"><p>25</p></td>
</tr>
<tr>
   <td valign="middle"><p>デプロイメントあたりの<a href="http://msdn.microsoft.com/library/gg557552.aspx#InstanceInputEndpoint">インスタンスの入力エンドポイント</a>数</p></td>
   <td valign="middle"><p>25</p></td>
   <td valign="middle"><p>25</p></td>
</tr>
<tr>
   <td valign="middle"><p>デプロイメントあたりの<a href="http://msdn.microsoft.com/library/gg557552.aspx#InputEndpoint">入力エンドポイント</a>数</p></td>
   <td valign="middle"><p>25</p></td>
   <td valign="middle"><p>25</p></td>
</tr>
<tr>
   <td valign="middle"><p>デプロイメントあたりの<a href="http://msdn.microsoft.com/library/gg557552.aspx#InternalEndpoint">内部エンドポイント</a>数</p></td>
   <td valign="middle"><p>25</p></td>
   <td valign="middle"><p>25</p></td>
</tr>
</table>

<sup>1</sup>Web/worker ロールの Cloud Service には、それぞれ運用環境用とステージング環境用の 2 つのデプロイを作成することができます。この制限は、ロールあたりのインスタンス数 (スケール) ではなく、別個のロール (構成) の数を指してることに注意してください。

<!---HONumber=August15_HO6-->