# Deep Dive: Django & The React Ecosystem (Web + Mobile)

The Ciklum stack combines the robustness of Django with the versatility of the React ecosystem.

## 1. Django REST Framework (DRF) Best Practices
*   **Custom Serializers:** Handling complex data relationships without overloading the CPU.
*   **Permissions:** Implementing RBAC (Role-Based Access Control) for different sustainability stakeholders.
*   **Filtering:** Using `django-filter` to allow users to drill down into resource efficiency data.

## 2. Shared Logic: React & React Native
*   **Hooks for Data Fetching:** Creating custom hooks (e.g., `useSustainabilityData`) that can be used in both the Web dashboard and the Mobile app.
*   **Theme Consistency:** Using a shared theme configuration (e.g., Styled Components or Tailwind) to maintain branding across platforms.
*   **Asset Management:** Sharing SVGs and icons between React and RN.

## 3. High-Autonomy Engineering
*   **Code Quality:** Enforcing 80%+ test coverage and using pre-commit hooks (Linters, Formatters).
*   **Mentorship:** Conducting "Senior-level" code reviews that focus on architectural alignment rather than just syntax.
*   **Project Delivery:** Breaking down complex sustainability features into manageable "Sprints" and "User Stories."

## 4. Sustainability-Focused Tech
*   **Supply Chain Visualization:** Using libraries like D3.js or Chart.js (React) to visualize waste reduction.
*   **Offline First (Mobile):** Using `AsyncStorage` or `SQLite` in React Native for workers in low-connectivity supply chain nodes.

## 5. Coding Exercise: DRF Optimizer
```python
# Before: N+1 issue
class WasteListView(ListAPIView):
    queryset = WasteRecord.objects.all() # Lazy load related location
    serializer_class = WasteSerializer

# After: Optimized with select_related
class WasteListView(ListAPIView):
    queryset = WasteRecord.objects.select_related('location').all()
    serializer_class = WasteSerializer
```

## Interview Questions
1.  "How do you share code between a React web project and a React Native mobile project effectively?"
2.  "Describe a time you used Django to solve a complex business challenge. What trade-offs did you make?"
3.  "How do you ensure data integrity when syncing offline mobile updates back to a Django backend?"
4.  "What is your approach to 'Pragmatic' software delivery in a high-autonomy team?"
