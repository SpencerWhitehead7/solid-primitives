<p>
  <img width="100%" src="https://assets.solidjs.com/banner?type=Primitives&background=tiles&project=history" alt="Solid Primitives history">
</p>

# @solid-primitives/history

[![turborepo](https://img.shields.io/badge/built%20with-turborepo-cc00ff.svg?style=for-the-badge&logo=turborepo)](https://turborepo.org/)
[![size](https://img.shields.io/bundlephobia/minzip/@solid-primitives/history?style=for-the-badge&label=size)](https://bundlephobia.com/package/@solid-primitives/history)
[![version](https://img.shields.io/npm/v/@solid-primitives/history?style=for-the-badge)](https://www.npmjs.com/package/@solid-primitives/history)
[![stage](https://img.shields.io/endpoint?style=for-the-badge&url=https%3A%2F%2Fraw.githubusercontent.com%2Fsolidjs-community%2Fsolid-primitives%2Fmain%2Fassets%2Fbadges%2Fstage-0.json)](https://github.com/solidjs-community/solid-primitives#contribution-process)

Primitives for managing undo/redo history in Solid.

## Installation

```bash
npm install @solid-primitives/history
# or
yarn add @solid-primitives/history
# or
pnpm add @solid-primitives/history
```

## `createUndoHistory`

Creates an undo history from a reactive source for going back and forth between state snapshots.

### How to use it

`createUndoHistory` takes two arguments:

- `source` - A function or an array thereof that tracks the state to be restored, and returns a callback to restore it.
- `options` - Configuration object. Optional.
  - `limit` - The maximum number of history states. Defaults to `100`.

```tsx
import { createUndoHistory } from "@solid-primitives/history";

const [count, setCount] = createSignal(0);

const history = createUndoHistory(() => {
  // track the changes to the state (and clone if you need to)
  const v = count();
  // return a callback to set the state back to the tracked value
  return () => setCount(v);
});

// undo the last change
history.undo();

// redo the last change
history.redo();

// check if there are any changes to undo/redo with .canUndo() and .canRedo()
return (
  <>
    <button disabled={!history.canUndo()} onClick={history.undo}>
      Undo
    </button>
    <button disabled={!history.canRedo()} onClick={history.redo}>
      Redo
    </button>
  </>
);
```

### Observing stores

Stores have many independent points of updates, so you can choose how and what to track.

But for the most part, you may want to track and copy the whole store value.

Copying is important so that the history points are not affected by future mutations to the store.

```ts
const [state, setState] = createStore({ a: 0, b: 0 });

const history = createUndoHistory(() => {
  // track and clone the whole state
  const stateVal = structuredClone(state);
  // reconcile the state back to the tracked value
  return () => setState(reconcile(stateVal));
});
```

### Observing multiple sources

You can track as many signals in the `source` callback as you want. Then any updates will create a point in history for all of them.

```ts
const [a, setA] = createSignal(0);
const [b, setB] = createSignal(0);

const history = createUndoHistory(() => {
  const aVal = a();
  const bVal = b();
  return () => {
    setA(aVal);
    setB(bVal);
  };
});

// set them both at the same time, to only create one point in history
batch(() => {
  setA(1);
  setB(1);
});
```

### Observing multiple independent sources

If you want to track multiple independent sources, you can pass an array of source functions to `createUndoHistory`.

This way the undo history will still be shared, but the individual source and setter functions will be called when needed, instead of all at once. This is useful for tracking multiple stores where you want to avoid unnecessary cloning and reconciliation.

```ts
const [a, setA] = createSignal(0);
const [b, setB] = createSignal(0);

const history = createUndoHistory([
  () => {
    const aVal = a();
    return () => setA(aVal);
  },
  () => {
    const bVal = b();
    return () => setB(bVal);
  },
]);

// e.g.
setA(1);
history.undo(); // will only call setA(0)
```

### Changing souces or clearing history

If you want to change what sources get tracked, or clear the history, you can wrap the `createUndoHistory` call in a `createMemo`:

Example of clearing the history:

```ts
const [trackClear, clear] = createSignal(undefined, { equals: false });

const history = createMemo(() => {
  // track what should rerun the memo
  trackClear();
  return createUndoHistory(/* ... */);
});

// history is now a signal
history().undo();

// clear the history
clear();
```

Example of changing the source:

```ts
const [a, setA] = createSignal(0);
const [b, setB] = createSignal(0);
const [useA, setUseA] = createSignal(true);

const history = createMemo(() =>
  createUndoHistory(
    useA()
      ? () => {
          const aVal = a();
          return () => setA(aVal);
        }
      : () => {
          const bVal = b();
          return () => setB(bVal);
        },
  ),
);
```

## Changelog

See [CHANGELOG.md](./CHANGELOG.md)