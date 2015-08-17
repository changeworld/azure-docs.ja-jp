<table cellspacing="0" border="1">
<tr>
   <th align="left" valign="middle">リソース</th>
   <th align="left" valign="middle">既定の制限</th>
</tr>
<tr>
   <td valign="middle"><p>データベース サイズ</p></td>
   <td valign="middle"><p>パフォーマンス レベルによって異なります <sup>1</sup></p></td>
</tr>
<tr>
   <td valign="middle"><p>ログイン</p></td>
   <td valign="middle"><p>パフォーマンス レベルによって異なります <sup>1</sup></p></td>
</tr>
<tr>
   <td valign="middle"><p>メモリ使用量</p></td>
   <td valign="middle"><p>16 MB のメモリ許可が 20 秒を超える</p></td>
</tr>
<tr>
   <td valign="middle"><p>セッション</p></td>
   <td valign="middle"><p>パフォーマンス レベルによって異なります <sup>1</sup></p></td>
</tr>
<tr>
   <td valign="middle"><p>Tempdb のサイズ</p></td>
   <td valign="middle"><p>5 GB</p></td>
</tr>
<tr>
   <td valign="middle"><p>トランザクションの実行時間</p></td>
   <td valign="middle"><p>24 時間<sup>2</sup></p></td>
</tr>
<tr>
   <td valign="middle"><p>トランザクションあたりのロック数</p></td>
   <td valign="middle"><p>100 万</p></td>
</tr>
<tr>
   <td valign="middle"><p>トランザクションあたりのサイズ</p></td>
   <td valign="middle"><p>2 GB</p></td>
</tr>
<tr>
   <td valign="middle"><p>合計ログ領域のうちトランザクションごとに使用される割合</p></td>
   <td valign="middle"><p>20%</p></td>
</tr>
<tr>
   <td valign="middle"><p>同時要求 (ワーカー スレッド) の最大数</p></td>
   <td valign="middle"><p>パフォーマンス レベルによって異なります <sup>1</sup></p></td>
</tr>
</table>

<sup>1</sup>SQL Database には、Basic、Standard、Premium などのパフォーマンス レベルがあります。また、Standard および Premium は複数のパフォーマンス レベルに分かれています。パフォーマンス レベルとサービス レベルごとの詳細な制限については、「[Azure SQL Database のサービス レベルとパフォーマンス レベル](https://msdn.microsoft.com/library/azure/dn741336.aspx)」を参照してください。

<sup>2</sup>基本システム タスクに必要なリソースをトランザクションがロックしている場合、最大実行時間は 20 秒間です。

<!---HONumber=August15_HO6-->