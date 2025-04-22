# Crossword Generator

A Flutter library for generating and displaying interactive crossword puzzles. Customize the appearance and behavior of crossword grids, and easily integrate them into your Flutter applications.

## Features

- Generate crossword layouts from a list of words and descriptions.
- Interactive crossword grid with cell selection and navigation.
- Customizable styles for cells, including colors and text styles.
- Reveal letters or entire words.
- Automatically validate and highlight completed words.
- Navigate between words using buttons.
- Expose completion event to notify when the crossword is fully completed.

## Installation

Add the following to your `pubspec.yaml` file:

```yaml
dependencies:
  crossword_generator: ^1.0.0
```

Then run `flutter pub get` to install the package.

## Usage

### Basic Example

Here's a basic example of how to use the `CrosswordWidget`:

```dart
import 'package:crossword_generator/crossword_generator.dart';
import 'package:flutter/material.dart';

void main() {
  runApp(CrosswordApp());
}

class CrosswordApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Crossword Generator',
      theme: ThemeData(
        primarySwatch: Colors.blue,
      ),
      home: CrosswordHomePage(),
    );
  }
}

class CrosswordHomePage extends StatefulWidget {
  @override
  _CrosswordHomePageState createState() => _CrosswordHomePageState();
}

class _CrosswordHomePageState extends State<CrosswordHomePage> {
  final TextEditingController _controller = TextEditingController();
  Function? _revealCurrentCellLetter;

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text('Crossword Generator'),
      ),
      body: Padding(
        padding: const EdgeInsets.all(16.0),
        child: SingleChildScrollView(
          child: Column(
            children: [
              TextField(
                controller: _controller,
                maxLines: 5,
                decoration: InputDecoration(
                  hintText: 'Enter words on separate lines...',
                  border: OutlineInputBorder(),
                ),
              ),
              SizedBox(height: 20),
              ElevatedButton(
                onPressed: () {
                  if (_controller.text.isNotEmpty) {
                    List<String> wordList = _controller.text.split(RegExp(r'[ \r\n,;:-]+')).where((word) => word.isNotEmpty).toList();
                    List<Map<String, dynamic>> inputJson = wordList.map((word) => {'answer': word.toLowerCase(), 'description': 'Description for $word'}).toList();

                    Navigator.push(
                      context,
                      MaterialPageRoute(
                        builder: (context) => Scaffold(
                          appBar: AppBar(
                            title: Text('Crossword Puzzle'),
                            actions: [
                              IconButton(
                                icon: Icon(Icons.help),
                                onPressed: () {
                                  if (_revealCurrentCellLetter != null) {
                                    _revealCurrentCellLetter!();
                                  }
                                },
                              ),
                            ],
                          ),
                          body: CrosswordWidget(
                            words: inputJson,
                            style: CrosswordStyle(
                              currentCellColor: Color.fromARGB(255, 84, 255, 129),
                              wordHighlightColor: Color.fromARGB(255, 200, 255, 200),
                              wordCompleteColor: Color.fromARGB(255, 255, 249, 196),
                              cellTextStyle: TextStyle(fontWeight: FontWeight.bold, color: Colors.black),
                              descriptionButtonStyle: ElevatedButton.styleFrom(
                                backgroundColor: Colors.blue,
                              ),
                              cellBuilder: (context, cell, isSelected, isHighlighted, isCompleted) {
                                return Container(
                                  width: 30,
                                  height: 30,
                                  alignment: Alignment.center,
                                  margin: EdgeInsets.all(1),
                                  decoration: BoxDecoration(
                                    border: Border.all(color: Colors.black),
                                    color: isCompleted
                                        ? Color.fromARGB(255, 255, 249, 196)
                                        : isSelected
                                            ? Color.fromARGB(255, 84, 255, 129)
                                            : isHighlighted
                                                ? Color.fromARGB(255, 200, 255, 200)
                                                : Colors.white,
                                  ),
                                  child: Text(
                                    cell.toUpperCase(),
                                    style: TextStyle(fontWeight: FontWeight.bold, color: Colors.black),
                                  ),
                                );
                              },
                            ),
                            onRevealCurrentCellLetter: (revealCurrentCellLetter) {
                              _revealCurrentCellLetter = revealCurrentCellLetter;
                            },
                            onCrosswordCompleted: () {
                              // Handle crossword completion
                              showDialog(
                                context: context,
                                builder: (context) {
                                  return AlertDialog(
                                    title: Text('Congratulations!'),
                                    content: Text('You have completed the crossword puzzle.'),
                                    actions: [
                                      TextButton(
                                        child: Text('OK'),
                                        onPressed: () {
                                          Navigator.of(context).pop();
                                        },
                                      ),
                                    ],
                                  );
                                },
                              );
                            },
                          ),
                        ),
                      ),
                    );
                  }
                },
                child: Text('Generate Layout'),
              ),
            ],
          ),
        ),
      ),
    );
  }
}
```

### Customizing Styles

You can customize the appearance of the crossword grid by providing a `CrosswordStyle` object to the `CrosswordWidget`. Here's an example:

```dart
CrosswordWidget(
  words: inputJson,
  style: CrosswordStyle(
    currentCellColor: Colors.blue,
    wordHighlightColor: Colors.yellow,
    wordCompleteColor: Colors.green,
    cellTextStyle: TextStyle(fontWeight: FontWeight.bold, color: Colors.black),
    descriptionButtonStyle: ElevatedButton.styleFrom(
      backgroundColor: Colors.blue,
    ),
    cellBuilder: (context, cell, isSelected, isHighlighted, isCompleted) {
      return Container(
        width: 30,
        height: 30,
        alignment: Alignment.center,
        margin: EdgeInsets.all(1),
        decoration: BoxDecoration(
          border: Border.all(color: Colors.black),
          color: isCompleted
              ? Colors.green
              : isSelected
                  ? Colors.blue
                  : isHighlighted
                      ? Colors.yellow
                      : Colors.white,
        ),
        child: Text(
          cell.toUpperCase(),
          style: TextStyle(fontWeight: FontWeight.bold, color: Colors.black),
        ),
      );
    },
  ),
  onRevealCurrentCellLetter: (revealCurrentCellLetter) {
    _revealCurrentCellLetter = revealCurrentCellLetter;
  },
  onCrosswordCompleted: () {
    // Handle crossword completion
    showDialog(
      context: context,
      builder: (context) {
        return AlertDialog(
          title: Text('Congratulations!'),
          content: Text('You have completed the crossword puzzle.'),
          actions: [
            TextButton(
              child: Text('OK'),
              onPressed: () {
                Navigator.of(context).pop();
              },
            ),
          ],
        );
      },
    );
  },
);
```

### UI Example

Here's an example of the UI generated by the `CrosswordWidget`:

<img src="https://github.com/speakm/crossword-generator/blob/main/cw-example.jpg?raw=true" alt="Crossword Example" width="400" >

### Methods

- `revealCurrentCellLetter()`: Reveals the letter in the currently selected cell.

### Acknowledgements

Special thanks to [Michael Wehar](https://github.com/MichaelWehar/Crossword-Layout-Generator/tree/master) for the original layout generation code in JavaScript, which inspired the layout generation logic in this library.

### License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

### Contributions

Contributions are welcome! Please open an issue or submit a pull request.

### Contact

For any questions or suggestions, feel free to contact at [support+crossword-generator@speakm.com].
