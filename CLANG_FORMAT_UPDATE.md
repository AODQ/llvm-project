# Clang-Format Configuration Update

## Overview
This document describes the updates made to the `.clang-format` configuration file to improve formatting of multi-line expressions.

## Changes Made
The following options were added to `.clang-format`:

### BreakBeforeBinaryOperators: NonAssignment
- Places binary operators at the **beginning** of continuation lines (not at the end)
- Does not apply to assignment operators (`=`), keeping them on the same line as the variable
- Improves readability by making operators more visible

### AlignOperands: AlignAfterOperator
- Aligns operands consistently after operators on continuation lines
- Creates visually aligned columns for better code readability

### AlignAfterOpenBracket: BlockIndent
- Uses block indentation style for opening brackets/parentheses
- Provides consistent indentation for wrapped expressions

## Examples

### Before (Default LLVM Style)
```cpp
bool condition = (firstConditionWithLongName && secondConditionWithLongName &&
                  thirdConditionWithLongName);
```

### After (New Configuration)
```cpp
bool condition =
    (firstConditionWithLongName && secondConditionWithLongName
     && thirdConditionWithLongName);
```

### More Examples
See `formatting_examples.cpp` for comprehensive examples of how different operators and expressions are formatted with the new configuration.

## Benefits
1. **Improved Readability**: Operators at the beginning of lines are easier to spot
2. **Better Alignment**: Operands are consistently aligned
3. **Consistent Style**: All binary operators follow the same breaking rules
4. **No Assignment Breaking**: Assignment operators stay on the variable line

## Testing
To test the formatting on your code:
```bash
clang-format -i your_file.cpp
```

To preview formatting without modifying the file:
```bash
clang-format your_file.cpp
```
