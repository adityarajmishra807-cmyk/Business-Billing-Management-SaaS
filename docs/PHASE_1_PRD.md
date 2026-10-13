# Phase 1 PRD — Foundation & Authentication

**Project:** Business Billing & Management SaaS  
**Prepared by:** Horizon Works  
**Phase:** 1 of 6 — Foundation & Authentication  
**Target duration:** Days 1–3, subject to final requirements and timely feedback  
**Status:** Draft for approval

## 1. Purpose

Establish the technical and security foundation for the SaaS before building billing, inventory, purchases, and reporting modules. Phase 1 creates the application shell, authentication, business profiles, basic dashboard, and a secure data model that keeps each business's information isolated.

## 2. Goals

- Create a working Next.js web application using TypeScript and Tailwind CSS.
- Connect the application to Supabase for authentication and database services.
- Allow users to register, sign in, sign out, and access protected pages.
- Let an account create and manage up to **3 business profiles**.
- Store basic business information and select an active business.
- Provide a basic dashboard shell and business settings.
- Enforce business-level data access in the database, not only in the UI.

## 3. Users and access

### Account owner
- Registers and signs in to the platform.
- Creates, edits, and selects their business profiles.
- Can access only businesses associated with their account.

### Basic team user (foundation only)
- The data model and authorization structure should allow a business owner to grant limited access to another user in a later phase.
- A complete team invitation and employee-management workflow is not included unless explicitly approved as part of Phase 1.

### Platform administrator
- No separate, fully featured super-admin console is included in Phase 1. The application should be structured so one can be added in a later phase.

## 4. Functional requirements

### FR-1: Application foundation
- Set up the agreed Next.js, TypeScript, and Tailwind CSS project.
- Establish a consistent application layout, navigation shell, form styles, loading states, and error states.
- Configure environment variables for local and production environments.
- Keep all secret keys out of client-side code and version control.

### FR-2: Authentication
- Users can register and sign in using the agreed Supabase Auth method.
- Users can sign out.
- Protected application routes require a valid session.
- Signed-out users are redirected to the sign-in screen.
- Show understandable validation and authentication error messages.
- Password reset or email verification should follow the authentication settings agreed before implementation.

### FR-3: Business profiles
- A signed-in account can create a business profile.
- Each profile stores, at minimum:
  - Business name (required)
  - Business type (optional)
  - Contact email and phone (optional)
  - Address (optional)
  - GSTIN (optional field only; no GST portal verification in this phase)
  - Logo URL or logo reference (optional; upload storage may be added if approved)
- Users can view and edit their own business profiles.
- One account can create up to 3 business profiles for the quoted Version 1 scope.
- Users can select an active business from the businesses they are permitted to access.
- Empty states should guide first-time users to create their first business.

### FR-4: Dashboard shell
- Show a basic dashboard after sign-in.
- Display the active business name and a clear path to business settings or business switching.
- During Phase 1, use simple setup/empty-state panels for modules not yet implemented (invoices, inventory, purchases, expenses, and reports).
- Do not display fabricated financial metrics or sample transaction totals as real account data.

### FR-5: Database and business isolation
- Create the initial schema for user profiles, business profiles, and user-to-business membership/ownership.
- Use stable IDs and timestamps; add appropriate foreign keys and indexes.
- Enable and test Supabase Row Level Security (RLS) for tables containing user or business data.
- A user must not read, modify, or delete another account's profile or a business they are not authorized to access, even by changing IDs in a request.
- Use server-side authorization checks for sensitive operations in addition to RLS.
- Never expose the Supabase service-role key in browser code.

## 5. Initial data model (proposed)

### profiles
- id — UUID matching the authenticated user's ID
- full_name — optional
- created_at, updated_at

### businesses
- id — UUID primary key
- name — required
- business_type, contact_email, phone, address, gstin, logo_url — optional
- created_by — owner user ID
- created_at, updated_at

### business_members
- id — UUID primary key
- business_id — foreign key to businesses
- user_id — foreign key to authenticated user/profile
- role — e.g. owner or member
- created_at
- Unique constraint on (business_id, user_id)

The schema is an initial proposal and may be adjusted during implementation. RLS policies must reflect the final schema and be tested before the phase is considered complete.

## 6. User flows

### First-time user
1. Opens the application.
2. Registers and signs in.
3. Sees an empty-state prompt to create a business.
4. Enters required business details and saves.
5. Lands on the dashboard for the newly created business.

### Returning user
1. Signs in.
2. Sees the last active business or a business-selection screen.
3. Opens the dashboard for a permitted business.
4. Can open business settings, edit details, or switch to another permitted business.

### Business limit
- When an account already owns 3 business profiles, creating a fourth is blocked with a clear explanation.

## 7. Non-functional requirements

- Responsive layout for desktop and mobile browsers.
- Basic accessibility: labeled form fields, visible focus states, keyboard-operable controls, and readable error messages.
- Security-sensitive checks must be enforced server-side and in RLS.
- Use consistent, understandable error handling; do not leak internal secrets or database details to users.
- Keep the structure maintainable so future modules can use the active business context.
- Provide a documented list of required environment variables without including live secrets.

## 8. Out of scope for Phase 1

- Invoice creation, GST calculations, PDF generation, quotations, and delivery challans.
- Product, supplier, purchase, expense, inventory, and report modules.
- Government APIs, GSTIN verification, IRN/e-invoice generation, e-way bills, and direct GST return filing.
- Payment gateway and recurring subscription billing.
- Full super-admin console, team invitations, detailed permission editor, and advanced audit logs.
- Native mobile applications.
- Production domain purchase or paid third-party subscriptions.

These will be handled in later phases or quoted separately if the approved scope changes.

## 9. Acceptance criteria

Phase 1 is complete when all of the following are demonstrated:

1. The application runs locally and the agreed build command completes without errors.
2. A user can register, sign in, sign out, and access protected pages as configured.
3. Signed-out users cannot access protected application screens.
4. An authenticated account can create, view, edit, and select its business profiles.
5. The account can create no more than 3 business profiles under the quoted initial scope.
6. A user's data access is enforced by RLS and server-side checks, not merely by hiding UI controls.
7. A user cannot access another user's business by changing a business ID or request.
8. The dashboard displays the selected business and useful empty states for future modules.
9. Required environment variables are documented and no credentials or service-role keys are committed.
10. A short handover note lists setup steps, schema/migration steps, and known limitations.

## 10. Dependencies and decisions required

Before or during implementation, confirm:
- Supabase project and access ownership.
- Preferred authentication method and whether email verification is required.
- Exact business fields and whether logo upload is needed now.
- Whether Phase 1 must include any team-user workflow, or only prepare the data model for it.
- Hosting provider and who owns the production account.
- Final UI direction and logo/brand assets.

## 11. Risks and mitigations

- **Incorrect tenant isolation:** test RLS using at least two separate users and businesses before sign-off.
- **Scope expansion:** freeze Phase 1 requirements; add new requests through a separate written change.
- **Missing configuration:** document environment variables and ensure credentials are managed outside source control.
- **Unclear account ownership:** use client-owned production accounts wherever practical and confirm access before deployment.

## 12. Deliverables

- Working application foundation and navigation shell.
- Supabase authentication integration.
- Initial database schema/migrations for profiles, businesses, and memberships.
- Tested RLS policies for Phase 1 tables.
- Business profile creation, editing, listing, and selection.
- Basic dashboard and future-module empty states.
- Setup and handover notes.

## 13. Sign-off

**Client representative:** __________________________  
**Approval:** Approved / Changes requested  
**Date:** __________________________

**Horizon Works:** __________________________  
**Date:** __________________________
