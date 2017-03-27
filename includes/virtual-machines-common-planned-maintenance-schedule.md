

## <a name="multi-and-single-instance-vms"></a>複数インスタンス VM と単一インスタンス VM
Azure を使用するユーザーの多くにとって、VM の計画メンテナンスをスケジュールできることは、重要なことです。計画メンテナンスでは最大 15 分のダウンタイムが発生します。 可用性セットを利用すると、プロビジョニングされた VM の計画メンテナンスがいつ行われるかを制御できます。

Azure で実行される VM には 2 つの可能な構成があります。 VM は、複数インスタンスまたは単一インスタンスとして構成されます。 可用性セット内にある VM は、複数インスタンスとして構成されます。 ただし、単一 VM を可用性セットにデプロイすることもでき、そのような VM は複数インスタンスとして扱われます。 可用性セット内にない VM は、単一インスタンスとして構成されます。  可用性セットの詳細については、[Windows 仮想マシンの可用性管理](../articles/virtual-machines/virtual-machines-windows-manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)または [Linux 仮想マシンの可用性管理](../articles/virtual-machines/virtual-machines-linux-manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)に関するページを参照してください。

単一インスタンス VM と複数インスタンス VM に対する計画的メンテナンスの更新は、個別に行われます。 VM を単一インスタンス (複数インスタンスの場合) または複数インスタンス (単一インスタンスの場合) として再構成することにより、VM の計画メンテナンスが行われるタイミングを制御できます。 Azure VM の計画的メンテナンスの詳細については、「[Azure での Linux 仮想マシンに対する計画メンテナンス](../articles/virtual-machines/virtual-machines-linux-planned-maintenance.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)」または「[Azure での仮想マシンに対する計画メンテナンス](../articles/virtual-machines/virtual-machines-windows-planned-maintenance.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)」を参照してください。

## <a name="for-multi-instance-configuration"></a>複数インスタンス構成の場合
可用性セットから VM を削除することで、可用性セット構成にデプロイされた VM に対していつ計画メンテナンスが行われるかを選択できます。

1. 複数インスタンス構成の VM に対する計画メンテナンスの 7 日前に、電子メールがユーザーに送信されます。 電子メールの本文には、サブスクリプション ID と、影響を受ける複数インスタンス VM の名前が記載されています。
2. この 7 日の間に、そのリージョン内の複数インスタンス VM を可用性セットから削除することにより、インスタンスが更新される時間を選択できます。 この構成変更により、仮想マシンがメンテナンス対象の物理ホストからメンテナンス対象ではない別の物理ホストに移動するので、再起動が行われます。 
3. 可用性セットからの VM の削除はクラシック ポータルで行うことができます。 
   
   1. クラシック ポータルで、VM をクリックし、[構成] を選択します。 
   2. [設定] で、VM がどの可用性セットの中にあるかを確認できます。
      
      ![可用性セットの選択](./media/virtual-machines-planned-maintenance-schedule/availabilitysetselection.png)
   3. [可用性セット] にドロップダウン メニューから [可用性セットから削除] を選択します。
      
      ![セットからの削除](./media/virtual-machines-planned-maintenance-schedule/availabilitysetselectionconfiguration.png)
   4. 下部にある [保存] を選択します。 [はい] を選択して、この操作によって VM が再起動されることを承認します。
4. これらの VM は単一インスタンス ホストに移動し、可用性セット構成の計画的メンテナンスでは更新されません。
5. 可用性セット VM の更新が (元の電子メールに記載されているスケジュールに従って) 完了した後で、可用性セットに VM を戻す必要があります。戻した VM は、複数インスタンス VM として再構成されます。 VM を単一インスタンスから複数インスタンスに戻すと、再起動が行われます。 通常、Azure 環境全体のすべての複数インスタンスの更新が完了した後で、単一インスタンスのメンテナンスが行われます。

これも Azure PowerShell を使用して行うことができます。

```
Get-AzureVM -ServiceName "<VmCloudServiceName>" -Name "<VmName>" | Remove-AzureAvailabilitySet | Update-AzureVM
```

## <a name="for-single-instance-configuration"></a>単一インスタンス構成の場合
VM を可用性セットに追加することで、単一インスタンス構成の VM に対する計画メンテナンスのタイミングを選択できます。

詳細な手順:

1. 単一インスタンス構成の VM に対する計画メンテナンスの 7 日前に、電子メールがユーザーに送信されます。 電子メールの本文には、サブスクリプション ID と、影響を受ける単一インスタンス VM の名前が記載されています。 
2. この 7 日の間に、単一インスタンス VM を同じリージョンの可用性セットに移動することにより、インスタンスが再起動する時間を選択できます。 この構成変更により、仮想マシンがメンテナンス対象の物理ホストからメンテナンス対象ではない別の物理ホストに移動するので、再起動が行われます。
3. クラシック ポータルと Azure PowerShell を使用し、ここで示す手順に従って、既存の VM を可用性セットに追加します (後の Azure PowerShell サンプルを参照)。
4. 複数インスタンスとして再構成した VM は、単一インスタンス VM の計画メンテナンスから除外されます。
5. 単一インスタンス VM の更新が (元の電子メールに記載されているスケジュールに従って) 完了したら、可用性セットから VM を削除できます。削除した VM は、単一インスタンス VM として再構成されます。

これも Azure PowerShell を使用して行うことができます。

    Get-AzureVM -ServiceName "<VmCloudServiceName>" -Name "<VmName>" | Set-AzureAvailabilitySet -AvailabilitySetName "<AvSetName>" | Update-AzureVM

<!--Anchors-->



<!--Link references-->
[仮想マシンの可用性管理]: virtual-machines-windows-tutorial.md
[Understand planned versus unplanned maintenance (計画メンテナンスと計画外メンテナンスの概要) (計画メンテナンスと計画外メンテナンスの概要)]: virtual-machines-manage-availability.md#Understand-planned-versus-unplanned-maintenance/


<!--HONumber=Nov16_HO3-->


