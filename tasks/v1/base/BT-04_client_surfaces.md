# Base Task: BT-04 — Client Applications & Shared Mobile Shell

## Objective
Build the three specialized web user interfaces (`web-family`, `web-facility`, `web-admin`) and the cross-platform shared mobile application shell (`mobile`).

## Business / System Value
Provides tailored user experiences for families looking for care, facility operators managing leads and listings, internal admins managing compliance and data verification, and mobile app users.

---

## Subtask Registry

- [ ] [ST-11: B2C Family Search Portal (Next.js)](file:///c:/Akshat/idea/TrueCare/agent_buildable_base/tasks/v1/sub/ST-11_web_family_b2c_app.md)
  - Details: SEO-friendly search landing page, interactive map views, facility detail pages, multi-facility comparison view, saved searches, and lead/tour request forms.
- [ ] [ST-12: B2B Facility Management Hub (Next.js / React)](file:///c:/Akshat/idea/TrueCare/agent_buildable_base/tasks/v1/sub/ST-12_web_facility_b2b_app.md)
  - Details: Profile claiming flow, admissions/pricing editor, staff ratio inputs, CRM lead inbox, tour scheduling calendar, and org member management.
- [ ] [ST-13: Internal Admin & ERP Dashboard (Next.js / React)](file:///c:/Akshat/idea/TrueCare/agent_buildable_base/tasks/v1/sub/ST-13_web_admin_erp_app.md)
  - Details: Human-in-the-loop verification queue UI, staged data approval table, ingestion run health dashboard, AKS billing auditing, and support user impersonation logs.
- [ ] [ST-14: Shared Mobile Application Shell (React Native / Flutter)](file:///c:/Akshat/idea/TrueCare/agent_buildable_base/tasks/v1/sub/ST-14_mobile_shell_app.md)
  - Details: Shared component shell, authentication wrappers, native location services, and responsive view adapters for iOS and Android.

---

## Complexity Rating
**High** — Requires constructing 3 web applications and 1 cross-platform mobile shell with responsive UI, map integrations, and real-time backend API consumption.

## Acceptance Criteria
- `web-family` renders fast SEO pages, interactive PostGIS maps, semantic search bars, and comparative facility view models.
- `web-facility` enables facility admins to claim listings, update clinical/pricing data, and manage incoming leads.
- `web-admin` allows data admins to review staged OCR/PDF items, approve/reject low-confidence AI outputs, and monitor ingestion health.
- `mobile` application builds and runs in iOS/Android simulators with spatial location search enabled.
