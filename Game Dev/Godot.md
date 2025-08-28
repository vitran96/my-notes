[[Design pattern#Game programming pattern]]

# Architecture

## GC
GC in Godot is Reference Counting

# Basic
Nodes are fundamental building block for creating game

- A Node can perform various things
- Node has a name
- Node has editable properties
- Node can receive a callback to process every frame
- Node can be extended
- Node can be added to another Node

Scenes: a scene is composed of a group of nodes organized hierarchically (TREE)

- A scene always has 1 root node
- Can be saved to disk and loaded back
- Can be instanced

⇒ Running a game means running a scene

# Convention

- Use snake_case for folder and file names (with the exception of C# scripts). This sidesteps case sensitivity issues that can crop up after exporting a project on Windows. C# scripts are an exception to this rule, as the convention is to name them after the class name which should be in PascalCase.
- Use PascalCase for node names, as this matches built-in node casing.
- In general, keep third-party resources in a top-level addons/ folder, even if they aren't editor plugins. This makes it easier to track which files are third-party. There are some exceptions to this rule; for instance, if you use third-party game assets for a character, it makes more sense to include them within the same folder as the character scenes and scripts.

## Styles
Link: [https://docs.godotengine.org/en/stable/getting_started/scripting/gdscript/gdscript_styleguide.html](https://docs.godotengine.org/en/stable/getting_started/scripting/gdscript/gdscript_styleguide.html)

File NAME

- Use snake_case for file names. For named classes, convert the PascalCase class name to snake_case:

Classes and nodes NAME

- Use PascalCase for class and node names:
- Also use PascalCase when loading a class into a constant or a variable:

Functions and variables NAME

- Use snake_case to name functions and variables:

Signals NAME

- Use the past tense to name signals:

Constants and enums NAME

- Write constants with CONSTANT_CASE, that is to say in all caps with an underscore (_) to separate words:

Code order:

1. tool
2. class_name
3. extends
4. # docstring
    
5. signals
6. enums
7. constants
8. exported variables
9. public variables
10. private variables
11. onready variables
12. optional built-in virtual _init method
13. built-in virtual _ready method
14. remaining built-in virtual methods
15. public methods
16. private methods

Class declaration

- If the code is meant to run in the editor, place the tool keyword on the first line of the script.
- Follow with the class_name if necessary. You can turn a GDScript file into a global type in your project using this feature. For more information, see GDScript basics.
- Then, add the extends keyword if the class extends a built-in type.

Encoding and special characters

- Use line feed (LF) characters to break lines, not CRLF or CR. (editor default)
- Use one line feed character at the end of each file. (editor default)
- Use UTF-8 encoding without a byte order mark. (editor default)
- Use Tabs instead of spaces for indentation. (editor default)

Indentation

- Each indent level should be one greater than the block containing it.

Trailing comma

- Use a trailing comma on the last line in arrays, dictionaries, and enums. This results in easier refactoring and better diffs in version control as the last line doesn't need to be modified when adding new elements.

Blank lines

- Surround functions and class definitions with two blank lines.

Line length

- Keep individual lines of code under 100 characters.
- If you can, try to keep lines under 80 characters. This helps to read the code on small displays and with two scripts opened side-by-side in an external text editor. For example, when looking at a differential revision.

One statement per line

- Never combine multiple statements on a single line. No, C programmers, not even with a single line conditional statement.

Avoid unnecessary parentheses

- Avoid parentheses in expressions and conditional statements. Unless necessary for order of operations, they only reduce readability.

Boolean operators: Prefer the plain English versions of boolean operators, as they are the most accessible:

- Use and instead of &&.
- Use or instead of ||.

Comment spacing

- Regular comments should start with a space, but not code that you comment out. This helps differentiate text comments from disabled code.

Whitespace

- Always use one space around operators and after commas. Also, avoid extra spaces in dictionary references and function calls.

Quotes

- Use double quotes unless single quotes make it possible to escape fewer characters in a given string. See the examples below:

Numbers

- Don't omit the leading or trailing zero in floating-point numbers. Otherwise, this makes them less readable and harder to distinguish from integers at a glance.

## Project structure
Link: [https://www.braindead.bzh/entry/creating-a-game-with-godot-engine-ep-2-project-organization](https://www.braindead.bzh/entry/creating-a-game-with-godot-engine-ep-2-project-organization)

Folder:

- **root project**: contains all the folders and files of the project. Should be named after the project name. Should be the root of your local VCS repository, if you choose to use a VCS (highly recommended, with a service like GitHub or Bitbucket).
- **assets**: contains all the original medias (sprites, textures, 3D models, music, sound effects, ...) before importing them into Godot as resources. It is important to separate this from your source code for many reasons: most of software have their own project files that you have to export first, like PSD to PNG; Godot has its own importing formats, like for textures; Some media are build from many files you would want to store, and version, separately. It is also a good idea to split it into subfolders per media type and/or media, example: /assets/music/intro-music/ or /assets/sprites/player/. In the demo project, you can see an Inkscape project file called master-1.svg which is used to export multiple PNGs. I didn't used subfolder because it is a very basic project.
- **releases**: contains your project as it will be distributed to the end users. It is the export result of your Godot project. Should contains subfolders for each platform you are planning to support.
- **src**: contains the source code and resources of your Godot project. Should be the root of your Godot project, i.e. contains the "engine.cfg" file. The following subfolders are categories of entity where you will store the different element of your project. They should cover most of the needs for a basic project.
- **actors**: contains an actor per subfolder. An actor is a "smart" entity, like the player character or enemies with some sort of AI.
- **areas**: contains an area per subfolder. An area is a static element which is used to create levels' map. For most of 2D games an area would be a Tile Map.
- **fonts**: contains fonts used to display text in your project. It is a good idea to store them in one location to avoid duplication. Protip: try to limit the number of fonts you want to use.
- **levels**: contains a level per subfolder. A level is where you are assembling all the other elements to create gameplay.
- **lights**: the way 2D lights are working in Godot is texture based, so a good idea is to store those textures in one location to avoid duplication.
- **objects**: contains an object per subfolder: An object is an interactive entity, like a door or a chest. Most of the time, the interaction is done through an actor. An object is also something which can move around, like a ball.
- **screens**: contains a screen per subfolder: A screen represents a mode your project can be in, like main menu, settings or game. Splitting your project into screens is also an easy way to implement multiple User Interfaces (UI).

Rule:

- Global elements, like the project entry point or singletons, should be placed directly inside the src folder. This will be discuss more in details in the next article.
- When creating an element a subfolder should be added to the proper category and named after the element it contains, like player, ball_emitter or level1. Inside this subfolder you should create a scene and attach a GD script to this scene, in order to get the basic file structure to start implementing the element.
- It is important to use the same name when creating the element scene and GD script, like actor_player.tscn or actor_player.gd.
- It is a good practice to put the category inside the scene and GD script filename using this format: <category>_<name>.<ext>. This way when the project becomes larger, and multiple files are open in the scene editor or script editor, it makes it easier to keep track of what you are editing.
- It is a good idea to only use lower case and alphanumeric ASCII characters. Underscore, '_', should be used as a separator, no space. This way you insure a broader compatibility and possibly avoid some weird errors later on.
- If you want to have common features for elements of one category, you can create a GD script directly inside the category folder, using abstract in the name to remember that it is not to be used directly, like abstract_<category>.gd. Then using the inheritance feature of GD script, you can make each element of the category inherit from it using extends "../abstract_<category>.gd".