# @solid-primitives/jsx-tokenizer

## 1.0.0

### Major Changes

- 60d7ac20: Package got renamed from `jsx-parser` to `jsx-tokenizer`.

  Improved jsdoc comments with better descriptions.

  `createJSXParser` renamed to `createTokenizer`.

  `isToken` and `resolveTokens` can now accept an array of tokenizers to match.

  `createToken` can be used without passing a tokenizer. This will create a token with the component function as the tokenizer.

## 0.2.0

### Minor Changes

- b1bf1d74: Merge `resolveData` and `resolveTokens` together. Add option to resolve JSX Elements as well.

## 0.1.3

### Patch Changes

- Updated dependencies [d6559a32]
  - @solid-primitives/utils@5.4.0

## 0.1.3-beta.0

### Patch Changes

- Updated dependencies [d6559a32]
  - @solid-primitives/utils@5.4.0-beta.0

## 0.1.2

### Patch Changes

- 865d5ee9: Fix build. (remove keepNames option)
- Updated dependencies [865d5ee9]
  - @solid-primitives/utils@5.2.1

## 0.1.1

### Patch Changes

- 3f2cc1fb: Warn about invalid elements only if thy are truthy

## 0.1.0

### Minor Changes

- fd0d137a: Add the token object to the `data` property of the token element, instead of spreading it with `Object.assign`.

  Separates available functions into own exports, `parser` is now required to be passed to the functions.

### Patch Changes

- Updated dependencies [c1538561]
  - @solid-primitives/utils@5.1.0

## 0.0.2

### Patch Changes

- e2533800: set returnType createToken from JSX.Element to TokenComponent<Token>