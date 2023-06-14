# Flutter Development Specification

# TL;DR

```
> Terms
**PROHIBIT** : **P**
**MUST** : **M**
**TRY TO** : **T**
**RECOMMEND** : **R**
**REQUIRED** : **Req**
**TRY TO** : **Try**
**TRY MY BEST** : **Try**
```

- **PROHIBIT** Any errors occur;
- **PROHIBIT** Do not write `new` , in Dart 2.x it is no longer recommended to continue to use the `new` keyword;
- **MUST** Indent to 2 spaces；
- **MUST** Use `flutter format` , `dartfmt` or `dart forma`t to format the code;
- **MUST** Use the `analysis_options.yaml` （[File](https://github.com/flutter/flutter/blob/master/analysis_options.yaml)） under the official Flutter repository for basic code specification constraints;
- **MUST** Ending commas must be used for splitting in long codes and nesting to prevent more than 80 words or `))))`;
- **TRY TO** Avoid any warnings；
- **TRY TO** Avoid more than 80 characters on a line. If exceeded, check for `))))`;
- **TRY TO**Avoid directly modifying the source code of Flutter or dependent package source code;
- **TRY TO** Keep all packages up-to-date as project schedules allow;
- **RECOMMEND** Keep the Flutter SDK at the latest **Stable Version** as project schedules allow;\

In addition, the following are the details of various specifications

# 1. File class specification

## 1.1 Naming convention
> For a project, good file and directory level readability ensures the robustness of the code.
An example **directory hierarchy** for a common Flutter project is as follows:

```
--flutter_project           # Project root directory
----/android, ios           # Mobile native files
----/web, macos, windows    # Web and desktop platform layer files
----/assets                 # resource file
------/fonts                # Fonts
------/images               # Images
------/others               # Other types of resources
----/lib                    # The main code of the project
------/apis                 # Interface definition, encapsulation of various requests
------/constants            # Constants, uniform import and export diameters
--------/constants.dart     # Various packages, models, apis, widgets are finally unified here for export
--------/resources.dart     # (optional) Convert resource references to constants (flutter_asset_generator)
------/extension            # Extension methods defined in the project
------/models               # Beans, Models, class definition 
--------/models.dart        # All content is divided into partitions
------/pages                # Pages
------/providers            # (optional) state preservation
------/utils                # Tool
------/widgets              # Custom widgets
----/analysis_options.yaml  # IDE code analysis configuration
----/main.dart              # Main entry
----/pubspec.lock           # Current dependent packages version
----/pubspec.yaml           # Pub packages declaration file
```
### 1.1.1 File Naming

- **REQUIRED** All files (including resources) are named in **lowercase+underscore**.
   - `home_page.dart`
- **MUST** When naming a functional group, if it represents a series of similar collections, use plural words.
    - `enums`, `models`
- **TRY TO** Do not use **abbreviations** for file names that are **unrelated** to the project and have **no consensus**.
    - `wlck_page.dart`
- **TRY MY BEST** When participle **Xin, Ya, Da. **
    - `user_edit_profile_page.dart`

### 1.1.2 Class Naming

A class should bluntly state its **content+identity+purpose**.

- **MUST** be named in camelCase. Same goes for **enums** and **extension functions**.
    - `class Foo` , `extension Bar<T>`
- **MUST** Capitalized acronyms longer than two letters are treated as regular words. Two-letter words are excluded.
    - `HttpConnectionInfo` , `IOStream` , `Id` ✅
    - `HTTPConnection` , `IoStream` , `ID` 🚫
- **MUST** use **lowercase+underscore** aliases when importing libraries.
    - `import 'dart:math' as math`
- **TRY TO** Do not exceed 5 words. If it is exceeded, it should be considered whether it is improperly named or split


### 1.1.3 Variable naming

- **MUST** use **little camelCase** for naming.
    - `int imagesLength`
    - `List<AssetEntity> selectedAssets`
- **MUST** be no more than 5 words.
- **REQUIRED** Boolean variables are prefixed with `is` or `should`.
    - `bool isLoading`
- **REQUIRED** If a variable is not private, do not prefix it with `_`, since there is no concept of private in Dart.
- **Whenever possible** In addition to the boolean value that represents the state, other variables are named using nouns.
    - `double width`
    - `Widget separator`
- **TRY TO** Do not use prefix letters. (`k` is a default prefix in Flutter.)
    - `kDefaultTimeout`
- **TRY TO** Do not name multiple variables that are close and confusing at the same time.
    - `tabIndex` , `tabCurrentIndex`
    
# 2 Usage specification

## 2.1 import/export

- **MUST** import/export in alphabetical order.
- **MUST** put `dart:` first.
- **MUST** put `package:` before relative references.
- **MUST** place Flutter packages before other packages.
- **MUST** place exports after all imports.
- **TRY TO** avoid importing the **src** of the package when building general pages. Unless you are customizing an official component. [implementation_imports](https://dart-lang.github.io/linter/lints/implementation_imports.html)

## 2.2 Construction method

Constructors are one of the most commonly used methods in OOP languages. In Dart + Flutter, according to the restrictions of various rules, there are generally the following requirements for constructors:

- **MUST** All **constructors** and **factory methods** must be written at the **beginning position** of the class, and then the variable declaration.
- **REQUIRED** `StatefulWidget` The construction field must be `final`. (@immutable)
- **REQUIRED** `Widget` constructors must take the named optional parameter `Key key`.
- **REQUIRED** If the constructor requires 3 or more parameters, or as a `Widget` constructor, turn them into named optional parameters.
- **MUST** use private constructors for singletons. (Example: `const API._()` )
- **MUST** implement `toString()` for all custom classes.
- **TRY TO** If the fields of `Widget` are all final values and there is no construction callback, add `const` to the construction method.
- **TRY TO** Classes that may be used for `Iterable*`, please override `operator ==` and `hashCode`.
- **RECOMMEND** As `ChangeNotifier` of Provider Model, field uses getter & setter.
- **RECOMMEND** An entity class converted from json data, please implement `Map<String, dynamic> toJson()`.

Here are two examples based on the above requirements:

```dart
class Person {
  const Person({
    this.name = 'Alex',
    this.age = 23,
    this.skills,
  });
  factory Person.fromJson(Map<String, dynamic?> json) {
    return Person(
      name: json['name'] as String,
      age: json['age'] as int,
      skills: json['skills'] as List<String>?,
    );
  }
  final String name;
  final int age;
  final List<String>? skills;
  @override
  bool operator ==(Object other) {
    if (identical(other, this)) {
      return true;
    }
    if (other.runtimeType != runtimeType) {
      return false;
    }
    return name == other.name
        && age == other.age
        && skills == other.skills;
  }
  @override
  int get hashCode => hashValues(name, age, skills);
}
```

```dart
class PersonalPage extends StatefulWidget {
  const PersonalPage({
    Key key,
    @required this.person,
  }) : super(key: key);
  final Person person;
  ///...///
}
```
## 2.3 Variable declaration, use, call

- **PROHIBIT** Do not use `var`, `Object`, **you must determine its type when determining a variable**.
- **PROHIBIT** Do not use **Magic Numbers**. If it's a type representation, directly define them **as enums**.
- **MUST** avoid common use of abbreviations, except for something like `x` for offsets.
    - `i` -> `index`
- **MUST** Write down the type when declaring, **including generics**. [always_specify_types](https://dart-lang.github.io/linter/lints/always_specify_types.html)
    - `Map<String, dynamic> json`
    - `Column(children: <Widget>[])`
- **MUST** use `final` to declare variables of **basic types** that **do not need to be changed**, **non-basic types** that **do not need to re-create instances**.
    - `final int userId = 0`
    - `final ScrollController controller = ScrollController()`
    - `final List<AssetEntity> selectedAssets = <AssetEntity>[]`
- **MUST** Null Safety Except for Model, don't abuse `?`, determine whether it is nullable when declaring it.

### 2.3.1 Boolean values

- **PROHIBITED** Do not write Boolean-to-Boolean statements.
    - `return list.isEmpty? true : false`
- **MUST** NOT NULL SAFE Use `??` to convert a possibly null boolean to a non-null value.
    - `list?.isEmpty?? true`
- **TRY TO** name the value in **positive form**. If you always negate it when you use it, consider inverting the meaning of the name.

### 2.3.2 Enumeration

- **Disable** Listing all enum values in `switch`, do not use `default`.
- **MUST** Use `switch` instead of `if` to determine the enum content.

### 2.3.3 Strings

- **MUST** Use adjacent strings instead of `+` when concatenating literal strings. [prefer_adjacent_string_concatenation](https://dart-lang.github.io/linter/lints/prefer_adjacent_string_concatenation.html)
- **MUST** Use interpolation to combine strings. [prefer_interpolation_to_compose_strings](https://dart-lang.github.io/linter/lints/prefer_interpolation_to_compose_strings.html)
- **MUST** Avoid unnecessary curly braces in string interpolation. [unnecessary_brace_in_string_interps](https://dart-lang.github.io/linter/lints/unnecessary_brace_in_string_interps.html)

### 2.3.4 Collections

- **REQUIRED** `List` , `Set` , `Map` use literals to construct instances. [prefer_collection_literals](https://dart-lang.github.io/linter/lints/prefer_collection_literals.html).
    - `<T>[]` , `<T>{}` , `<T, S>{}`
- **TRY TO** `Iterable*` is initialized to an empty collection.
    - `List<int> list = <int>[]`
- **TRY TO** Avoid `toList()` and `List.cast`, use `List.from`.
    - Only used when mutating types and generating `List` from iterable objects, do not use it at other times.
- **TRY TO** Construct new `Iterable` using the **spread operator**.
    - `final List<int> anotherList = <int>[...list]`
- **RECOMMENDED** to use the **cascade operator** to make consecutive calls to the same object.
    - `list..add(x)..add(y)`

### 2.3.5 Local variables
