# Block Formatting Implementation Plan

## Goal
Add a new formatting style to clang-format that formats expressions with "block style":

```cpp
bool condition = (
    firstConditionWithLongName
&& secondConditionWithLongName
&& thirdConditionWithLongName
);
```

Key characteristics:
- Opening parenthesis on the assignment line
- Each operand on its own line
- Binary operators at column 0 or minimally indented (user's examples show no indentation)
- Closing parenthesis on its own line

## Required Changes

### 1. Add New Configuration Option in Format.h

Add a new enum value or boolean option to control this behavior. Options:
- Add a new value to `BinaryOperatorStyle` enum (e.g., `BOS_BlockStyle`)
- Add a separate boolean option (e.g., `BlockStyleBinaryOperators`)
- Add indentation control options for operators vs operands

### 2. Modify ContinuationIndenter.cpp

The main formatting logic for continuation lines is in `ContinuationIndenter.cpp`. Need to:
- Detect when we're in a binary expression
- Force line breaks after opening parenthesis if needed
- Set operator indentation to 0 or minimal value
- Set operand indentation to desired value (appears to be 4 spaces from examples)

Key functions to modify:
- `addTokenOnCurrentLine()` - where indentation is calculated
- `moveStateToNextToken()` - where state transitions happen
- Functions that check `BreakBeforeBinaryOperators`

### 3. Modify TokenAnnotator.cpp

May need to adjust how tokens are annotated to recognize block-style patterns.

### 4. Update Format.cpp

- Add the new option to the YAML parsing in `parseBool()` or `mapOptional()`
- Set default values for different base styles (LLVM, Google, etc.)

### 5. Add Tests

Add comprehensive tests in `clang/unittests/Format/FormatTest.cpp`:
- Test basic binary operators (`+`, `-`, `*`, `/`)
- Test logical operators (`&&`, `||`)
- Test comparison operators
- Test mixed operator expressions
- Test with different parenthesis nesting levels
- Test that assignment operators are not affected

### 6. Update Documentation

Update `clang/docs/ClangFormatStyleOptions.rst` (auto-generated from Format.h)

## Implementation Challenges

1. **Operator Indentation**: Current clang-format always adds some indentation. Setting it to 0 may require special handling.

2. **Break After Opening Paren**: Current logic tries to keep something on the same line as `(`. Need to force break.

3. **First Operand Position**: Need to ensure the first operand starts on a new line after `(`.

4. **Closing Paren Alignment**: Need to ensure `)` aligns correctly (typically at column 0 or matching opening `(`).

5. **Interaction with Other Options**: Need to ensure this works well with other formatting options like `ColumnLimit`, `ContinuationIndentWidth`, etc.

## Alternative Approach

If modifying clang-format is too complex, could consider:
- Creating a post-processing tool that adjusts formatted code
- Using clang-format with close-enough settings and documenting the limitations
- Proposing this as an upstream LLVM feature request

## Next Steps

1. Confirm with user this is the correct approach
2. Set up build environment for clang-format
3. Start with minimal implementation (add configuration option)
4. Incrementally add formatting logic
5. Add tests
6. Iterate and refine
