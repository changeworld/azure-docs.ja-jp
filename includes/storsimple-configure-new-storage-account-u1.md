<!--author=alkohli last changed: 9/17/15-->

#### <a name="to-add-a-storage-account-in-storsimple-8000-series-update-10"></a>StorSimple 8000 シリーズ Update 1.0 では、ストレージ アカウントを追加するには
1. StorSimple Manager サービスのランディング ページで、サービスを選択し、ダブルクリックします。 これは、操作により、**クイック スタート**ページ。 選択、**構成**ページ。
2. をクリックして**ストレージ アカウントの追加/編集**です。
3. **ストレージ アカウントの追加/編集**ダイアログ ボックスで、をクリックして**新規追加**です。
4. **プロバイダー**フィールドで、適切なクラウド サービス プロバイダーを選択します。 サポートされているプロバイダーとは、Azure、Amazon S3、RR、HP OpenStack と Amazon S3 です。 資格情報と、クラウド サービス プロバイダーのストレージ アカウントに関連付けられている場所を指定します。 資格情報の表示されるフィールドが指定したクラウド サービス プロバイダーによって異なります。 
   
   * クラウド サービス プロバイダーとして Azure を選択した場合の指定、**名前**となり、プライマリ**アクセス キー** Microsoft Azure ストレージ アカウントのです。 Azure のアカウントの場所は自動的に設定されます。
     
        ![Azure ストレージ アカウントを追加します。](./media/storsimple-configure-new-storage-account-u1/AddAzureStorageaccount-include.png)
   * Amazon S3 または rr Amazon S3 を選択した場合は、わかりやすいを提供**ストレージ アカウント名**、**アクセス キー**、および**シークレット キー**です。 Amazon S3 と rr Amazon S3 では、次の場所はサポートされています。
     
     * 米国標準
     * US West (Oregon)
     * US West (北カリフォルニア)
     * ヨーロッパ (アイルランド)
     * アジア太平洋 (シンガポール)
     * アジア太平洋 (シドニー)
     * アジア太平洋 (東京)
     * 南アメリカ (サン サンパウロ)
       
       ![Amazon ストレージ アカウントを追加します。](./media/storsimple-configure-new-storage-account-u1/AddAmazonStorageaccount-include.png)
   * クラウド サービス プロバイダーとして HP を選択した場合は、わかりやすいを指定**ストレージ アカウント名**、**テナント ID**、 **Username**、および**パスワード**です。 HP、次の場所はサポートされています。
     
     * 米国東部
     * 米国西部
       
       ![HP ストレージ アカウントを追加します。](./media/storsimple-configure-new-storage-account-u1/AddHPStorageaccount-include.png)
   * 選択した場合に**Openstack** 、クラウド サービス プロバイダーとして提供する、**ホスト名**、**アクセス キー**と**シークレット キー**です。
     
     > [!NOTE]
     > すべてのクラウド サービス プロバイダー、Azure を除くフレンドリ名が許可されます。 別のフレンドリ名を使用し、同じ資格情報のセットを 1 つ以上のストレージ アカウントを作成できます。
     > 
     > 
     
        ![Openstack などのストレージ アカウントを追加します。](./media/storsimple-configure-new-storage-account-u1/AddOpenstackStorageaccount-include.png)
5. 選択**SSL モードを有効にする**デバイスとクラウド間のネットワーク通信をセキュリティで保護されたチャネルを作成します。 クリア、 **SSL モードを有効にする**チェック ボックスをプライベート クラウド内で動作している場合のみです。
   
   > [!NOTE]
   > HP プロバイダーとしてを使用している場合は、SSL 常に有効にします。
   > 
   > 
6. チェック アイコンをクリックします。 ![チェック アイコン](./media/storsimple-configure-new-storage-account/HCS_CheckIcon-include.png)。 ストレージ アカウントが正常に作成された後通知されます。
7. 新しく作成されたストレージ アカウントが表示されます、**構成**ページで、**ストレージ アカウント**です。 をクリックして**保存**を新しいストレージ アカウントを保存します。 をクリックして**OK**確認を求めるときにします。

