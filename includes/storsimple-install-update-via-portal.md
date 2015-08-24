
#### 管理ポータルを使用して Update 1 をインストールするには

1. StorSimple サービス ページでデバイスを選択します。**[デバイス]**、**[メンテナンス]** の順にクリックします。

2. ページの下部にある **[更新プログラムのスキャン]** をクリックします。利用可能な更新プログラムをスキャンするジョブが作成されます。このジョブが正常に完了すると、通知されます。

3. 同じページの **[ソフトウェア更新プログラム]** セクションで、新しいソフトウェア更新プログラムを利用できることがわかります。Update 1.0 をデバイスに適用する前に、リリース ノートを確認することをお勧めします。

    ![Install software updates](./media/storsimple-install-update-via-portal/HCS_SoftwareUpdates1-include.png)

4. ページの下部にある **[更新プログラムのインストール]** をクリックします。

5. 確認を求められます。**[OK]** をクリックします。

6. **[更新プログラムのインストール]** ダイアログ ボックスが表示されます。デバイスが、このダイアログ ボックスに示されているチェック項目を満たしていることを確認します。**[上記の要件を理解し、デバイスを更新する準備ができました]** を選択します。チェック マーク アイコンをクリックします。

    ![Confirmation message](./media/storsimple-install-update-via-portal/HCS_SoftwareUpdates2-include.png)

7. 更新前のチェックを実行中であることが通知されます。
  
    ![Pre-check notification](./media/storsimple-install-update-via-portal/HCS_SoftwareUpdates3-include.png)

    アップグレード前のチェックが失敗した例を次に示します。この場合、両方のデバイス コントローラーが正常であり、オンラインであることを確認する必要があります。また、ハードウェア コンポーネントの正常性も確認する必要があります。この例では、コント ローラー 0 とコント ローラー 1 の各コンポーネントに対処する必要があります。これらの問題に自分で対処できない場合、Microsoft サポートに連絡することが必要になる場合があります。

    ![Pre-check failed](./media/storsimple-install-update-via-portal/HCS_PreUpgradeChecksFailed-include.png)

8. アップグレード前のチェックが正常に完了したら、更新ジョブが作成されます。更新ジョブが正常に作成されると、通知されます。
 
    ![Update job creation](./media/storsimple-install-update-via-portal/HCS_SoftwareUpdates4-include.png)

    その後、更新プログラムがデバイスに適用されます。
 
9. 更新ジョブの進行状況を監視するには、**[ジョブの表示]** をクリックします。[ジョブ] ページでは、更新の進行状況を確認できます。

    ![Update job progress](./media/storsimple-install-update-via-portal/HCS_SoftwareUpdates5-include.png)

10. 更新が完了するまでに数時間かかります。ジョブの詳細はいつでも表示できます。

    ![Update job details](./media/storsimple-install-update-via-portal/HCS_SoftwareUpdates6-include.png)

11. ジョブが完了したら、**[メンテナンス]** ページに移動し、**[ソフトウェア更新プログラム]** まで下にスクロールします。

12. デバイスで **[StorSimple 8000 Series Update 1.0 (6.3.9600.17491)]** が実行されていることを確認します。**[最終更新日]** も変更されています。

    ![Maintenance page](./media/storsimple-install-update-via-portal/HCS_SoftwareUpdates7-include.png)

<!---HONumber=August15_HO7-->