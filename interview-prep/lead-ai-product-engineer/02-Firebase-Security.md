# 02: Firebase & Security Mastery

For this role, mastery of Firebase isn't just about reading/writing data; it's about bulletproof security and multi-tenancy.

## 1. Firestore Security Rules (Multi-tenancy)
The most important part of "absolute data isolation".

### Example: Tenant-based Isolation
```javascript
service cloud.firestore {
  match /databases/{database}/documents {
    // Helper function to check if user belongs to the tenant
    function isTenantMember(tenantId) {
      return request.auth != null && 
             get(/databases/$(database)/documents/users/$(request.auth.uid)).data.tenantId == tenantId;
    }

    match /tenants/{tenantId}/data/{document=**} {
      allow read, write: if isTenantMember(tenantId);
    }
  }
}
```

## 2. Firebase Auth & Custom Claims
- Use **Custom Claims** to store `role` (admin, user) and `tenantId` in the JWT.
- This avoids repeated database lookups in Security Rules.
- Example: `request.auth.token.tenantId == tenantId`.

## 3. Real-time Database vs. Firestore
- **Firestore:** Better for structured data, querying, and scaling.
- **RTDB:** Better for high-frequency, low-latency updates (e.g., live content generation status).

## 4. Cloud Functions (Node.js/Python)
- **Background Triggers:** Automatically creating a "Company" document when a user signs up.
- **Scheduled Functions:** Generating daily MRR reports or cleaning up expired tokens.

## 5. Potential Interview Questions
1. **What is the "limit of 10" in Firestore Security Rules and how do you work around it?**
   - *Answer:* You can have up to 10 `get()` or `exists()` calls per request. To scale beyond this, use Custom Claims in Auth tokens to store permissions.
2. **How do you handle a data migration in Firestore for a SaaS app with 100+ tenants?**
   - *Answer:* Use a script that iterates through collections. For large migrations, use a background Cloud Function or a Google Cloud Task queue to avoid timeouts. Always back up using `gcloud firestore export` first.
3. **How do you implement "Usage-based" limits in Firestore?**
   - *Answer:* Maintain a `usage` counter in a document. Before allowing a write, a Security Rule checks if `usage < limit`. Update the counter via a Cloud Function (to ensure atomic increments).
