# godot-build-tool

Example repo for my tiny build tool for Godot projects using Rake. See [my article](https://chikorito.land/articles/2024-06-03-build-tool-godot) for more info.

# Usage

## Installing

```sh
bundle install
rake install
```

## Starting the project

```sh
# Running your project
rake test

# Opening the editor
rake dev
```

## Exporting

Exporting requires some additional setup and possibly tweaking. See [my article](https://chikorito.land/articles/2024-06-03-build-tool-godot) for more info.

```sh
rake build
cd .build
ls  # Build artifacts should be here
```
