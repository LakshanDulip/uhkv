# School portal: remaining security and authentication checks

## Scope of current frontend patch
- Defensively handle missing gallery URL arrays and reject unsupported photo MIME types.
- Handle independent Firebase reads with Promise.allSettled, reporting failures in the console.
- Hide targeted/unpublished notices from public homepage rendering.
- Guard login attempts until Firebase Auth initializes.

## Important: client display filtering is NOT data access security
The public page still queries the `notices` collection. Firestore Security Rules must enforce target-based access without exposing private teacher/student notices to anonymous browsers. Prefer publishing approved announcements to a dedicated `publicNotices` collection and restricting `notices` to authenticated authorized roles. Verify the deployed Firestore rules before rollout.

## Authentication
The current main branch has secondary Firebase Auth client-side account provisioning. Verify that authorized administrators alone can provision accounts, that role records cannot be forged, that UID linkage is consistent, and that failures do not leave partial user accounts. For production, use trusted server-side Firebase Admin SDK endpoints or Cloud Functions with authorization checks. Validate that both student and teacher password reset controls update credentials rather than just displaying a generated string. Avoid distributing passwords until a real account creation flow is confirmed.

## Manual validation checklist
- Gallery albums with missing, empty, malformed and populated `urls`.
- On a Firebase permission-denied response for one collection, check whether other homepage sections still populate.
- Test login with offline and slow Firebase initialization.
- Query targeted notices as an unauthenticated user: the server must deny access, not merely hide them from rendered HTML.
- Test end-to-end creation/login/reset for students and teachers in a staging Firebase project.
- Confirm actual deployed Firestore Security Rules and the student/teacher portal role guards.

No live production database or Firebase Security Rules were changed by this patch.
