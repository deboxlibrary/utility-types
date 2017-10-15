# React / Redux / TypeScript Utils
> Redux helpers for Type-safety (action types, action creators, reducers)
- Semantic Versioning
- No external dependencies
- 100% test coverage
- output es5 and es6 bundles

### Table of Contents
- [Helpers v2.2](#helpers-v22)
- [Helpers v2.0](#helpers-v20)
- [returntypeof polyfill](#returntypeof-polyfill)

---

### Helpers v3.0
```ts
WIP
...
```

---

### Helpers v2.2
```ts

export class ActionCreator<T, P> {
  readonly type: T;
  readonly payload: P;

  constructor(type: T) { this.type = type; }
  create = (payload: P) => ({ type: this.type, payload });
}

// Example:
import { ActionCreator } from 'react-redux-typescript';

export const ActionCreators = {
  IncreaseCounter: new ActionCreator<'IncreaseCounter', number>('IncreaseCounter'),
  ChangeBaseCurrency: new ActionCreator<'ChangeBaseCurrency', string>('ChangeBaseCurrency'),
};

store.dispatch(ActionCreators.IncreaseCounter.create(4)); // { type: "IncreaseCounter", payload: 4 }
store.dispatch(ActionCreators.ChangeBaseCurrency.create('USD')); // { type: "ChangeBaseCurrency", payload: 'USD' }

// Action Types
type Action = typeof ActionCreators[keyof typeof ActionCreators];
// { type: "IncreaseCounter", payload: number } | { type: "ChangeBaseCurrency", payload: string }

// Reducer                                                           vvvvvv
export default function reducer(state: State = initialState, action: Action): State {
  if (action.type === ActionCreators.IncreaseCounter.type) {
    state.counter = action.payload; // number
  }
  else if (action.type === ActionCreators.ChangeBaseCurrency.type) {
    state.baseCurrency = action.payload; // string
  }
...
```

---

### Helpers v2.0
```ts
/**
 * @type EmptyAction - Empty Action Type
 * @template T - Generic Type
 */
export type EmptyAction<T> = {
  readonly type: T;
}

/**
 * @type PayloadAction - Flux Standard Action Type
 * @template T - Generic Type
 * @template P - Generic Type
 */
export type PayloadAction<T, P> = {
  readonly type: T;
  readonly payload: P;
  readonly error?: boolean;
}

export function createEmptyAction<T>(type: T): () => EmptyAction<T> {
  return () => ({ type });
};

export function createPayloadAction<T, P>(type: T): (payload: P) => PayloadAction<T, P> {
  return (payload) => ({ type, payload });
}
```

---

### getReturnOfExpression
> Get return value of an "expression" with inferred return type  
> alias: returntypeof  
https://github.com/Microsoft/TypeScript/issues/6606

```ts
// this polyfill exist because TypeScript does not support getting type of expression 
// (tracking issue: https://github.com/Microsoft/TypeScript/issues/6606)
export function returntypeof<RT>(expression: (...params: any[]) => RT): RT {
  return null as any as RT;
}

// Example:
import { getReturnOfExpression } from 'react-redux-typescript';

const increment = () => { type: 'INCREMENT' };

const returnOfIncrement = getReturnOfExpression(increment);
type INCREMENT = typeof returnOfIncrement;
```
---
MIT License

Copyright (c) 2016 Piotr Witek <piotrek.witek@gmail.com> (http://piotrwitek.github.io)
