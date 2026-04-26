# 03: MVI & State Management

MVI (Model-View-Intent) is a popular pattern in the Kotlin world for building unidirectional data flow (UDF) applications.

## 1. The MVI Cycle
1. **Intent:** The user performs an action (e.g., clicks a button).
2. **Model (State):** The ViewModel processes the intent and updates the state.
3. **View:** The UI observes the state and renders it.
4. **Side Effects:** Navigation, showing a Toast, or logging (events that aren't part of the state).

## 2. Implementation with `StateFlow`
```kotlin
// State
data class HomeState(
    val isLoading: Boolean = false,
    val items: List<String> = emptyList(),
    val error: String? = null
)

// Intent/Action
sealed interface HomeIntent {
    object LoadItems : HomeIntent
    data class OnItemClick(val id: String) : HomeIntent
}

// Side Effect
sealed interface HomeEffect {
    data class ShowToast(val message: String) : HomeEffect
    data class NavigateToDetail(val id: String) : HomeEffect
}

class HomeViewModel : ViewModel() {
    private val _state = MutableStateFlow(HomeState())
    val state: StateFlow<HomeState> = _state.asStateFlow()

    private val _effect = MutableSharedFlow<HomeEffect>()
    val effect: SharedFlow<HomeEffect> = _effect.asSharedFlow()

    fun handleIntent(intent: HomeIntent) {
        when (intent) {
            HomeIntent.LoadItems -> loadItems()
            is HomeIntent.OnItemClick -> emitEffect(HomeEffect.NavigateToDetail(intent.id))
        }
    }
}
```

## 3. Libraries for MVI in KMP
- **Orbit-MVI:** A simple and powerful MVI framework for Kotlin.
- **MVIKotlin:** A more strict and feature-rich implementation of MVI.
- **Ballast:** A flexible MVI framework with built-in support for time-travel debugging and testing.

## 4. Key Advantages
- **Single Source of Truth:** The state represents the entire UI.
- **Predictability:** Debugging is easier because every UI change is triggered by an Intent.
- **Testability:** You can test the ViewModel by sending Intents and asserting the resulting States.

## 5. Potential Interview Questions
1. **Why use MVI over MVVM?**
   - *Answer:* MVVM often leads to "fragmented state" where multiple LiveData/StateFlows are updated independently. MVI enforces a single State object, making it easier to handle complex UI logic and side effects.
2. **How do you handle "One-time events" (like navigation) in MVI?**
   - *Answer:* Using `SharedFlow` or a dedicated `Effect` stream. Unlike State, effects shouldn't be replayed when the UI is re-composed (e.g., screen rotation).
3. **What is "State Hoisting" in Compose and how does it relate to MVI?**
   - *Answer:* State hoisting is moving state up to make a component stateless. In MVI, we hoist the state to the ViewModel, which then passes it down to the Composables.
