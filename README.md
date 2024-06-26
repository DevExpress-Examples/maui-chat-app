<!-- default badges list -->
![](https://img.shields.io/endpoint?url=https://codecentral.devexpress.com/api/v1/VersionRange/811922089/23.2.5%2B)
[![](https://img.shields.io/badge/Open_in_DevExpress_Support_Center-FF7200?style=flat-square&logo=DevExpress&logoColor=white)](https://supportcenter.devexpress.com/ticket/details/T1237000)
[![](https://img.shields.io/badge/📖_How_to_use_DevExpress_Examples-e9f6fc?style=flat-square)](https://docs.devexpress.com/GeneralInformation/403183)
[![](https://img.shields.io/badge/💬_Leave_Feedback-feecdd?style=flat-square)](#does-this-example-address-your-development-requirementsobjectives)
<!-- default badges end -->

# Use DevExpress .NET MAUI Components to Build a Chat View

This example uses DevExpress .NET MAUI Components to display a chat view with sender and receiver messages, suggested quick replies, and response input controls.

<img width="40%" alt="DevExpress Chat for .NET MAUI" src="Images/app-preview.png">

## Included Controls and Their Properties

* [DXContentPresenter](https://docs.devexpress.com/MAUI/DevExpress.Maui.Core.DXContentPresenter): [Content](https://docs.devexpress.com/MAUI/DevExpress.Maui.Core.DXBorder.Content), [HorizontalOptions](https://learn.microsoft.com/en-us/dotnet/api/microsoft.maui.controls.view.horizontaloptions?view=net-maui-8.0)
* [DXBorder](https://docs.devexpress.com/MAUI/DevExpress.Maui.Core.DXBorder): [VerticalOptions](https://learn.microsoft.com/en-us/dotnet/api/microsoft.maui.controls.view.verticaloptions?view=net-maui-8.0), [IsVisible](https://learn.microsoft.com/en-us/dotnet/api/microsoft.maui.controls.visualelement.isvisible?view=net-maui-8.0)
* [Label](https://learn.microsoft.com/en-us/dotnet/maui/user-interface/controls/label?view=net-maui-8.0): [Text](https://learn.microsoft.com/en-us/dotnet/api/microsoft.maui.controls.label.text?view=net-maui-8.0)
* [DXCollectionView](https://docs.devexpress.com/MAUI/DevExpress.Maui.CollectionView.DXCollectionView): [GroupHeaderTemplate](https://docs.devexpress.com/MAUI/DevExpress.Maui.CollectionView.DXCollectionView.GroupHeaderTemplate), [AllowGroupCollapse](https://docs.devexpress.com/MAUI/DevExpress.Maui.CollectionView.DXCollectionView.AllowGroupCollapse), [ItemsSource](https://docs.devexpress.com/MAUI/DevExpress.Maui.CollectionView.DXCollectionView.ItemsSource), [ItemTemplate](https://docs.devexpress.com/MAUI/DevExpress.Maui.CollectionView.DXCollectionView.ItemTemplate)
* [SafeKeyboardAreaView](https://docs.devexpress.com/MAUI/DevExpress.Maui.Core.SafeKeyboardAreaView)
* [ChipGroup](https://docs.devexpress.com/MAUI/DevExpress.Maui.Editors.ChipGroup): [ChipTapCommand](https://docs.devexpress.com/MAUI/DevExpress.Maui.Editors.ChipGroup.ChipTapCommand), [ItemsSource](https://docs.devexpress.com/MAUI/DevExpress.Maui.Editors.ChipGroup.ItemsSource), [IsMultiline](https://docs.devexpress.com/MAUI/DevExpress.Maui.Editors.ChipGroup.IsMultiline), [DisplayMember](https://docs.devexpress.com/MAUI/DevExpress.Maui.Editors.ChipGroup.DisplayMember)
* [TextEdit](https://docs.devexpress.com/MAUI/DevExpress.Maui.Editors.TextEdit): [PlaceholderText](https://docs.devexpress.com/MAUI/DevExpress.Maui.Editors.EditBase.PlaceholderText), [Text](https://docs.devexpress.com/MAUI/DevExpress.Maui.Editors.TextEditBase.Text)
* [DXButton](https://docs.devexpress.com/MAUI/DevExpress.Maui.Core.DXButton): [Command](https://docs.devexpress.com/MAUI/DevExpress.Maui.Core.DXButtonBase.Command), [Icon](https://docs.devexpress.com/MAUI/DevExpress.Maui.Core.DXContentPresenter.Icon)

## Implementation Details

1. Use a [`SafeKeyboardAreaView`](https://docs.devexpress.com/MAUI/DevExpress.Maui.Core.SafeKeyboardAreaView) as the root for the view layout. When implemented in this manner, the device keyboard will not overlap the message view when it appears on-screen.

    ```xaml
    <dx:SafeKeyboardAreaView> 
        ... 
    </dx:SafeKeyboardAreaView>
    ```

2. Use the [`DXCollectionView`](https://docs.devexpress.com/MAUI/DevExpress.Maui.CollectionView.DXCollectionView) control to display messages. Specify the data source and item templates (use different templates for sender and receiver). Groups items using a built-in algorithm that uses date/time ranges ("Today", "Yesterday", "Last Week", etc). Specify a template for group headers.

    ```xaml
    <dxcv:DXCollectionView
        ItemsSource="{Binding Messages}"
        ItemTemplate="{local:MessageTemplateSelector SenderTemplate=..., RecipientTemplate=...}"
        GroupHeaderTemplate="{StaticResource dayGroupTemplate}"
        ... 
    >
        <dxcv:DXCollectionView.GroupDescription>
            <dxcv:GroupDescription FieldName="SentAt" GroupInterval="DateRange" />
        </dxcv:DXCollectionView.GroupDescription>
    </dxcv:DXCollectionView>
    ```

3. Use [`DXContentPresenter`](https://docs.devexpress.com/MAUI/DevExpress.Maui.Core.DXContentPresenter) components to define templates for sender and receiver messages.

    ```xaml
    <ContentPage.Resources>
        <DataTemplate x:Key="senderMessageTemplate"  x:DataType="{x:Type local:Message}">
            <dx:DXContentPresenter ... >
            </dx:DXContentPresenter>
        </DataTemplate>
        <DataTemplate x:Key="recipientMessageTemplate" x:DataType="{x:Type local:Message}">
            <dx:DXContentPresenter ... >
            </dx:DXContentPresenter>
        </DataTemplate>
        ...
    </ContentPage.Resources>
    ```
    
4. Call the [`DXCollectionView.ScrollTo()`](https://docs.devexpress.com/MAUI/DevExpress.Maui.CollectionView.DXCollectionView.ScrollTo(System.Int32)) method to scroll the view to the last message.

    ```csharp
    public partial class MainPage : ContentPage {
        // ...
        void OnMessagesCollectionChanged(object sender, NotifyCollectionChangedEventArgs e) {
            chatSurface.ScrollTo(chatSurface.GetItemHandle(vm.Messages.Count - 1));
        }
    }
    ```
    
5. Use the [`ChipGroup`](https://docs.devexpress.com/MAUI/DevExpress.Maui.Editors.ChipGroup) control to display short answers.

    ```xaml
    <dxe:ChipGroup ...      
        DisplayMember="Text"
        ItemsSource="{Binding SuggestedActions}" />
    ```

    ```csharp
    public ChatViewModel() {
        // ...
        SuggestedActions = new ObservableCollection<SuggestedAction>() {
            new SuggestedAction() { Message = new Message() { Sender = Me, SentAt = DateTime.Now, Text = "Sure" }, Text = "Sure" },
            new SuggestedAction() { Message = new Message() { Sender = Me, SentAt = DateTime.Now, Text = "Great" }, Text = "Great" },
            new SuggestedAction() { Message = new Message() { Sender = Me, SentAt = DateTime.Now, Text = "Thank you" }, Text = "Thank you" },
            new SuggestedAction() { Message = new Message() { Sender = Me, SentAt = DateTime.Now, Text = "My pleasure" }, Text = "My pleasure" }
        };
    }
    ```

6. Use the [`TextEdit`](https://docs.devexpress.com/MAUI/DevExpress.Maui.Editors.TextEdit) control to create/display a text message input field. To send the message, specify the [DXButton](https://docs.devexpress.com/MAUI/DevExpress.Maui.Core.DXButton) control.

    ```xaml
    <Grid ... >
        <dxe:TextEdit ...
            x:Name="messageEditor"
            Text="{Binding EditMessageText}"/>
        <dx:DXButton ... 
            Command="{Binding SendMessageCommand}"
            CommandParameter="{Binding EditMessageText}"/>
    </Grid>
    ```

## Files to Review

- [MainPage.xaml](./CS/MainPage.xaml)
- [MainPage.xaml.cs](./CS/MainPage.xaml.cs)
- [ChatViewModel.cs](./CS/ChatViewModel.cs)

## Documentation

* [DXContentPresenter](https://docs.devexpress.com/MAUI/DevExpress.Maui.Core.DXContentPresenter)
* [DXCollectionView](https://docs.devexpress.com/MAUI/DevExpress.Maui.CollectionView.DXCollectionView)
* [DXBorder](https://docs.devexpress.com/MAUI/DevExpress.Maui.Core.DXBorder)
* [SafeKeyboardAreaView](https://docs.devexpress.com/MAUI/DevExpress.Maui.Core.SafeKeyboardAreaView)
* [ChipGroup](https://docs.devexpress.com/MAUI/DevExpress.Maui.Editors.ChipGroup)
* [TextEdit](https://docs.devexpress.com/MAUI/DevExpress.Maui.Editors.TextEdit)

## More Examples

* [DevExpress .NET MAUI Demo Center](https://github.com/DevExpress-Examples/maui-demo-app)
* [Stocks App](https://github.com/DevExpress-Examples/maui-stocks-mini)
* [Data Grid](https://github.com/DevExpress-Examples/maui-data-grid-get-started)
* [Data Form](https://github.com/DevExpress-Examples/maui-data-form-get-started)
* [Data Editors](https://github.com/DevExpress-Examples/maui-editors-get-started)
* [Charts](https://github.com/DevExpress-Examples/maui-charts)
* [Scheduler](https://github.com/DevExpress-Examples/maui-scheduler-get-started)
* [Tab Page](https://github.com/DevExpress-Examples/maui-tab-page-get-started)
* [Tab View](https://github.com/DevExpress-Examples/maui-tab-view-get-started)
* [Drawer Page](https://github.com/DevExpress-Examples/maui-drawer-page-get-started)
* [Drawer View](https://github.com/DevExpress-Examples/maui-drawer-view-get-started)
* [Collection View](https://github.com/DevExpress-Examples/maui-collection-view-get-started)
* [Popup](https://github.com/DevExpress-Examples/maui-popup-get-started)
<!-- feedback -->
## Does this example address your development requirements/objectives?

[<img src="https://www.devexpress.com/support/examples/i/yes-button.svg"/>](https://www.devexpress.com/support/examples/survey.xml?utm_source=github&utm_campaign=maui-chat-app&~~~was_helpful=yes) [<img src="https://www.devexpress.com/support/examples/i/no-button.svg"/>](https://www.devexpress.com/support/examples/survey.xml?utm_source=github&utm_campaign=maui-chat-app&~~~was_helpful=no)

(you will be redirected to DevExpress.com to submit your response)
<!-- feedback end -->
