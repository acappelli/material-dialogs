# Material Dialogs

The code you see below is also found in the sample project. You can download a APK of the sample here: https://github.com/afollestad/material-dialogs/blob/master/sample/sample.apk. The sample's also available on Google Play: https://play.google.com/store/apps/details?id=com.afollestad.materialdialogssample.

---

### What's New

###### Version 0.3.2

> 1. Large performance improvements for list dialogs, thanks [hzsweers](https://github.com/hzsweers)! The item processor API was switched with custom list adapters, see the sample for an example.
> 2. Other padding and logic fixes.

###### Version 0.3.1

> 1. Global theming! A single attribute can be added to your Activity theme to make all dialogs dark. See the [Global Theming](#global-theming) section below.

###### Version 0.3.0

> 1. `MaterialDialogCompat` allows easy migration from use of `AlertDialog` (see below).
> 2. Convenience `show()` method in Builder, to skip call to `build()`.
> 3. Various important fixes from pull requests and the maintainer.

###### Version 0.2.0

> 1. Action buttons must be explicitly shown by setting text to them. The buttons will be hidden in any dialog type if no text is passed for them. This also allows you to display neutral or negative action buttons individually without relying on positive text. 
> 2. List dialogs now use CharSequence arrays rather than String arrays.
> 3. Other bug fixes are included.

---

### Gradle Dependency (jCenter)

Easily reference the library in your Android projects using this dependency in your module's `build.gradle` file:

```Groovy
dependencies {
    compile 'com.afollestad:material-dialogs:0.3.2'
}
```

***Make sure*** you're using the jCenter repository, Android Studio uses this repository by default.

Check back here frequently for version updates.

---

### Basic Dialog

Here's a basic example that mimics the dialog you see on Google's Material design guidelines
(here: http://www.google.com/design/spec/components/dialogs.html#dialogs-usage). Note that you can
always substitute literal strings and string resources for methods that take strings, the same goes
for color resources (e.g. `titleColor` and `titleColorRes`).

```java
new MaterialDialog.Builder(this)
        .title("Use Google's Location Services?")
        .content("Let Google help apps determine location. This means sending anonymous location data to Google, even when no apps are running.")
        .theme(Theme.LIGHT)  // the default is light, so you don't need this line
        .positiveText("Agree")
        .negativeText("Disagree")
        .show();
```

On Lollipop (API 21) or if you use AppCompat, the Material dialog will automatically match the `positiveColor`
(which is used on the positive action button) to the `colorAccent` attribute of your styles.xml theme.

If the content is long enough, it will become scrollable and a divider will be displayde above the action buttons.

---

### Migration from AlertDialogs

If you're migrating old dialogs you could use ```MaterialDialogCompat```. You need change imports and replace ```AlertDialog.Builder``` with ```MaterialDialogCompat.Builder```:

```java
MaterialDialogCompat.Builder dialogBuilder = new MaterialDialogCompat.Builder(context);
dialogBuilder.setMessage(messageId);
dialogBuilder.setTitle(titleId);
dialogBuilder.setNegativeButton(R.string.OK, new DialogInterface.OnClickListener() {
    @Override
    public void onClick(DialogInterface dialog, int which) {
        dialog.dismiss();
        if (listener != null) {
            listener.onCancel(dialog);
        }
    }
});
dialogBuilder.create().show();
```

But it's highly recommended to use original ```MaterialDialog``` API for new usages.

---

### Displaying an Icon

MaterialDialog supports the display of an icon just like the stock AlertDialog; it will go to the left of the title.

```java
Drawable d = // ... get from somewhere...
new MaterialDialog.Builder(this)
        .title("Use Google's Location Services?")
        .content("Let Google help apps determine location. This means sending anonymous location data to Google, even when no apps are running.")
        .positiveText("Agree")
        .icon(d)
        .show();
```

You can substitute a `Drawable` instance of a drawable resource ID or attribute ID.

---

### Stacked Action Buttons

If you have multiple action buttons that together are too wide to fit on one line, the dialog will stack the
buttons to be vertically orientated.

```java
new MaterialDialog.Builder(this)
        .title("Use Google's Location Services?")
        .content("Let Google help apps determine location. This means sending anonymous location data to Google, even when no apps are running.")
        .positiveText("Turn on speed boost right now!")
        .negativeText("No thanks")
        .show();
```

If you have multiple action buttons, and together they're too long to fit on one line, the dialog will stack
 the buttons automatically.

---

### Neutral Action Button

You can specify neutral text in addition to the positive and negative text. It will show the neutral
action on the far left.

```java
new MaterialDialog.Builder(this)
        .title("Use Google's Location Services?")
        .content("Let Google help apps determine location. This means sending anonymous location data to Google, even when no apps are running.")
        .positiveText("Agree")
        .negativeText("Disagree")
        .neutralText("More info")
        .show();
```

---

### Callbacks

To know when the user selects an action button, you set a callback. There's three variations of the callback for the action buttons:

```java
new MaterialDialog.Builder(this)
        .callback(new MaterialDialog.SimpleCallback() {
            @Override
            public void onPositive(MaterialDialog dialog) {
            }
        });

new MaterialDialog.Builder(this)
        .callback(new MaterialDialog.Callback() {
            @Override
            public void onPositive(MaterialDialog dialog) {
            }

            @Override
            public void onNegative(MaterialDialog dialog) {
            }
        });

new MaterialDialog.Builder(this)
        .callback(new MaterialDialog.FullCallback() {
            @Override
            public void onPositive(MaterialDialog dialog) {
            }

            @Override
            public void onNegative(MaterialDialog dialog) {
            }

            @Override
            public void onNeutral(MaterialDialog dialog) {
            }
        });
```

If `autoDismiss` is turned off, then you must manually dismiss the dialog in these callbacks. Auto dismiss is on by default.

---

### List Dialogs

Creating a list dialog only requires passing in an array of strings. The callback (`itemsCallback`) is
also very simple.

```java
new MaterialDialog.Builder(this)
        .title("Social Networks")
        .items(new String[]{"Twitter", "Google+", "Instagram", "Facebook"})
        .itemsCallback(new MaterialDialog.ListCallback() {
            @Override
            public void onSelection(MaterialDialog dialog, View view, int which, CharSequence text) {
            }
        })
        .show();
```

If `autoDismiss` is turned off, then you must manually dismiss the dialog in the callback. Auto dismiss is on by default.
You can pass `positiveText()` or the other action buttons to the builder to force it to display the action buttons
below your list, however this is only useful in some specific cases.

---

### Single Choice List Dialogs

Single choice list dialogs are almost identical to regular list dialogs. The only difference is that
you use `itemsCallbackSingleChoice` to set a callback rather than `itemsCallback`. That signals the dialog to
display radio buttons next to list items.

This also makes it so that an action button has to be pressed, tapping a list item won't dismiss the dialog.
Note that this means the positive action button callback will be overridden if you specify one.

```java
new MaterialDialog.Builder(this)
        .title("Social Networks")
        .items(new String[]{"Twitter", "Google+", "Instagram", "Facebook"})
        .itemsCallbackSingleChoice(-1, new MaterialDialog.ListCallback() {
            @Override
            public void onSelection(MaterialDialog dialog, View view, int which, CharSequence text) {
            }
        })
        .positiveText("Choose")
        .show();
```

If you want to preselect an item, pass an index 0 or greater in place of -1 in `itemsCallbackSingleChoice()`.
If `autoDismiss` is turned off, then you must manually dismiss the dialog in the callback. Auto dismiss is on by default.
When `positiveText()` is not used, the callback will be called every time you select an item since no action is
available to press, without the dialog being dismissed. You can pass `positiveText()` or the other action
buttons to the builder to force it to display the action buttons below your list, however this is only
useful in some specific cases.

---

### Multi Choice List Dialogs

Multiple choice list dialogs are almost identical to regular list dialogs. The only difference is that
you use `itemsCallbackMultiChoice` to set a callback rather than `itemsCallback`. That signals the dialog to
display check boxes next to list items, and the callback can return multiple selections.

This also makes it so that an action button has to be pressed, tapping a list item won't dismiss the dialog.
Note that this means the positive action button callback will be overridden if you specify one.

```java
new MaterialDialog.Builder(this)
        .title("Social Networks")
        .items(new String[]{"Twitter", "Google+", "Instagram", "Facebook"})
        .itemsCallbackMultiChoice(null, new MaterialDialog.ListCallbackMulti() {
            @Override
            public void onSelection(MaterialDialog dialog, Integer[] which, CharSequence[] text) {
            }
        })
        .positiveText("Choose")
        .show();
```

If you want to preselect item(s), pass an array of indices in place of null in `itemsCallbackSingleChoice()`.
For an example, `new Integer[] { 2, 5 }`. If `autoDismiss` is turned off, then you must manually
dismiss the dialog in the callback. Auto dismiss is on by default. When action buttons are not added, the
callback will be called every time you select an item since no action is available to press, without the
dialog being dismissed. You can pass `positiveText()` or the other action buttons to the builder to force
it to display the action buttons below your list, however this is only useful in some specific cases.

---

### Custom List Dialogs

Like Android's native dialogs, you can also pass in your own adapter via `.adapter()` to customize
example how you want your list to work. You also have access to the dialog's list via `getListView()` method.

```java
MaterialDialog dialog = new MaterialDialog.Builder(this)
        .title(R.string.socialNetworks)
        .adapter(new ButtonItemAdapter(this, R.array.socialNetworks))
        .build();

ListView listView = dialog.getListView();
if (listView != null) {
    listView.setOnItemClickListener(new AdapterView.OnItemClickListener() {
        @Override
        public void onItemClick(AdapterView<?> parent, View view, int position, long id) {
            Toast.makeText(MainActivity.this, "Clicked item " + position, Toast.LENGTH_SHORT).show();
        }
    });
}

dialog.show();
```

---

### Custom Views

Custom views are very easy to implement. To match the dialog show here: http://www.google.com/design/spec/components/dialogs.html#dialogs-behavior

```java
new MaterialDialog.Builder(this)
        .title("Google Wifi")
        .customView(R.layout.custom_view)
        .positiveText("Connect")
        .positiveColor(Color.parseColor("#03a9f4"))
        .build()
        .show();
```

Where `custom_view.xml` contains a LinearLayout of TextViews, an EditText, and a CheckBox. You'll see in
the sample project that you don't need to add padding to the edges of your custom view, the dialog
already does that. Note that your custom view's top and bottom
margins will be overrided; if your custom view is a ViewGroup (e.g. a LinearLayout or RelativeLayout),
then the first and last child's top and bottom will be overided.

`MaterialDialog` inserts your view into a `ScrollView` and displays a divider above the action buttons,
so don't wrap your custom view in a scroll view and don't worry about it being too long or needing a divider.
However, you should avoid making any content that wouldn't belong in a dialog because of its size.

You can pass `positiveText()` or the other action buttons to the builder to force it to display the action buttons
below your list, however this is only useful in some specific cases.

---

### Theming

Before Lollipop, theming AlertDialogs was basically impossible without using reflection and custom drawables.
Since KitKat, Android became more color neutral but AlertDialogs continued to use Holo Blue for the title and
title divider. Lollipop has improved even more, with no colors in the dialog by default other than the action
buttons. This library makes theming even easier. Here's a basic example:

```java
final int materialRed500 = Color.parseColor("#D50000");
new MaterialDialog.Builder(this)
        .title("Use Google's Location Services?")
        .content("Let Google help apps determine location. This means sending anonymous location data to Google, even when no apps are running.")
        .positiveText("Agree")
        .negativeText("Disagree")
        .positiveColor(materialRed500)
        .negativeColor(materialRed500)
        .neutralColor(materialRed500)
        .titleAlignment(Alignment.CENTER)
        .titleColor(materialRed500)
        .theme(Theme.DARK)
        .show();
```

To see more colors that fit the Material design palette, see this page: http://www.google.com/design/spec/style/color.html#color-color-palette

---

### Global Theming

If you don't want to manually make calls to `theme()` everytime you construct a dialog, you can put a single attribute in
your Activity themes that makes all dialogs dark.

```xml
<style name="AppTheme" parent="Theme.AppCompat.Light.DarkActionBar">

    <item name="md_dark_theme">true</item>

</style>
```

The action button color is also derived from the `android:colorAccent` attribute of the Material theme,
or `colorAccent` attribute of the AppCompat Material theme as seen in the sample project. Manually setting
the color will override that behavior.

---

### Misc

If you need to access a View in the custom view set to a MaterialDialog, you can use `getCustomView()` of
MaterialDialog. This is especially useful if you pass a layout resource to the Builder.

```java
MaterialDialog dialog = //... initialization via the builder ...
View view = dialog.getCustomView();
```

If you want to get a reference to the title frame (which contains the icon and title, e.g. to change visibility):

```java
MaterialDialog dialog = //... initialization via the builder ...
TextView title = dialog.getTitleFrame();
```

If you want to get a reference to one of the dialog action buttons (e.g. to enable or disable buttons):

```java
MaterialDialog dialog = //... initialization via the builder ...
View negative = dialog.getActionButton(DialogAction.NEGATIVE);
View neutral = dialog.getActionButton(DialogAction.NEUTRAL);
View positive = dialog.getActionButton(DialogAction.POSITIVE);
```

If you want to update the title of a dialog action button (you can pass a string resource ID in place of the literal string, too):

```java
MaterialDialog dialog = //... initialization via the builder ...
dialog.setActionButton(DialogAction.NEGATIVE, "New Title");
```

If you don't want the dialog to automatically be dismissed when an action button is pressed or when
the user selects a list item:

```java
MaterialDialog dialog new MaterialDialog.Builder(this)
        // ... other initialization
        .autoDismiss(false)
        .show();
```

To customize fonts:

```java
Typeface titleAndActions = // ... initialize
Typeface contentAndListItems = // ... initialize
MaterialDialog dialog new MaterialDialog.Builder(this)
        // ... other initialization
        .typeface(titleAndActions, contentAndListItems)
        .show();
```
