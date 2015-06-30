<properties 
	pageTitle="Notification Hubs ローカライズ ニュース速報のチュートリアル" 
	description="Azure Service Bus Notification Hubs を使用して、ローカライズしたニュース速報通知を送信する方法を説明します。" 
	services="notification-hubs" 
	documentationCenter="windows" 
	authors="RickSaling" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="notification-hubs" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="11/21/2014" 
	ms.author="ricksal"/>
# Notification Hubs を使用したローカライズ ニュース速報の送信

<div class="dev-center-tutorial-selector sublanding"> 
    	<a href="/documentation/articles/notification-hubs-windows-store-dotnet-send-localized-breaking-news/" title="Windows Store C#" class="current">Windows Store C#</a><a href="/documentation/articles/notification-hubs-ios-send-localized-breaking-news/" title="iOS">iOS</a>
</div>

このトピックでは、Azure Notification Hubs の**テンプレート**機能を使用して、言語およびデバイスごとにローカライズしたニュース速報通知をブロードキャストする方法について説明します。このチュートリアルでは、「[Notification Hubs を使用したニュース速報の送信]」で作成した Windows ストア アプリケーションを使用します。完了すると、興味のあるニュース速報カテゴリに登録して受信する通知の言語を指定し、選択したカテゴリのその言語のプッシュ通知だけを受信できるようになります。

このチュートリアルでは、このシナリオを有効にするための、次の基本的な手順について説明します。

1. [テンプレートの概念] 
2. [アプリケーションのユーザー インターフェイス]
3. [Windows ストア クライアント アプリケーションを構築する]
4. [バックエンドから通知を送信する]


このシナリオは次の 2 つに分けられます。

- Windows ストア アプリケーションで、クライアント デバイスが言語を指定し、さまざまなニュース速報カテゴリを購読できるようになります。 

- Azure Notification Hubs の**タグ**機能と**テンプレート**機能を使用して、バックエンドから通知がブロードキャストされます。



##前提条件 ##

「[Notification Hubs を使用したニュース速報の送信]」のチュートリアルを完了し、コードが使用可能な状態になっている必要があります。このチュートリアルでは、そのコードに基づいているためです。 

Visual Studio 2012 も必要です。


<h2><a name="concepts"></a>テンプレートの概念</h2>

「[Notification Hubs を使用したニュース速報の送信]」では、**タグ**を使用してさまざまなニュース カテゴリの通知を購読するアプリケーションを構築しました。
しかし、多くのアプリケーションは複数の市場をターゲットとしており、ローカライズが必要です。これは、通知自体の内容をローカライズし、適切なデバイス セットに配信する必要があることを意味します。
このトピックでは、Notification Hubs の**テンプレート**機能を使用して、ローカライズしたニュース速報通知を簡単に配信する方法について説明します。

注: ローカライズした通知を送信する 1 つの方法として、各タグの複数のバージョンを作成する方法があります。たとえば、ワールド ニュースで英語、フランス語、標準中国語をサポートするには、3 つのタグ("world_en"、"world_fr"、"world_ch") が必要です。その後、ワールド ニュースのローカライズ バージョンをこれらの各タグに送信する必要があります。このトピックでは、テンプレートを使用することでタグの増加を抑制し、複数のメッセージを送信しなくてもよいようにします。

大まかに言えば、テンプレートとは、特定のデバイスが通知をどのように受信するかを特定するための手段です。テンプレートは、アプリケーション バックエンドにより送信されるメッセージの一部となっているプロパティを参照することで、正確なペイロードを指定します。このケースでは、サポートされるすべての言語を含む、ロケールにとらわれないメッセージを送信します。

	{
		"News_English": "...",
		"News_French": "...",
		"News_Mandarin": "..."
	}

次に、デバイスが、適切なプロパティを参照するテンプレートを登録するようにします。たとえば、簡単なトースト メッセージを受信する Windows ストア アプリケーションは、次のテンプレートを登録します。

	<toast>
	  <visual>
	    <binding template="ToastText01">
	      <text id="1">$(News_English)</text>
	    </binding>
	  </visual>
	</toast>



テンプレートは有用な機能です。詳細については、「[Notification Hubs の概要]」の記事を参照してください。テンプレート式言語のリファレンスは、「[Notification Hubs How-To for Windows Store (方法: Notification Hubs (Windows ストア))]」にあります。


<h2><a name="ui"></a>アプリケーションのユーザー インターフェイス</h2>

ここでは、「[Notification Hubs を使用したニュース速報の送信]」で作成したニュース速報アプリケーションを変更し、テンプレートを使用してローカライズしたニュース速報を送信します。


クライアント アプリケーションがローカライズしたメッセージを受信できるようにするには、 *ネイティブ*登録 (つまり、テンプレートを指定する登録) をテンプレート登録に置き換える必要があります。


Windows ストア アプリケーションで、以下の手順を実行します。

MainPage.xaml を変更してロケール コンボボックスを追加します。

	<Grid Margin="120, 58, 120, 80"  
			Background="{StaticResource ApplicationPageBackgroundThemeBrush}">
        <Grid.RowDefinitions>
            <RowDefinition />
            <RowDefinition />
            <RowDefinition />
            <RowDefinition />
            <RowDefinition />
            <RowDefinition />
        </Grid.RowDefinitions>
        <Grid.ColumnDefinitions>
            <ColumnDefinition />
            <ColumnDefinition />
        </Grid.ColumnDefinitions>
        <TextBlock Grid.Row="0" Grid.Column="0" Grid.ColumnSpan="2"  TextWrapping="Wrap" Text="Breaking News" FontSize="42" VerticalAlignment="Top"/>
        <ComboBox Name="Locale" HorizontalAlignment="Left" VerticalAlignment="Center" Width="200" Grid.Row="1" Grid.Column="0">
            <x:String>English</x:String>
            <x:String>French</x:String>
            <x:String>Mandarin</x:String>
        </ComboBox>
        <ToggleSwitch Header="World" Name="WorldToggle" Grid.Row="2" Grid.Column="0"/>
        <ToggleSwitch Header="Politics" Name="PoliticsToggle" Grid.Row="3" Grid.Column="0"/>
        <ToggleSwitch Header="Business" Name="BusinessToggle" Grid.Row="4" Grid.Column="0"/>
        <ToggleSwitch Header="Technology" Name="TechnologyToggle" Grid.Row="2" Grid.Column="1"/>
        <ToggleSwitch Header="Science" Name="ScienceToggle" Grid.Row="3" Grid.Column="1"/>
        <ToggleSwitch Header="Sports" Name="SportsToggle" Grid.Row="4" Grid.Column="1"/>
        <Button Content="Subscribe" HorizontalAlignment="Center" Grid.Row="5" Grid.Column="0" Grid.ColumnSpan="2" Click="Button_Click" />
    </Grid>

<h2><a name="building-client"></a><span class="building app">アプリケーションの ui</span>Windows ストア クライアント アプリケーションを構築する</h2>

1. Notifications クラスで、 *StoreCategoriesAndSubscribe* メソッドと  *SubscribeToCateories* メソッドにロケール パラメーターを追加します。

		public async Task StoreCategoriesAndSubscribe(string locale, IEnumerable<string> categories)
        {
            ApplicationData.Current.LocalSettings.Values["categories"] = string.Join(",", categories);
            ApplicationData.Current.LocalSettings.Values["locale"] = locale;
            await SubscribeToCategories(locale, categories);
        }

        public async Task SubscribeToCategories(string locale, IEnumerable<string> categories)
        {
            var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();
            var template = String.Format(@"<toast><visual><binding template=""ToastText01""><text id=""1"">$(News_{0})</text></binding></visual></toast>", locale);

            await hub.RegisterTemplateAsync(channel.Uri, template, "newsTemplate", categories);
        }

	 *RegisterNativeAsync* メソッドを呼び出す代わりに  *RegisterTemplateAsync* を呼び出す点に注意してください。テンプレートがロケールに依存している特定の通知形式を登録します。さらに、テンプレートの名前 ("newsTemplate") も指定します。複数のテンプレートを登録する可能性があり (たとえば、トースト通知用のテンプレートとタイル通知用のテンプレートなど)、それらを更新または削除するには名前を付ける必要があるためです。

	同じタグを持つ複数のテンプレートがデバイスによって登録された場合、そのタグをターゲットとするメッセージが受信されると複数の通知がデバイスに配信される点に注意してください (テンプレートごとに 1 つずつ)。この動作は、同じ論理メッセージによって複数のビジュアル通知を生成する必要がある場合に役立ちます。たとえば、Windows ストア アプリケーションにバッジとトーストの両方を表示する場合などです。

2. 次のメソッドを追加して、格納されたロケールを取得します。

		public string RetrieveLocale()
        {
            var locale = (string) ApplicationData.Current.LocalSettings.Values["locale"];
            return locale != null ? locale : "English";
        }

3. 次に示すように、MainPage.xaml.cs でロケール コンボボックスの現在の値を取得し、それを Notifications クラスへの呼び出しに提供することで、ボタン クリック ハンドラーを更新します。

		 var locale = (string)Locale.SelectedItem;
            
         var categories = new HashSet<string>();
         if (WorldToggle.IsOn) categories.Add("World");
         if (PoliticsToggle.IsOn) categories.Add("Politics");
         if (BusinessToggle.IsOn) categories.Add("Business");
         if (TechnologyToggle.IsOn) categories.Add("Technology");
         if (ScienceToggle.IsOn) categories.Add("Science");
         if (SportsToggle.IsOn) categories.Add("Sports");

         await ((App)Application.Current).Notifications.StoreCategoriesAndSubscribe(locale, categories);

         var dialog = new MessageDialog(String .Format("Locale: {0}; Subscribed to: {1}", locale, string.Join(",", categories)));
         dialog.Commands.Add(new UICommand("OK"));
         await dialog.ShowAsync();

4. 最後に、App.xaml.cs ファイルで、 
 *OnLaunched* メソッドにある Notifications シングルトンへの呼び出しを更新します。

		Notifications.SubscribeToCategories(Notifications.RetrieveLocale(), Notifications.RetrieveCategories());


<h2><a name="send"></a>バックエンドからローカライズした通知を送信する</h2>

[AZURE.INCLUDE [notification-hubs-localized-back-end](../../includes/notification-hubs-localized-back-end.md)]





## 次のステップ

テンプレートの使用方法の詳細については、「[Notification Hubs によるユーザーへの通知:ASP.NET]」および「[Notification Hubs によるユーザーへの通知:Mobile Services]」を参照してください。また、「[Notification Hubs の概要]」も参照してください。テンプレート式言語のリファレンスは、「[Notification Hubs How-To for Windows Store (方法: Notification Hubs (Windows ストア))]」にあります。

<!-- Anchors. -->
[テンプレートの概念]: #concepts
[アプリケーションのユーザー インターフェイス]: #ui
[Windows ストア クライアント アプリケーションを構築する]: #building-client
[バックエンドから通知を送信する]: #send
[次のステップ]:#next-steps

<!-- Images. -->





















<!-- URLs. -->
[モバイル サービス]: /develop/mobile/tutorials/get-started
[Notification Hubs によるユーザーへの通知:ASP.NET]: /manage/services/notification-hubs/notify-users-aspnet
[Notification Hubs によるユーザーへの通知:Mobile Services]: /manage/services/notification-hubs/notify-users
[Notification Hubs を使用したニュース速報の送信]: /manage/services/notification-hubs/breaking-news-dotnet 

[アプリケーションの提出に関するページ]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[マイ アプリケーション]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Windows 向け live SDK]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Mobile Services の使用]: /develop/mobile/tutorials/get-started/#create-new-service
[データの使用]: /develop/mobile/tutorials/get-started-with-data-dotnet
[認証の使用]: /develop/mobile/tutorials/get-started-with-users-dotnet
[プッシュ通知の使用]: /develop/mobile/tutorials/get-started-with-push-dotnet
[アプリケーション ユーザーへのプッシュ通知]: /develop/mobile/tutorials/push-notifications-to-app-users-dotnet
[スクリプトを使用したユーザーの承認]: /develop/mobile/tutorials/authorize-users-in-scripts-dotnet
[JavaScript と HTML]: /develop/mobile/tutorials/get-started-with-push-js

[Azure 管理ポータル]: https://manage.windowsazure.com/
[wns オブジェクト]: http://go.microsoft.com/fwlink/p/?LinkId=260591
[Notification Hubs の概要]: http://msdn.microsoft.com/library/jj927170.aspx
[方法: Notification Hubs (iOS)]: http://msdn.microsoft.com/library/jj927168.aspx
[Notification Hubs How-To for Windows Store (方法: Notification Hubs (Windows ストア))]: http://msdn.microsoft.com/library/jj927172.aspx

<!--HONumber=49--> 