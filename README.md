# Flutter_doc_CokBK_NAV_Return_data_from_a_screen
 https://docs.flutter.dev/cookbook/navigation/returning-data
Return data from a screen
=========================

1.  [Cookbook](https://docs.flutter.dev/cookbook)
2.  [Navigation](https://docs.flutter.dev/cookbook/navigation)
3.  [Return data from a screen](https://docs.flutter.dev/cookbook/navigation/returning-data)

In some cases, you might want to return data from a new screen. For example, say you push a new screen that presents two options to a user. When the user taps an option, you want to inform the first screen of the user's selection so that it can act on that information.

You can do this with the [`Navigator.pop()`](https://api.flutter.dev/flutter/widgets/Navigator/pop.html) method using the following steps:

1.  Define the home screen
2.  Add a button that launches the selection screen
3.  Show the selection screen with two buttons
4.  When a button is tapped, close the selection screen
5.  Show a snackbar on the home screen with the selection

[](https://docs.flutter.dev/cookbook/navigation/returning-data#1-define-the-home-screen)1\. Define the home screen
------------------------------------------------------------------------------------------------------------------

The home screen displays a button. When tapped, it launches the selection screen.

content_copy

```
class HomeScreen extends StatelessWidget {
  const HomeScreen({super.key});

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: const Text('Returning Data Demo'),
      ),
      // Create the SelectionButton widget in the next step.
      body: const Center(
        child: SelectionButton(),
      ),
    );
  }
}
```

[](https://docs.flutter.dev/cookbook/navigation/returning-data#2-add-a-button-that-launches-the-selection-screen)2\. Add a button that launches the selection screen
--------------------------------------------------------------------------------------------------------------------------------------------------------------------

Now, create the SelectionButton, which does the following:

-   Launches the SelectionScreen when it's tapped.
-   Waits for the SelectionScreen to return a result.

content_copy

```
class SelectionButton extends StatefulWidget {
  const SelectionButton({super.key});

  @override
  State<SelectionButton> createState() => _SelectionButtonState();
}

class _SelectionButtonState extends State<SelectionButton> {
  @override
  Widget build(BuildContext context) {
    return ElevatedButton(
      onPressed: () {
        _navigateAndDisplaySelection(context);
      },
      child: const Text('Pick an option, any option!'),
    );
  }

  Future<void> _navigateAndDisplaySelection(BuildContext context) async {
    // Navigator.push returns a Future that completes after calling
    // Navigator.pop on the Selection Screen.
    final result = await Navigator.push(
      context,
      // Create the SelectionScreen in the next step.
      MaterialPageRoute(builder: (context) => const SelectionScreen()),
    );
  }
}
```

[](https://docs.flutter.dev/cookbook/navigation/returning-data#3-show-the-selection-screen-with-two-buttons)3\. Show the selection screen with two buttons
----------------------------------------------------------------------------------------------------------------------------------------------------------

Now, build a selection screen that contains two buttons. When a user taps a button, that app closes the selection screen and lets the home screen know which button was tapped.

This step defines the UI. The next step adds code to return data.

content_copy

```
class SelectionScreen extends StatelessWidget {
  const SelectionScreen({super.key});

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: const Text('Pick an option'),
      ),
      body: Center(
        child: Column(
          mainAxisAlignment: MainAxisAlignment.center,
          children: [
            Padding(
              padding: const EdgeInsets.all(8),
              child: ElevatedButton(
                onPressed: () {
                  // Pop here with "Yep"...
                },
                child: const Text('Yep!'),
              ),
            ),
            Padding(
              padding: const EdgeInsets.all(8),
              child: ElevatedButton(
                onPressed: () {
                  // Pop here with "Nope"...
                },
                child: const Text('Nope.'),
              ),
            )
          ],
        ),
      ),
    );
  }
}
```

[](https://docs.flutter.dev/cookbook/navigation/returning-data#4-when-a-button-is-tapped-close-the-selection-screen)4\. When a button is tapped, close the selection screen
---------------------------------------------------------------------------------------------------------------------------------------------------------------------------

Now, update the `onPressed()` callback for both of the buttons. To return data to the first screen, use the [`Navigator.pop()`](https://api.flutter.dev/flutter/widgets/Navigator/pop.html) method, which accepts an optional second argument called `result`. Any result is returned to the `Future` in the SelectionButton.

### [](https://docs.flutter.dev/cookbook/navigation/returning-data#yep-button)Yep button

content_copy

```
ElevatedButton(
  onPressed: () {
    // Close the screen and return "Yep!" as the result.
    Navigator.pop(context, 'Yep!');
  },
  child: const Text('Yep!'),
)
```

### [](https://docs.flutter.dev/cookbook/navigation/returning-data#nope-button)Nope button

content_copy

```
ElevatedButton(
  onPressed: () {
    // Close the screen and return "Nope." as the result.
    Navigator.pop(context, 'Nope.');
  },
  child: const Text('Nope.'),
)
```

[](https://docs.flutter.dev/cookbook/navigation/returning-data#5-show-a-snackbar-on-the-home-screen-with-the-selection)5\. Show a snackbar on the home screen with the selection
--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

Now that you're launching a selection screen and awaiting the result, you'll want to do something with the information that's returned.

In this case, show a snackbar displaying the result by using the `_navigateAndDisplaySelection()` method in `SelectionButton`:

content_copy

```
// A method that launches the SelectionScreen and awaits the result from
// Navigator.pop.
Future<void> _navigateAndDisplaySelection(BuildContext context) async {
  // Navigator.push returns a Future that completes after calling
  // Navigator.pop on the Selection Screen.
  final result = await Navigator.push(
    context,
    MaterialPageRoute(builder: (context) => const SelectionScreen()),
  );

  // When a BuildContext is used from a StatefulWidget, the mounted property
  // must be checked after an asynchronous gap.
  if (!mounted) return;

  // After the Selection Screen returns a result, hide any previous snackbars
  // and show the new result.
  ScaffoldMessenger.of(context)
    ..removeCurrentSnackBar()
    ..showSnackBar(SnackBar(content: Text('$result')));
}
```

`\
`
