- [ ] Tests
- [ ] Docs

# Expect Gen
As in "expect generator to...". An assertion / snapshot library for testing [iterators and generators](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Iterators_and_Generators).  It was designed for, and works particularly well with, [redux-saga](https://github.com/redux-saga/redux-saga).

## Example
```es6
function* myEffect(fakeAction) {
  const threadIds = yield select(threadIds);
  yield put(threadsLoading());
  const results = yield call(apiFetch, options);
}

import { expectGen } from 'expect-gen';

it('runs effect with fakeThreadIds and fakeResults', () => {
  expectGen(myEffect, fakeAction)
    // asserts step yields first `select(threadIds)`
    .yields(
      select(threadIds),
      fakeThreadIds
    )
    .yields(
      put(threadsLoading())
    )
    .yields(
      call(apiFetch, options),
      fakeResults
    )
    .finishes()
    .run();
});

it('runs effect with fakeThreadIds and fakeResults', () => {
  const snapshot = expectGen(myEffect, fakeAction)
    // Doesn't run assertion for `next`
    .next(fakeThreadIds)
    .next()
    .yields(
      call(apiFetch, options),
      fakeResults
    )
    .finishes()
    .run();

  expect(snapshot).toMatchSnapshot();
});
```


# API
## expectGen
`expectGen(generator, [...args])`
- Calls the first argument with the rest of the arguments
- Returns instance of `StepManager`

## StepManager
`expectGen` returns an instance `StepManager`.  It gives us a set of chainable methods:

### yields
```es6
expectGen(generator, [...args])
  .yields(expectedValue, [result])
```
- Runs an assertion that iterator will yield the `expectedValue`
- Passes `result` into generator's `next` method
- Returns same instance `StepManager`

### next
```es6
expectGen(generator, [...args])
  .next([result])
```
- Passes `result` into generator's `next` method
- Makes no assertion
- Returns same instance `StepManager`

### finishes
```es6
expectGen(generator, [...args])
  ...
  .finishes([result])
```
- Asserts that generator finishes with `result`
- Returns same object with `run` method

### run
```es6
expectGen(generator, [...args])
  ...
  .run()
```
- Executes all assertions
- Returns snapshot of all generator steps