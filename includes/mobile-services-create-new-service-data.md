

次に、データ ストレージのメモリ内のリストを置き換える新しいモバイル サービスを作成します。新しいモバイル サービスを作成するには、次のステップに従います。

1. [Azure 管理ポータル](https://manage.windowsazure.com/)にログインします。 
2.	ナビゲーション ウィンドウの下部にある **[新規]** をクリックします。

	![plus-new](./media/mobile-services-create-new-service-data/plus-new.png)

3.	**[コンピューティング]**、**[モバイル サービス]** の順に展開し、**[作成]** をクリックします。

	![mobile-create](./media/mobile-services-create-new-service-data/mobile-create.png)

    **[新しいモバイル サービス]** ダイアログ ボックスが表示されます。

4.	**[モバイル サービスの作成]** ページで、**[無料の 20 MB SQL データベースの作成]** を選択し、**[URL]** ボックスに新しいモバイル サービスのサブドメイン名を入力して、名前が確認されるまで待ちます。名前の確認が完了したら、右矢印ボタンをクリックして次のページに進みます。

	![mobile-create-page1](./media/mobile-services-create-new-service-data/mobile-create-page1.png)

    **[データベースの設定の指定]** ページが表示されます。

    
	> [AZURE.NOTE]このチュートリアルの一環として、新しい SQL Database インスタンスと SQL Database サーバーを作成します。この新しいデータベースは、再利用したり、その他の SQL データベース インスタンスと同様に管理したりすることができます。新しいモバイル サービスと同じリージョンにデータベースを既に所有している場合は、代わりに **[既存のデータベースを使用する]** を選択してそのデータベースを選択できます。別のリージョンにあるデータベースを使用することは、帯域幅コストと待機時間が増加するため、お勧めしません。

5.	**[名前]** ボックスに新しいデータベースの名前を入力し、**[ログイン名]** ボックスに、新しい SQL データベース サーバーの管理者ログイン名を入力します。パスワードを入力および確認入力し、チェック ボタンをクリックして処理を完了します。

	![mobile-create-page2](./media/mobile-services-create-new-service-data/mobile-create-page2.png)

    
	> [AZURE.NOTE]指定したパスワードが最小要件を満たしていない場合や不一致が発生している場合は、警告が表示されます。
	>
	> 指定した管理者ログイン名とパスワードを書き留めておくことをお勧めします。この情報は、今後 SQL Database インスタンスまたはサーバーを再利用する際に必要になります。

これで、モバイル アプリケーションで使用できる新しいモバイル サービスが作成されました。次に、アプリケーション データを格納するための新しいテーブルを追加します。このテーブルは、アプリケーションがメモリ内のコレクションの代わりに使用します。

<!---HONumber=Oct15_HO3-->