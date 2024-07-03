# how-to-use-relay-command-on-LoadMore-command-of-chat-dotnet-maui
This repository contains a sample demonstrating how to use RelayCommand for LoadMore Command in .NET MAUI Chat SfChat.
The [.NET MAUI Chat](https://www.syncfusion.com/maui-controls/maui-chat) allows the user to use RelayCommand for [LoadMore](https://help.syncfusion.com/maui/chat/load-more) Command of SfChat

**XAML:**

You can integrate the relay command for LoadMore

```
<ContentPage xmlns="http://schemas.microsoft.com/dotnet/2021/maui"
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
xmlns:local="clr-namespace:ChatMaui"
xmlns:sfchat="clr-namespace:Syncfusion.Maui.Chat;assembly=Syncfusion.Maui.Chat"
x:Class="ChatMaui.MainPage">

<ContentPage.BindingContext>
<local:LoadMoreViewModel LoadMoreBehavior="AutoOnScroll" x:Name="viewModel"/>
</ContentPage.BindingContext>

<ContentPage.Content>
<Grid RowDefinitions="Auto,*">
<sfchat:SfChat Grid.Row="1" 
x:Name="sfChat"
CanAutoScrollToBottom="False"
Messages="{Binding Messages}"
CurrentUser="{Binding CurrentUser}" 
MessageSpacing="24" 
MessageShape="RoundedRectangle"
LoadMoreCommand="{Binding LoadMoreItemsCommand}" 
LoadMoreBehavior="{Binding LoadMoreBehavior}" 
IsLazyLoading="{Binding IsBusy}">
</sfchat:SfChat>

</Grid>
</ContentPage.Content>
</ContentPage>
 ```

**ViewModel.cs**
```
 public partial class LoadMoreViewModel: ObservableObject, INotifyPropertyChanged
 {
        private bool isBusy = false;

        private LoadMoreOption loadMoreBehavior;

        public LoadMoreOption LoadMoreBehavior
        {
            get
            {
                return this.loadMoreBehavior;
            }
            set
            {
                this.loadMoreBehavior = value;
                this.RaisePropertyChanged("LoadMoreBehavior");
            }
        }
        
        public bool IsBusy
        {
            get { return this.isBusy; }
            set
            {
                this.isBusy = value;
                RaisePropertyChanged("IsBusy");
            }
        }
   

        #region Constructor
        public LoadMoreViewModel()
        {
            this.LoadMoreBehavior = LoadMoreOption.Auto;
        }
        #endregion

        #region Private Methods
        
        private bool CanLoadMoreItems(object obj)
        {
            // If messages are still there in the old message collection then execute the load more command.
            if (this.OldMessages.Count > 0)
            {
                return true;
            }
            else
            {
                this.LoadMoreBehavior = LoadMoreOption.None;
                IsBusy = false;
                return false;
            }

            return true;
        }

        [RelayCommand (CanExecute = nameof(CanLoadMoreItems))]
        public async void LoadMoreItems(object obj)
        {
            var chat = obj;
            try
            {
                // Set is busy as true to show the busy indicator
                this.IsBusy = true;
                await Task.Delay(3000);
                LoadMoreMessages();
            }
            catch { }
            finally
            {
                // Set is busy as false to hide the busy indicator
                IsBusy = false;
            }
        }

        private void LoadMoreMessages()
        {
            for (int i = 1; i <= 5; i++)
            {
                var oldMessage = this.OldMessages[this.OldMessages.Count - 1];
                this.Messages.Insert(0, oldMessage);
                this.OldMessages.Remove(oldMessage);
            }
        }

        #endregion
    }
}



```
