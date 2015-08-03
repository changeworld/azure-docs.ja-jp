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

<sup>1</sup>サービス管理 (リソース マネージャーではなく) で作成された仮想マシンは自動的にクラウド サービスに格納されます。より多くの仮想マシンをそのクラウド サービスに追加することで、負荷分散と可用性を実現できます。「[仮想マシンを仮想ネットワークまたはクラウド サービスと接続する方法](../virtual-machines/cloud-services-connect-virtual-machine.md)」を参照してください。

<sup>2</sup>入力エンドポイントは、仮想マシンのクラウド サービスの外部から仮想マシンへの通信を可能にします。同じクラウド サービスまたは仮想ネットワーク内の仮想マシンは自動的に相互に通信できます。「[仮想マシンに対してエンドポイントを設定する方法](../virtual-machines/virtual-machines-set-up-endpoints.md)」を参照してください。

<!---HONumber=July15_HO4-->