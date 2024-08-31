**MessageDetailPage.xaml**

```
<?xml version="1.0" encoding="utf-8" ?>
<ContentPage
    x:Class="ContactSMS.Client.Views.MessageDetailPage"
    xmlns="http://schemas.microsoft.com/dotnet/2021/maui"
    xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
    xmlns:model="clr-namespace:ContactSMS.Client.Models"
    xmlns:templates="clr-namespace:ContactSMS.Client.Views.Templates"
    xmlns:vm="clr-namespace:ContactSMS.Client.ViewModels"
    Title="消息详情"
    x:DataType="vm:MessageViewModel">

    <ContentPage.Resources>
        <ResourceDictionary>

            <DataTemplate x:Key="ReceiverChatMessageItemTemplate" x:DataType="model:MessageDetailModel">
                <templates:ReceiverChatMessageItemTemplate />
            </DataTemplate>

            <DataTemplate x:Key="SenderChatMessageItemTemplate" x:DataType="model:MessageDetailModel">
                <templates:SenderChatMessageItemTemplate />
            </DataTemplate>

            <templates:MessageDataTemplateSelector
                x:Key="MessageDataTemplateSelector"
                ReceiverMessageTemplate="{StaticResource ReceiverChatMessageItemTemplate}"
                SenderMessageTemplate="{StaticResource SenderChatMessageItemTemplate}" />

            <Style x:Key="ChatCollectionStyle" TargetType="CollectionView">
                <Setter Property="Margin" Value="20" />
            </Style>
        </ResourceDictionary>
    </ContentPage.Resources>


    <ScrollView>

        <CollectionView
            ItemTemplate="{StaticResource MessageDataTemplateSelector}"
            ItemsSource="{Binding Messages}"
            Style="{StaticResource ChatCollectionStyle}" />

    </ScrollView>


</ContentPage>
```

**SenderChatMessageItemTemplate**

```
<?xml version="1.0" encoding="utf-8" ?>
<ContentView
    x:Class="ContactSMS.Client.Views.Templates.SenderChatMessageItemTemplate"
    xmlns="http://schemas.microsoft.com/dotnet/2021/maui"
    xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
    xmlns:model="clr-namespace:ContactSMS.Client.Models"
    x:DataType="model:MessageDetailModel">

    <ContentView.Resources>
        <ResourceDictionary>
            <Style x:Key="SenderContainerStyle" TargetType="Grid">
                <Setter Property="HorizontalOptions" Value="End" />
                <Setter Property="Margin" Value="0,15" />
            </Style>

            <Style x:Key="MessageTextStyle" TargetType="Label">
                <Setter Property="FontSize" Value="14" />
                <Setter Property="TextColor" Value="Black" />
                <Setter Property="Padding" Value="10" />
            </Style>

            <Color x:Key="MessageColor">#D8E9F1</Color>

            <Style x:Key="MessageShapeStyle" TargetType="BoxView">
                <Setter Property="Color" Value="{StaticResource MessageColor}" />
                <Setter Property="CornerRadius" Value="24, 0, 24, 24" />
            </Style>

            <Style x:Key="MessageTimeStyle" TargetType="Label">
                <Setter Property="TextColor" Value="#A5A4A9" />
                <Setter Property="HorizontalOptions" Value="End" />
                <Setter Property="Margin" Value="0,5,0,0" />
            </Style>

        </ResourceDictionary>
    </ContentView.Resources>

    <Grid RowDefinitions="*,20" Style="{StaticResource SenderContainerStyle}">
        <Grid>
            <BoxView Style="{StaticResource MessageShapeStyle}" />
            <Label Style="{StaticResource MessageTextStyle}" Text="{Binding Content}" />
        </Grid>
        <Label
            Grid.Row="1"
            Style="{StaticResource MessageTimeStyle}"
            Text="{Binding Date}" />
    </Grid>

</ContentView>
```

**ReceiverChatMessageItemTemplate.xaml**

```
<?xml version="1.0" encoding="utf-8" ?>
<ContentView
    x:Class="ContactSMS.Client.Views.Templates.ReceiverChatMessageItemTemplate"
    xmlns="http://schemas.microsoft.com/dotnet/2021/maui"
    xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
    xmlns:model="clr-namespace:ContactSMS.Client.Models"
    x:DataType="model:MessageDetailModel">

    <ContentView.Resources>
        <ResourceDictionary>
            <Style x:Key="ReceiverContainerStyle" TargetType="Grid">
                <Setter Property="HorizontalOptions" Value="Start" />
            </Style>

            <Style x:Key="MessageTextStyle" TargetType="Label">
                <Setter Property="FontSize" Value="14" />
                <Setter Property="TextColor" Value="Black" />
                <Setter Property="Padding" Value="10" />
                <Setter Property="Margin" Value="0,0,0,5" />
            </Style>

            <Color x:Key="MessageColor">#9297A1</Color>

            <Style x:Key="MessageShapeStyle" TargetType="BoxView">
                <Setter Property="Color" Value="{StaticResource MessageColor}" />
                <Setter Property="CornerRadius" Value="0, 24, 24, 24" />
            </Style>

            <Style x:Key="MessageTimeStyle" TargetType="Label">
                <Setter Property="TextColor" Value="#A5A4A9" />
                <Setter Property="HorizontalOptions" Value="Start" />
                <Setter Property="Margin" Value="0,5,0,0" />
            </Style>

        </ResourceDictionary>
    </ContentView.Resources>

    <Grid RowDefinitions="*,20" Style="{StaticResource ReceiverContainerStyle}">
        <Grid>
            <BoxView Style="{StaticResource MessageShapeStyle}" />
            <Label Style="{StaticResource MessageTextStyle}" Text="{Binding Content}" />
        </Grid>
        <Label
            Grid.Row="1"
            Style="{StaticResource MessageTimeStyle}"
            Text="{Binding Date}" />
    </Grid>

</ContentView>
```

**MessageDataTemplateSelector.cs 必须要是public，**

```
namespace ContactSMS.Client.Views.Templates
{
    public class MessageDataTemplateSelector : DataTemplateSelector
    {
        public DataTemplate SenderMessageTemplate { get; set; }
        public DataTemplate ReceiverMessageTemplate { get; set; }

        protected override DataTemplate OnSelectTemplate(object item, BindableObject container)
        {
            var message = (MessageDetailModel)item;

            if (message.UserId == 0)
                return ReceiverMessageTemplate;

            return SenderMessageTemplate;
        }
    }
}
```