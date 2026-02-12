---
name: maui-data-binding
description: >-
  Guidance for .NET MAUI XAML data bindings, compiled bindings, value converters,
  binding modes, multi-binding, relative bindings, and MVVM best practices.
---

# .NET MAUI Data Binding

## Binding Modes

| Mode | Direction | Use case |
|------|-----------|----------|
| `OneWay` | Source → Target | Display-only (default for most properties) |
| `TwoWay` | Source ↔ Target | Editable controls (`Entry.Text`, `Switch.IsToggled`) |
| `OneWayToSource` | Target → Source | Read user input without pushing back to UI |
| `OneTime` | Source → Target (once) | Static values; no change tracking overhead |

Set explicitly when the property default doesn't match your intent:

```xml
<Entry Text="{Binding UserName, Mode=TwoWay}" />
<Label Text="{Binding Title, Mode=OneTime}" />
```

## BindingContext and Property Paths

- Every `BindableObject` inherits `BindingContext` from its parent unless explicitly set.
- Property paths support dot notation and indexers:

```xml
<Label Text="{Binding Address.City}" />
<Label Text="{Binding Items[0].Name}" />
```

- Set `BindingContext` in XAML or code-behind:

```xml
<ContentPage xmlns:vm="clr-namespace:MyApp.ViewModels"
             x:DataType="vm:MainViewModel">
    <ContentPage.BindingContext>
        <vm:MainViewModel />
    </ContentPage.BindingContext>
</ContentPage>
```

## Compiled Bindings

Compiled bindings resolve at build time, delivering **8–20× faster** binding resolution than reflection-based bindings.

### Enabling compiled bindings

Declare `x:DataType` on the element or an ancestor:

```xml
<ContentPage x:DataType="vm:MainViewModel">
    <Label Text="{Binding UserName}" />
</ContentPage>
```

### DataTemplate requires its own x:DataType

`DataTemplate` creates a new binding scope. Always redeclare:

```xml
<CollectionView ItemsSource="{Binding People}">
    <CollectionView.ItemTemplate>
        <DataTemplate x:DataType="model:Person">
            <Label Text="{Binding FullName}" />
        </DataTemplate>
    </CollectionView.ItemTemplate>
</CollectionView>
```

### Compiler warnings

| Warning | Meaning |
|---------|---------|
| **XC0022** | Binding path not found on the declared `x:DataType` |
| **XC0023** | Property is not bindable |
| **XC0024** | `x:DataType` type not found |
| **XC0025** | Binding used without `x:DataType` (non-compiled fallback) |

Treat these as errors in CI: `<WarningsAsErrors>XC0022;XC0025</WarningsAsErrors>`.

### .NET 9+ compiled code bindings (SetBinding with lambda)

```csharp
// Fully AOT-safe, no reflection
label.SetBinding(Label.TextProperty,
    static (PersonViewModel vm) => vm.FullName);

// With mode and converter
entry.SetBinding(Entry.TextProperty,
    static (PersonViewModel vm) => vm.Age,
    mode: BindingMode.TwoWay,
    converter: new IntToStringConverter());
```

## IValueConverter

Implement `IValueConverter` with `Convert` (source → target) and `ConvertBack` (target → source):

```csharp
public class IntToBoolConverter : IValueConverter
{
    public object? Convert(object? value, Type targetType,
        object? parameter, CultureInfo culture)
        => value is int i && i != 0;

    public object? ConvertBack(object? value, Type targetType,
        object? parameter, CultureInfo culture)
        => value is true ? 1 : 0;
}
```

### Declaring converters in XAML resources

```xml
<ContentPage.Resources>
    <local:IntToBoolConverter x:Key="IntToBool" />
</ContentPage.Resources>

<Switch IsToggled="{Binding Count, Converter={StaticResource IntToBool}}" />
```

### ConverterParameter

`ConverterParameter` is always passed as a **string**. Parse it inside `Convert`:

```xml
<Label Text="{Binding Score, Converter={StaticResource ThresholdConverter},
              ConverterParameter=50}" />
```

```csharp
int threshold = int.Parse((string)parameter);
```

## StringFormat

Use `Binding.StringFormat` for simple display formatting without a converter:

```xml
<Label Text="{Binding Price, StringFormat='Total: {0:C2}'}" />
<Label Text="{Binding DueDate, StringFormat='{0:MMM dd, yyyy}'}" />
```

> **Note:** Wrap the format string in single quotes when it contains commas or braces.

## Multi-Binding

Combine multiple source values with `IMultiValueConverter`:

```xml
<Label>
    <Label.Text>
        <MultiBinding Converter="{StaticResource FullNameConverter}"
                      StringFormat="{}{0}">
            <Binding Path="FirstName" />
            <Binding Path="LastName" />
        </MultiBinding>
    </Label.Text>
</Label>
```

```csharp
public class FullNameConverter : IMultiValueConverter
{
    public object Convert(object[] values, Type targetType,
        object parameter, CultureInfo culture)
    {
        if (values.Length == 2 && values[0] is string first && values[1] is string last)
            return $"{first} {last}";
        return string.Empty;
    }

    public object[] ConvertBack(object value, Type[] targetTypes,
        object parameter, CultureInfo culture)
        => throw new NotSupportedException();
}
```

## Relative Bindings

| Source | Syntax | Use case |
|--------|--------|----------|
| Self | `{Binding Source={RelativeSource Self}, Path=Width}` | Bind to own properties |
| Ancestor | `{Binding Source={RelativeSource AncestorType={x:Type vm:ParentVM}}, Path=Title}` | Reach parent BindingContext |
| TemplatedParent | `{Binding Source={RelativeSource TemplatedParent}, Path=Padding}` | Inside ControlTemplate |

```xml
<!-- Square box: Height = Width -->
<BoxView WidthRequest="100"
         HeightRequest="{Binding Source={RelativeSource Self}, Path=WidthRequest}" />
```

## Binding Fallbacks

- **FallbackValue** – used when the binding path cannot be resolved or the converter throws.
- **TargetNullValue** – used when the bound value is `null`.

```xml
<Label Text="{Binding MiddleName, TargetNullValue='(none)',
              FallbackValue='unavailable'}" />
<Image Source="{Binding AvatarUrl, TargetNullValue='default_avatar.png'}" />
```

## Threading

MAUI **automatically marshals** property-change notifications to the UI thread. You can raise `PropertyChanged` from any thread; the binding engine dispatches the update to the main thread.

```csharp
// Safe from a background thread
await Task.Run(() =>
{
    Items = LoadData();          // Raises PropertyChanged
    OnPropertyChanged(nameof(Items));
});
```

> **Caveat:** Direct `ObservableCollection` mutations (Add/Remove) from background threads may still require `MainThread.BeginInvokeOnMainThread`.

## Performance

- **Reflection overhead:** Non-compiled bindings use reflection to resolve paths at runtime—measurably slower on large lists and startup.
- **Compiled bindings** eliminate reflection; always prefer them.
- **NativeAOT / trimming:** Reflection-based bindings may break under trimming. Compiled bindings (XAML `x:DataType` or code `SetBinding` with lambdas) are trimmer- and AOT-safe.
- Avoid complex converter chains in hot paths; pre-compute values in the ViewModel instead.
- Use `OneTime` mode for truly static data to skip change-tracking registration.
