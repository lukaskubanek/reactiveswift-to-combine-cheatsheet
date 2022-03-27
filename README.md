A Cheat Sheet of various equivalent of ReactiveSwift for Combine.

This list is by no mean exhaustive, and while each Combine equivalent try to have the same semantic as its ReactiveSwift, there might be cases where the behavior differs in subtle manners.  
Please do report these cases so we can update the list accordingly.

## Basics

|                       | ReactiveSwift                                                                                             | Combine                                                                                                     |
|-----------------------|-----------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------|
| Platforms supported   | • iOS 8.0+<br />• macOS 10.9+<br />• Mac Catalyst 13.0+<br />• watchOS 2.0+<br />• tvOS 9.0+<br />• Linux | • iOS 13.0+<br />• macOS 10.15+<br />• Mac Catalyst 13.0+<br />• watchOS 6.0+<br />• tvOS 13.0+<br />&nbsp; |
| Framework Consumption | Third-party                                                                                               | First-party (built-in)                                                                                      |
| Maintained by         | Open-Source / Community                                                                                   | Apple                                                                                                       |
| UI Bindings           | ReactiveCocoa (interfacing with UIKit)                                                                    | SwiftUI<sup>[1]</sup>                                                                                       |

<small>[1] Is actually a full UI framework replacing UIKit, that natively supports Combine.</small>

## Components

Note that in Combine, anything can be transformed in an `AnyPublisher` through `eraseToAnyPublisher()`.  
This means that all the equivalent given for Combine can be returned as an `AnyPublisher`.

### Core

| ReactiveSwift         | Combine                                                                                                                | Notes                                                                                                            |  |
|-----------------------|------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------|--|
| `SignalProducer`      | `Deferred<PassthroughSubject>`                                                                                         |                                                                                                                  |  |
| `Signal`              | `PassthroughSubject`                                                                                                   |                                                                                                                  |  |
| `Property`            | `private(set)` variable annotated with `@Published` (use `$variable` to get the underlying publisher)                  |                                                                                                                  |  |
| `MutableProperty`     | `CurrentValueSubject`, or a variable annotated with `@Published` (use the `$variable` to get the underlying publisher) |                                                                                                                  |  |
| `ValidatingProperty`  | ❌                                                                                                                      |                                                                                                                  |  |
| `Action`              | ❌                                                                                                                      | An equivalent exists at https://github.com/Fueled/ios-utilities/blob/v3-support/FueledUtils/Combine/Action.swift |  |
| `Disposable`          | `Cancellable`                                                                                                          |                                                                                                                  |  |
| `SerialDisposable`    | ❌                                                                                                                      | No direct equivalent, see `ScopedDisposable<SerialDisposable>`                                                   |  |
| `CompositeDisposable` | ❌                                                                                                                      | No direct equivalent, see `ScopedDisposable<CompositeDisposable>`                                                |  |
| `Event`               | ❌                                                                                                                      | No direct equivalent                                                                                             |  |
| `Lifetime`            | ❌                                                                                                                      |                                                                                                                  |  |
| `Observer`            | `Subscriber`                                                                                                           | `AnySubscriber` can be used to create a `Subscriber` without creating a type that conforms to the former         |  |
| `Scheduler`           | `Scheduler`                                                                                                            | `DispatchQueue`, `RunLoop`, `ImmediateScheduler`, `OperationQueue` conforms to `Scheduler`                       |  |
| `DateScheduler`       | `Scheduler`                                                                                                            |  |  |
| `Atomic`              | ❌                                                                                                                      | An equivalent called `AtomicValue` exists at https://github.com/Fueled/ios-utilities/blob/v3-support/FueledUtils/Core/Atomic.swift#L19-L46 |  |
| `Bag`                 | ❌                                                                                                                      |  |  |

### Other

| ReactiveSwift                                  | Combine                                                          | Notes                                                                                                                                 |  |
|------------------------------------------------|------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------|--|
| `SignalProducer<Value, Failure>(value: value)` | `Just(value).setFailureType(Failure.self)`                       |                                                                                                                                       |  |
| `SignalProducer<Value, Failure>(error: error)` | `Fail(outputType: Value.self, failure: error)`                   |                                                                                                                                       |  |
| `SignalProducer<Value, Failure>.empty`         | `Empty().setFailureType(Failure.self)`                           |                                                                                                                                       |  |
| `SignalProducer<Value, Failure>.never`         | `Empty(completeImmediately: false).setFailureType(Failure.self)` |                                                                                                                                       |  |
| `Signal<Value, Failure>.empty`                 | `Empty().setFailureType(Failure.self)`                           |                                                                                                                                       |  |
| `Signal<Value, Failure>.never`                 | `Empty(completeImmediately: false).setFailureType(Failure.self)` |                                                                                                                                       |  |
| `Property<Value>(value: value)`                | `Just(value).setFailureType(Failure.self)`                       |                                                                                                                                       |  |
| `ScopedDisposable<SerialDisposable>`           | `AnyCancellable`                                                 |                                                                                                                                       |  |
| `ScopedDisposable<CompositeDisposable>`        | `Collection` of `AnyCancellable`s                                | Call `anyCancellable.store(in: collection)`, where `collection` can be an `Array`, a `Set`, or any other `RangeReplaceableCollection` |  |

## Operators

| ReactiveSwift                         | Combine                                                                                                                               | Notes                                                                                                                                       |
|---------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------|
| `combineLatest()`                     | `Publishers.CombineLatest`/`Publishers.CombineLatest3`/`Publishers.CombineLatest4`                                                    | Limited to up to 4 parameters, as opposed to 10 for `ReactiveSwift`.                                                                        |
| `flatMap(.merge) { <...> }`          | `flatMap { <...> }`                                                                                                                   |                                                                                                                                             |
| `flatMap(.latest) { <...> }`          | `map { <...> }.switchToLatest()`                                                                                                                   |                                                                                                                                             |
| `mapError`                            | `mapError`                                                                                                                            |                                                                                                                                             |
| `flatMapError`                        | `catch`                                                                                                                               |                                                                                                                                             |
| `zip`                                 | `Publishers.zip/Publishers.zip2/Publishers.zip3`                                                                                      |                                                                                                                                             |
| `merge(one, two, ..., eight)`         | `Publishers.Merge`/`Publishers.Merge2`/.../`Publishers.Merge8`                                                                        |                                                                                                                                             |
| `merge([])`                           | `Publishers.MergeMany`                                                                                                                |                                                                                                                                             |
| `start()`                             | `self.sink(receiveCompletion: { _ in }, receiveValue: { _ in })`                                                                      |                                                                                                                                             |
| `startWithValues { value in <...> }`  | `self.sink(receiveValue: { value in <...> })`                                                                                         |                                                                                                                                             |
| `startWithResult { result in <...> }` | `self.sink(receiveCompletion { completion in if case .failure(let error) = completion { <...> } }, receiveValue: { value in <...> })` |                                                                                                                                             |
| `ignoreError()`                       | `self.catch { _ in Empty() }`                                                                                                         |                                                                                                                                             |
| `skipNil()`                           | ❌                                                                                                                                     | An equivalent can be found at https://github.com/Fueled/ios-utilities/blob/v3-support/FueledUtils/Combine/PublisherExtensions.swift#L36-L38 |
| `skip(first:)`                        | `dropFirst()`                                                                                                                         |                                                                                                                                             |
| `skip(until:)`                        | `drop(untilOutputFrom:)`                                                                                                              |                                                                                                                                             |
| `skip(while:)`                        | `drop(while:)`                                                                                                                        |                                                                                                                                             |
| `scan()`                              | `scan()`                                                                                                                              |                                                                                                                                             |
| `throttle()`                          | `throttle()`                                                                                                                          |                                                                                                                                             |
| `SignalProducer<Date, Never>.timer()` | `Timer.publish().autoconnect()`                                                                                                       |                                                                                                                                             |
| `prefix()`                            | `prepend()`                                                                                                                           |                                                                                                                                             |
| `on()`                                | `handleEvents()`                                                                                                                      |                                                                                                                                             |
| `<~`                                  | If binding to a property on an class, can use `.assign(to:, on:)`. Keep in mind that this will retain the object.                     |                                                                                                                                             |
| `compositeDisposable += <disposable>` | `store(in: &cancellables)`                                                                                                            |                                                                                                                                             |

> Format inspired by https://github.com/CombineCommunity/rxswift-to-combine-cheatsheet
