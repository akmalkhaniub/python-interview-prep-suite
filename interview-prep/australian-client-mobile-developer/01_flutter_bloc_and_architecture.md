# Deep Dive: Flutter BLoC & Clean Architecture

For a 7-10 year experience role, the interviewers will expect you to design systems, not just write widgets.

## 1. Why BLoC?
- **Separation of Concerns:** Clearly separating Business Logic (BLoC) from the UI (Widgets).
- **Testability:** BLoCs are logic-heavy and can be tested 100% without a UI or device.
- **Predictability:** State transitions are explicit (Events -> Logic -> States).

## 2. Advanced BLoC Patterns
*   **Bloc-to-Bloc Communication:** Using a `StreamSubscription` to listen to one BLoC inside another, or providing multiple BLoCs via a single `MultiBlocProvider`.
*   **State Concurrency:** Using `transformer` in BLoC 8+ to handle event concurrency (e.g., `droppable()` to ignore events while one is processing, or `restartable()` to cancel the previous event).
*   **Error Handling:** Using a base `Failure` class and emitting `ErrorState` with specific metadata for the UI to display SnackBar/Dialogs.

## 3. Clean Architecture in Flutter
A standard senior-level architecture:
- **Data Layer:** Repositories and Data Sources (APIs, Local DB).
- **Domain Layer:** Entities (Plain data classes) and Use Cases (Business rules).
- **Presentation Layer:** BLoCs and Widgets.

## 4. Flutter Web Specifics
*   **Navigator 2.0:** Essential for Web URL routing and handling the "Back" button in the browser.
*   **CORS:** Understanding how to handle Cross-Origin Resource Sharing when hitting APIs from the browser.
*   **Package Compatibility:** Always check `pub.dev` for the "Web" tag before adding a package.

## 5. Coding Exercise: BLoC Test
```dart
blocTest<WeatherBloc, WeatherState>(
  'emits [Loading, Loaded] when WeatherRequested is added',
  build: () => WeatherBloc(weatherRepository: mockRepository),
  act: (bloc) => bloc.add(WeatherRequested(city: 'Sydney')),
  expect: () => [
    WeatherLoading(),
    WeatherLoaded(weather: mockWeather),
  ],
);
```

## Interview Questions
1.  "What is the difference between `Bloc` and `Cubit`, and when would you prefer one over the other?"
2.  "How do you handle 'state hydration' (persisting state across app restarts) in BLoC?"
3.  "Describe a time you optimized a Flutter app that had jank/stuttering. What tools did you use?"
4.  "How do you manage complex form validation using BLoC?"
