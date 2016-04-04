

## 複数インスタンス VM と単一インスタンス VM
Azure を使用するユーザーの多くにとって、VM の計画的メンテナンスをスケジュールできることは、重要なことです。計画的メンテナンスでは最大 15 分のダウンタイムが発生します。可用性セットを利用すると、プロビジョニングされた VM の計画的メンテナンスがいつ行われるかを制御できます。

Azure で実行される VM には 2 つの可能な構成があります。VM は、複数インスタンスまたは単一インスタンスとして構成されます。可用性セット内にある VM は、複数インスタンスとして構成されます。ただし、単一 VM を可用性セットにデプロイすることもでき、そのような VM は複数インスタンスとして扱われます。可用性セット内にない VM は、単一インスタンスとして構成されます。可用性セットの詳細については、「[Manage the availability of Windows virtual machines (Windows 仮想マシンの可用性を管理する)](../articles/virtual-machines/virtual-machines-windows-manage-availability.md)」または「[Manage the availability of Linux virtual machines (Linux 仮想マシンの可用性を管理する)](../articles/virtual-machines/virtual-machines-linux-manage-availability.md)」を参照してください。

単一インスタンス VM と複数インスタンス VM に対する計画的メンテナンスの更新は、個別に行われます。VM を単一インスタンス (複数インスタンスの場合) または複数インスタンス (単一インスタンスの場合) として再構成することにより、VM の計画的メンテナンスが行われるタイミングを制御できます。Azure VM の計画的メンテナンスの詳細については、「[Planned maintenance for Azure virtual machines (Azure 仮想マシンの計画的メンテナンス)](../articles/virtual-machines/virtual-machines-linux-planned-maintenance.md)」(Linux) または「[Planned maintenance for Azure virtual machines (Azure 仮想マシンの計画的メンテナンス)](../articles/virtual-machines/virtual-machines-windows-planned-maintenance.md)」(Windows) を参照してください。

## 複数インスタンス構成の場合
可用性セットから VM を削除することで、可用性セット構成にデプロイされた VM に対していつ計画的メンテナンスが行われるかを選択できます。
1.	複数インスタンス構成の VM に対する計画的メンテナンスの 7 日前に、電子メールがユーザーに送信されます。電子メールの本文には、サブスクリプション ID と、影響を受ける複数インスタンス VM の名前が記載されています。
2.	この 7 日の間に、そのリージョン内の複数インスタンス VM を可用性セットから削除することにより、インスタンスが更新される時間を選択できます。この構成変更により、仮想マシンがメンテナンス対象の物理ホストからメンテナンス対象ではない別の物理ホストに移動するので、再起動が行われます。 
3.	可用性セットからの VM の削除はクラシック ポータルで行うことができます。 
   
        a.	In the Classic portal, click on the VM and then select “configure.” 
        
        b.	Under “settings”, you can see which Availability Set the VM is in.
        
    ![可用性セットの選択](./media/virtual-machines-planned-maintenance-schedule/availabilitysetselection.png)

        c.	In the availability set dropdown menu, select “remove from availability set.”
        
    ![セットからの削除](./media/virtual-machines-planned-maintenance-schedule/availabilitysetselectionconfiguration.png)

        d.	At the bottom, select “save.” Select “yes” to acknowledge that this action will restart the VM.
4.	これらの VM は単一インスタンス ホストに移動し、可用性セット構成の計画的メンテナンスでは更新されません。
5.	可用性セット VM の更新が (元の電子メールに記載されているスケジュールに従って) 完了した後で、可用性セットに VM を戻す必要があります。戻した VM は、複数インスタンス VM として再構成されます。VM を単一インスタンスから複数インスタンスに戻すと、再起動が行われます。通常、Azure 環境全体のすべての複数インスタンスの更新が完了した後で、単一インスタンスのメンテナンスが行われます。

これも次の Azure PowerShell を使用して行うことができます。Get-AzureVM -ServiceName "<VmCloudServiceName>" -Name "<VmName>" | Remove-AzureAvailabilitySet | Update-AzureVM

## 単一インスタンス構成の場合
VM を可用性セットに追加することで、単一インスタンス構成の VM に対する計画的メンテナンスのタイミングを選択できます。詳細な手順:
1.	単一インスタンス構成の VM に対する計画的メンテナンスの 7 日前に、電子メールがユーザーに送信されます。電子メールの本文には、サブスクリプション ID と、影響を受ける単一インスタンス VM の名前が記載されています。 
2.	この 7 日の間に、単一インスタンス VM を同じリージョンの可用性セットに移動することにより、インスタンスが再起動する時間を選択できます。この構成変更により、仮想マシンがメンテナンス対象の物理ホストからメンテナンス対象ではない別の物理ホストに移動するので、再起動が行われます。
3.	クラシック ポータルと Azure PowerShell を使用し、ここで示す手順に従って、既存の VM を可用性セットに追加します (後の Azure PowerShell サンプルを参照)。
4.	複数インスタンスとして再構成した VM は、単一インスタンス VM の計画的メンテナンスから除外されます。
5.	単一インスタンス VM の更新が (元の電子メールに記載されているスケジュールに従って) 完了したら、可用性セットから VM を削除できます。削除した VM は、単一インスタンス VM として再構成されます。

これも Azure PowerShell を使用して行うことができます。

    Get-AzureVM -ServiceName "<VmCloudServiceName>" -Name "<VmName>" | Set-AzureAvailabilitySet -AvailabilitySetName "<AvSetName>" | Update-AzureVM

<!--Anchors-->



<!--Link references-->
[Virtual Machines Manage Availability]: virtual-machines-windows-tutorial.md
[Understand planned versus unplanned maintenance]: virtual-machines-manage-availability.md#Understand-planned-versus-unplanned-maintenance/

<!---HONumber=AcomDC_0323_2016-->