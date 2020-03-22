---
layout: post
title:  "Converting A Redux App From Thunks To Sagas: Getting Started"
date:   2020-3-22 1:03:00 -0700
categories: tutorials
---

## Motivation

Handling asynchronous effects within Redux apps has generally traditionally been handled with thunks, which are action creators that return functions to be executed later in a delayed way for async side effects such as API calls.

Thunks can involve a lot of nesting and what looks like "the pyramid of doom" and "callback hell." 

Thunks are also very hard to test.

Enter sagas, a long running process in Redux that can listen to actions being fired, dispatch ("put") a new action, and do multiple things to make our apps more testable, snappy and responsive.

Sagas are generators, so if you don't know what that means, check out the [async await / generators post](http://blog.amandafalke.com/tutorials/2018/02/24/async-await-infinite-regression.html).

##  To upgrade a repo from using only thunks to using sagas:

1. Create a dummy saga
2. Add in Saga middleware to store configuration, and start/run sagas from store config
3. Test sagas (generators with generator.next() { value: someValue, done: false } until { ... done: true }
4. Since thunks and sagas can live together in the same repo, you can now move forward with handling async side effects
with new code by using sagas (and being able to test them), and slowly migrate your legacy thunks over to sagas over time. 

### Step 1. Create the root saga.

> rootSaga.js

```
import { all, call } from 'redux-saga/effects';
import 'regenerator-runtime/runtime'; // Justification: https://github.com/redux-saga/redux-saga/issues/280

function sagaStub() {
  return console.log('Dummy saga here ...');
}

function* dummySaga() {
  const dummyReturn = yield call(sagaStub);
  return dummyReturn;
}

export default function* rootSaga() {
  try {
    yield all([
      dummySaga(),
    ]);
  } catch (e) {
    console.error(e);
  }
}

export { dummySaga, sagaStub };
```

### Step 2. Store configuration
```
// thunk stuff here ...

// 2a. then add the Saga middleware:
import createSagaMiddleware from 'redux-saga';
import rootSaga from '<path>/rootSaga';
const sagaMiddleware = createSagaMiddleware();

// 2b. there will be some thunk middleware; add in sagaMiddleware to the end of that array. e.g.
const middleware = [ thunk, routerMiddleware(history), createLogger(), otherStuffForThunks, sagaMiddleware ]
       
// 2c. Make sure that middleware, with your sagaMiddleware, is in the createStore() function ...

// 2d. Start your sagas.
sagaMiddleware.run(rootSaga);

// RESULT: This will just log a 'Dummy saga here ...' to Chrome devtools/Console when you npm start/run your app.
// It's just a dummy saga; it's not hooked into any actions, watching or listening for actions etc.
```

### Step 3. Test.

> sagas-test.js (Enzyme, Jest)

```
import { call } from 'redux-saga/effects';
import { dummySaga, sagaStub } from './rootSaga';

describe('<path>/rootSaga.js', () => {
  describe('dummySaga', () => {
    it('should use call saga effect to ret console log', () => {
      const generator = dummySaga();
      const received = generator.next().value;
      const expected = call(sagaStub);
      expect(received).toEqual(expected);
    });

    it('should return done after a single yield is completed', () => {
      const generator = dummySaga();
      generator.next();
      const next = generator.next();
      expect(next.done).toEqual(true);
    });

    it('should return false for generator not done, after a single yield', () => {
      const generator = dummySaga();
      generator.next();
      const next = generator.next();
      expect(next.done).not.toEqual(false);
    });
  });
});
```

### Step 4. Have your sagas listen to actions!

### Step 5. Over time, replace your old thunks with sagas.


That's it for a brief high level overview.



[My Github gist](https://gist.github.com/abstractmachines/180c062794f70e75603e477e53006c02) on converting thunks to sagas

<script src="https://gist.github.com/abstractmachines/180c062794f70e75603e477e53006c02.js"></script>

Please also see [the gist itself]([link](https://gist.github.com/abstractmachines/180c062794f70e75603e477e53006c02)) if you like.



