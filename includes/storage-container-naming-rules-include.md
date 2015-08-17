Azure Storage のどの BLOB もコンテナーに格納する必要があります。コンテナーは、BLOB 名の一部を形成しています。たとえば、次の BLOB の URI のサンプルでは、`mycontainer` がコンテナーの名前です。

	https://storagesample.blob.core.windows.net/mycontainer/blob1.txt
	https://storagesample.blob.core.windows.net/mycontainer/photos/myphoto.jpg
 
> [AZURE.IMPORTANT]コンテナーの名前は、常に小文字にする必要があります。コンテナー名に大文字が含まれている場合や、コンテナーの名前付け規則の他の違反がある場合、400 エラー (無効な要求) が発生することがあります。コンテナーの名前付け規則については、「[コンテナー、BLOB、およびメタデータの名前付けおよび参照](https://msdn.microsoft.com/library/azure/dd135715.aspx)」をご覧ください。

<!---HONumber=August15_HO6-->