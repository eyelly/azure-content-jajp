1.	オンラインの [Microsoft Azure プレビュー ポータル](https://portal.azure.com/)にサインインします。
2.	ジャンプバーで **[新規]**、**[データ + ストレージ]**、**[DocumentDB]** の順にクリックします。 
  
	![[作成] ブレードの [新規] ボタンと [データ + ストレージ]、および [データ + ストレージ] ブレードの Azure DocumentDB が強調表示された、データベース作成用の Azure プレビュー ポータルのスクリーン ショット](media/documentdb-create-dbaccount/ca1.png)

3. **[新しい DocumentDB アカウント]** ブレードで、DocumentDB アカウントに対して必要な構成を指定します。
 
	![新しい [DocumentDB] ブレードのスクリーン ショット](media/documentdb-create-dbaccount/ca3.png)


	- **[ID]** ボックスに、DocumentDB アカウントを識別する名前を入力します。**ID** が検証されると、緑色のチェック マークが **[ID]** ボックスに表示されます。この **ID** の値は、URI 内のホスト名になります。**ID** に含めることができるのは英小文字、数字、および "-" のみで、文字数は 3 ～ 50 文字にする必要があります。選択したエンドポイント名に *documents.azure.com* が追加され、これが DocumentDB アカウント エンドポイントになります。
	

	- DocumentDB では標準アカウント レベルのみがサポートされるため、**[アカウント レベル]** レンズはロックされています。詳細については、「[DocumentDB の料金](http://go.microsoft.com/fwlink/p/?LinkID=402317&clcid=0x409)」を参照してください。
	
	- **[サブスクリプション]** で、DocumentDB アカウントに使用する Azure サブスクリプションを選択します。アカウントのサブスクリプションが 1 つである場合は、既定ではそのアカウントが選択されます。

	- **[リソース グループ]** で、DocumentDB アカウントのリソース グループを選択または作成します。既定では、新しいリソース グループが作成されます。ただし、DocumentDB アカウントを追加する既存のリソース グループを選択することもできます。詳細については、「[Azure プレビュー ポータルを使用した Azure リソースの管理](resource-group-portal.md)」を参照してください。
 
	- **[場所]** を使用して、DocumentDB アカウントのホストの場所を指定します。

4.	新しい DocumentDB アカウントのオプションを構成したら、**[作成]** をクリックします。DocumentDB アカウントの作成には数分かかる場合があります。状態を確認するには、スタート画面で進行状況を監視してください。  
	![スタート画面の [作成中] タイルのスクリーン ショット (オンライン データベース クリエーター)](media/documentdb-create-dbaccount/ca4.png)
  
	または、通知ハブから進行状況を監視できます。

	![迅速なデータベースの作成 - DocumentDB アカウントを作成中であることを示す通知ハブのスクリーンショット](media/documentdb-create-dbaccount/ca5.png)

	![DocumentDB アカウントが正常に作成され、リソース グループにデプロイされたことを示す通知ハブのスクリーンショット - オンライン データベース クリエーターの通知](media/documentdb-create-dbaccount/ca6.png)

5.	DocumentDB アカウントが作成されたら、オンライン ポータルの既定の設定で使用するための準備が整います。DocumentDB アカウントの既定の一貫性は **Session** に設定されることに注意してください。既定の一貫性の設定は、**[DocumentDB アカウント]** ブレードの **[既定の一貫性]** タイルをクリックして変更できます。

    ![[リソース グループ] ブレードのスクリーン ショット - アプリケーション開発の開始](media/documentdb-create-dbaccount/ca7.png)

[How to: Create a DocumentDB account]: #Howto
[Next steps]: #NextSteps
[documentdb-manage]: ../articles/documentdb/documentdb-manage.md

<!---HONumber=Oct15_HO3-->