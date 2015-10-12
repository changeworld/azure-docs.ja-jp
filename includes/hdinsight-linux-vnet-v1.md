> Linux ベースの HDInsight で v1 (クラシック) Azure Virtual Network を使用することはできません。Azure プレビュー ポータルでの HDInsight クラスターの作成プロセスで Virtual Network をオプションとして表示したり、Azure CLI または Azure PowerShell からクラスターを作成するときに Virtual Network を使用できるようにしたりするには、Virtual Network が v2 (Azure リソース マネージャー) である必要があります。
> 
> v1 ネットワーク上にリソースがあり、HDInsight から仮想ネットワークを介してそれらのリソースに直接アクセスできるようにする場合、v2 Virtual Network を v1 Virtual Network に接続する方法については、「[従来の VNet を新しい VNet に接続する](../virtual-network/virtual-networks-arm-asm-s2s.md)」をご覧ください。この接続が確立されると、v2 Virtual Network で HDInsight クラスターを作成できます。

<!---HONumber=Oct15_HO1-->