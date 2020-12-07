---
title: Choosing redux-observable or redux-sagas
date: 2018-01-31 09:00:00
tags:
    - react
    - redux
    - redux-saga
category: tech
keywords:
    - react
    - redux
    - redux-saga
---


My task is to compare two libraries which help you resolve same problem, in different manner - Handling async actions in redux applications.
I don't want to state which one is better than another but I do want to list pros and cons of each and provide clear feedback which one I have picked and my reasons for it.

There is a nice topic on stack-overflow on the question ["Why use Redux-Observable over Redux-Saga?"](http://stackoverflow.com/questions/40021344/why-use-redux-observable-over-redux-saga). In this topic there is feedback from creator of react-observable, stating "We don't currently provide any reason redux-observable is better than redux-saga because...it's not. They solve the same problem in extremely similar ways, but have some fundamental differences that only become truly apparent once you use them enough"

In the same response he also points out that deciding for one or another prematurely is unnecessary and even after you can/should still use redux in cases where power of those libs is not needed.

This is very pragmatic approach which resonates with me, but I would like to test it, how easy/clean it is to maintain both solutions and if that doesn't introduce unnecessary complications.

Maybe redux-thunk is good for development process and setting up flow, and then moving it to sagas/rxJS could be easier than doing it upfront - no way of knowing without testing.

In the topic i mentioned people write their pros and cons and their own thoughts on the matter so I recommend reading it, it is always nice to get to know at least couple of different opinions and reasons.

I will try to summarize both libs in the context of the project I am working on, so my comparison is not 'objective' comparison, but just selecting the one that I believe is appropriate for me and deciding that in most objective way.

## Defining the attributes

So first I would like to introduce some aspects/attributes that I will try to base upon, those will have most impact on my decision, but I don't know yet if there won't be something that will be a game changer during my analysis, so this list is not something set in stone.

Project I will be working on will be used by many different developers, so **Learning Curve** of lib will have big impact, same as **community support**. In team there will be likely no one who had any prior experience with neither of those. 
It is important that there are some **examples** of best practices available. Project lifetime will be quite long so **long-term support** would be appreciated.

Those aspects are aspects that I am specifically interested in. Nevertheless there are some things I would like also to analyse: Like **testability** of the solutions and **expressiveness** of resulting code (very opinionated), 

So with such description we've got couple of different aspects that I will try to consider.
- Learning Curve
- Community support
- Examples - best practices
- Longevity
- Testability
- Expressiveness

For each of those "Attributes" I would like to have a well-defined metric. I want to have decision based on the needs of project, not on personal preference and be confident with it.

## Defining metrics

- **Learning curve** I will base on opinions of others, with little support of my own experience.
I would like to be able to support the claim with a couple of examples in both libraries, but I know that this can be very misleading, as sometimes libraries allows you to write very complicated stuff much easier in the expense that simple stuff becomes a little harder (but manageable), so it is hard to measure on that. But a side by side comparison is always nice.

    Nevertheless the 'Learning Curve' can be also measured by the ease of transforming my existing thunk solutions to their libraries counterparts. I will probably run a timer and see how long it will take me for each solution. It is most objective metric I can come up with right know. I can also try to come up with couple of 'change requests' that could happen, I will also ask my colleagues for couple of such requests just to be less opinionated.
    
    I will also try to move couple of examples that are done in one library to another just to see how/if same things can be easily achieved in both.

- **Expressiveness** I have no prior experience with neither sagas and rxJS my assumption is that:
If I am able to understand the code someone has written and would be able to reproduce it with 'minor' tweaks, lib is expressive enough :). It is very opinion/personal metric. But it will work for me.

- **Community support** will be measured by couple of numbers that may not be very representative but provide easy quantifier. It will be stars on github, questions on stack-overflow, job offers. It would be also nice to see how many developers are familiar with given technology, but I don't know if I will find a simple channel for that.

- **Testability** will be measured by the amount necessities in order to tests flows. I would expect that mocks doesn't require too much knowledge of the lib and you have to mock only the very necessities. The library should also provide some guidelines for testing. 

<!-- TODO WJ NOT TRUE-->
- **Longevity** it is very hard to estimate life expectancy of the framework, but it can be done by seeing the roadmap of project, the amount of contributors and traction it gets. I will try to derive some statistics and visuals from that information. (Cause graphs are pretty).

## Comparison

There is nice comparison of libs done on [https://hackmd.io/s/H1xLHUQ8e#side-by-side-comparison](https://hackmd.io/s/H1xLHUQ8e#side-by-side-comparison), I like the fact that there are same things achieved in both libraries, so probably I won't duplicate the same code I may add my own examples on top of that but in order to see basic ones, please refer to the mentioned post. Also this is probably nice read as complement to what you will learn here.

### Learning Curve and Expressiveness

 I have had no prior working experience with rxJS, neither with generator functions. 
 I do know how generators work same with rxJS, I have 'understanding' of principles, but I haven't used them any real application with either. 
 
 When I was starting this comparison I was not able to write any Epic/Saga without constantly looking to examples/documentation, so I suppose, if I will be able
  to say at the end of comparison that I can do it I will be able to at least give a little feedback how much difference there is in necessary knowledge/understanding.
  
### React-observable (rxJS)
 
Library strongly bases on a library called rxJS. It is supporting declarative way of writing code. For someone who is familiar with functional programming the concepts may seam easier to understand but there is a lot of idiomatic functions that you will have to learn in order to use it. 
 
There is a lot of rxJS method available, that give you big flexibility which is ok, but it expands learning curve, making it quite steep.
  
Couple of code examples from library docs:

```javascript
const pingEpic = action$ =>
  action$.ofType(PING)
    .delay(1000) // Asynchronously wait 1000ms then continue
    .mapTo({ type: PONG });
```

```javascript
const fetchUserEpic = action$ =>
  action$.ofType(FETCH_USER)
    .mergeMap(action =>
      ajax.getJSON(`https://api.github.com/users/${action.payload}`)
        .map(response => fetchUserFulfilled(response))
    );
```

```javascript
const incrementIfOddEpic = (action$, store) =>
  action$.ofType(INCREMENT_IF_ODD)
    .filter(() => store.getState().counter % 2 === 1)
    .map(() => increment());
```

Up front in these examples you can see:
  ```javascript
  .map
  .mergeMap
  .mapTo
  ```
But there are also:
  ```javascript
  .switchMap
  .flatMap
```

Those functions names mean little without explaining. Prior to looking through documentation, only seeing examples - even though understandable - I still could not easily 'define' what given method does. 
I would probably by able to define them through analysis of code and creating a mental model, but it is not *obvious*.

After reading some community opinion on matter it is also most commonly thought about as 'not easy' to learn library and approach. My first impressions are confirming those opinions.

### React-sagas (generators)
  
Sagas code use and base upon *ES6* feature: generators functions. It also adds couple of lib specific functions like `call`, `put`, `take`.

Couple of code examples from library docs:

```javascript
function* watcher() {
    const action = yield* takeEvery(PING);
    const result = yield fork(sendDelayedPong)
}

sendDelayedPong (){
    yield put(PONG)
}
```

```javascript
function* fetchUser(action) {
   try {
      const user = yield call(Api.fetchUser, action.payload.userId);
      yield put({type: "USER_FETCH_SUCCEEDED", user: user});
   } catch (e) {
      yield put({type: "USER_FETCH_FAILED", message: e.message});
   }
}

/*
  Starts fetchUser on each dispatched `USER_FETCH_REQUESTED` action.
  Allows concurrent fetches of user.
*/
function* mySaga() {
  yield takeEvery("USER_FETCH_REQUESTED", fetchUser);
}
```

```javascript
export function* getAllProducts() {
  const products = yield call(api.getProducts)
  yield put(actions.receiveProducts(products))
}
```

First benefit I can see from such approach is that async code looks quite synchronous. So it is rather stragthforward to write application in such way.

Looking at examples I can clearly state what additional methods do (`put, take, call`) and I can do it with pretty strong confidence that I am right.
 
I believe for most people it will be much easier to understand with it imperative style of writing and it being mostly pure JavaScript with simple and well named methods.
 
The thing that seems that one has to learn are: sagas specific methods and how generators in javascript work. That seems like reasonable amount.


### Moving from redux-saga to redux-observable
<!-- comparison-section -->

<!-- redux-saga -->
redux-saga
```javascript
export function* getAllProducts() {
  const products = yield call(api.getProducts)
  yield put(actions.receiveProducts(products))
}
```

<!-- redux-observable -->
redux-observable (4minutes)
```javascript
const getAllProducts = action$ => 
  $action.ofType(GET_ALL_PRODUCTS)
    .mergeMap(() => 
      Observable(api.getProducts())
        .map(actions.receiveProducts)
    );
```


<!-- redux-saga -->
redux-saga
```javascript
function* fetchUser(action) {
   try {
      const user = yield call(Api.fetchUser, action.payload.userId);
      yield put({type: "USER_FETCH_SUCCEEDED", user: user});
   } catch (e) {
      yield put({type: "USER_FETCH_FAILED", message: e.message});
   }
}
```

<!-- redux-observable -->
redux-observable (3minutes)
```javascript
const fetchUserEpic = action$ =>
  action$.ofType(FETCH_USER)
    .mergeMap((action) => 
      Observable(api.fetchUser(action.payload.userId))
        .map((user) => {type: "USER_FETCH_SUCCEEDED", user: user})
        .catch(e => {type: "USER_FETCH_FAILED", message: e.message})
    );
```


<!-- redux-saga-->
redux-saga (from thunk implementation - 30minutes (learned how to cancel, takeLatest event, tested on working application)

```javascript
function* commandSwitchIssues(action) {
  yield call(processesService.switchIssuesDoingWell);
  yield put(switchIssuesDoingWellFinished(action.payload.timestamp));
  yield put(fetchProcesses());
}
  
function* Watcher() {
  let timestamp = null, commandInProgress = null;

  while (commandInProgress = yield takeLatest(actionsIDs.SWITCH_ISSUES_DOING_WELL_REQUEST, commandSwitchIssues)) {
    yield take(SWITCH_ISSUES_CANCELLED);
    yield cancel(commandInProgress);
  }
}
```

redux-observable (from saga 10-15 minutes)
<!-- redux-observable-->
```javascript
const switchIssuesEpic = action$ =>
    action$.ofType(actionsIDs.SWITCH_ISSUES_DOING_WELL_REQUEST)
    .switchMap((action) =>
        Observable(processesService.switchIssuesDoingWell)
            .map(() => Observable.merge(
                fetchProcesses(),
                switchIssuesDoingWellFinished(action.payload.timestamp)
            ))
            .takeUntil(action$.ofType(SWITCH_ISSUES_CANCELLED))
    )
```

### Summary of learning curve and expressivness

I believe redux-saga is more straightforward in comparison to redux-observable, but for people already familiar with rxJS, that may not be the case.
I was quite concerned with complexity of rxJS, but when I move examples from sagas to rxJS it is quite straightforward, yet the effect is not amusing.

I have 'tested' expressivnes with trying to explain to someone code who doesn't have experience with neither and it was much harder to do for rxJS (redux-observable)

> "My advice at the moment for most people is that if you have to ask which one you should use, you probably should choose redux-saga." ~Jay Phelps

Currently Angular2 also uses rxJS, so in future probably more and more people will be familiar with concepts and the conclusion may be completely different.
Till then, I believe that without team that is ready to spend time learning rxJS specifics or having at least one person fluent in it, going for redux-observable will introduce quite a challenge.


Community, Examples - best practices
---

Those are connected due to the fact that the more extensive community, the more problems were encountered and resolved. Behind redux-saga there is relatively big community, as it is de facto library for handling any non trivial async flows.
 
Redux-observable is a nice library which bases on features of rxJS, so community can be expanded to people using rxJS. But it probably won't be in same context.

I promised some numbers:

2028 stars for redux-observable (Apr 30, 2016 version 0.1.0)
6154 stars for redux-saga (Dec 2, 2015 version 0.1.0)

Stack-overflow:

185 questions - redux-observable, very small amount of questions, but doesn't necessary mean anything (look below))

11 024 questions - rxJS, as redux-observable is almost entirely wrapper around rxJS this shows that there is a lot going on with this lib.

537 questions - redux-saga, relatively small amount of questions, either library is so 'easy' or there are not 'so', many projects using it.

Job offers:
This was a poor metric as it looks like job offers state mostly "redux". Without specifying any concrete library knowledge.
The rxJS is searchable but it is almost always connected with angular 2 jobs.

 
Longevity
---

In JavaScript world life expectancy of libraries change from day to day, but if I were to guess I would tell that redux-observable with its rxJS base will outlive sagas.
Reactive and functional programming is getting quite a traction in recent years so probably amount of developers and tools will further increase.
 
 
Testability
---

Redux-saga is not actually running any actions, so it is very easy to test it, you can check if result of the next action was something you expected. 
Transformations, are pure functions.

Redux-observable requires mocking in order to be testable and the tests, aren't as 'pure' as it is the case with redux-saga.

From testing perspective redux-saga is a big winner here. 

Examples from redux-saga repository:
```javascript
import { put, call, takeEvery } from '../../../../src/effects'
import { delay } from '../../../../src'

export function* incrementAsync() {
  yield call(delay, 1000)
  yield put({type: 'INCREMENT'})
}

export default function* rootSaga() {
  yield takeEvery('INCREMENT_ASYNC', incrementAsync)
}
```

```javascript
import test from 'tape';

import { put, call } from '../../../src/effects'
import { delay } from '../../../src'
import { incrementAsync } from '../src/sagas'

test('incrementAsync Saga test', (t) => {
  const generator = incrementAsync();

  t.deepEqual(
    generator.next().value,
    call(delay, 1000),
    'counter Saga must call delay(1000)'
  );
  
  t.deepEqual(
    generator.next().value,
    put({type: 'INCREMENT'}),
    'counter Saga must dispatch an INCREMENT action'
  );

  t.deepEqual(
    generator.next(),
    { done: true, value: undefined },
    'counter Saga must be done'
  );

  t.end();
});
```

You can notice that the only thing tested was if generator returned 'call' to specific action, as it result.


Tests of redux-observable also from official example https://redux-observable.js.org/docs/recipes/WritingTests.html
```javascript
import nock from 'nock';
import expect from 'expect';
import configureMockStore from 'redux-mock-store';
import { createEpicMiddleware } from 'redux-observable';
import { fetchUserEpic, fetchUser, FETCH_USER } from '../../redux/modules/user';

const epicMiddleware = createEpicMiddleware(fetchUserEpic);
const mockStore = configureMockStore([epicMiddleware]);

describe('fetchUserEpic', () => {
  let store;

  beforeEach(() => {
    store = mockStore();
  });

  afterEach(() => {
    nock.cleanAll();
    epicMiddleware.replaceEpic(fetchUserEpic);
  });

  it('produces the user model', () => {
    const payload = { id: 123 };
    nock('http://example.com/')
      .get('/api/users/123')
      .reply(200, payload);

    store.dispatch({ type: FETCH_USER });

    expect(store.getActions()).toEqual([
      { type: FETCH_USER },
      { type: FETCH_USER_FULFILLED, payload }
    ]);
  });
});
```

The first thing to notice is how much mocking is necessary in order to test the code, there is a lot going on in this short example.
We create mock of middleware, we create mock store and we mock xhr Request. Probably it is easy to get used to such setup. But it is much less verbose then redux-saga testing.

## Conclusion

For the project I will be working on I have decided to pick redux-saga, it allows for much more 'reasonable' code. It is easy to test and is quite easy to learn.
After trying both solutions however I would not treat redux-observable as 'less of a library', that should be considered only after redux-saga.  
I cannot assume profile of developers that will work with me on the project, so I cannot expect knowledge about concepts like functional programming nor Observables. 
But if you had team that already knew these principles I would probably reconsider redux-observable (rxJS). The paradigm of it is probably the future of JavaScript. 
And with the growing community of Angular 2 it will be probably much easier to find developers knowledgeable in rxJS.


