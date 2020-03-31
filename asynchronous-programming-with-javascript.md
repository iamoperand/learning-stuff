## Asynchronous Programming in JavaScript

### Iterators and Observers
- **Iterator pattern**: Consumers have the power to pull data from the producer
- **Observer pattern**: Producers have the power to push data to the consumers

```js Iterator pattern
const iterator = [1, 2, 3][Symbol.iterator]();

console.log(iterator.next());
// {value: 1, done: false}

console.log(iterator.next());
// {value: 2, done: false}

console.log(iterator.next());
// {value: 3, done: false}

console.log(iterator.next());
// {done: true}
```

```js Observer pattern
document.addEventListener('click', function(event) {
  console.log(event);
})
```

These 2 patterns are closely linked to each other, but the people working on this way back in 1994 missed this, and it got us to where we are right now.

Observers can't be in the "done" or "error" state. This led to a lot of complexity concerned with asynchronous programming in JavaScript.


**Observables to the rescue**

Observable === Collection + Time

**Iterable** gives you **iterator**
**Observable** gives you **observer**


### Observables

- **Converting Events to Observables**

```js
Observable.fromEvent = (dom, eventName) => {
  // returning Observable object
  return {
    forEach: (observer) => {
      const handler = (event) => observer.onNext(event);
      dom.addEventListener(eventName, handler);

      // returning Subscription object
      return {
        dispose: () => {
          dom.removentEventListener(eventName, handler);
        }
      }
    }
  }
}
```


- **Observable methods**

-- forEach
-- map
-- filter
-- concatAll - method to flatten the 2d collection of observables. Takes care of the order of observables.
```js
{
  ....{1}
  ..........{2................3}
  ..............{}
  ..................{4}
}.concatAll()

// result: {...1.....2.................3..4}
```


- mergeAll - method to flatten the 2d collection of observables. Use first come first serve basis.
```js
{
  ....{1}
  ..........{2................3}
  ..............{}
  ..................{4}
}.mergeAll()

// result: {...1.....2....4........3}
```

- switchLatest - method to flatten the 2d collection of observables. Switches to the latest observable as soon as the latest observable emits, and calls `dispose` on the last used observable.
```js
{
  ....{1}
  ..........{2................3}
  ..............{}
  ..................{4}
}.switchLatest()

// result: {...1.....2....4}
```



- takeUntil - unsubscribe from source collection whenever the stop observable emits either `onNext` or `onCompleted`

### Cold and Hot Observables
Observables don't do anything until you apply **forEach** to them.

Observables that haven't been forEach'ed, are **cold observables**.
Whereas observables that have been forEached are **hot observables**.


**Observables are lazy**

That is they don't do anything, until you apply forEach.



### Thinking in terms of Asynchronous programming
1. What collections / observables do I have?
2. What collections / observables do I want?
3. How do I get from the collections that I have, to the collections that I want?
4. What am I gonna do with the collections that I got from the output


### How Promises are different from Observables?
- Promises can't be cancelled. They can either be resolved to a value, or rejected with an error.
- Promises can just resolve to a single value, not multiple values.
- Promises can't be retried.

So essentially, Observables can do everything that Promises can do, but the opposite is just not true.


## Credits
[Frontend Masters course](https://frontendmasters.com/courses/asynchronous-javascript/) by [Jafar Husain](https://twitter.com/jhusain)