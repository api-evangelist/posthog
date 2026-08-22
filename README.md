# PostHog (posthog)

<!-- API-EVANGELIST-PROVENANCE:BEGIN -->
> ### About this repository
>
> **This is not our API.** This repository is an independent, third-party profile of a company's
> **publicly available** API surface, maintained by [API Evangelist](https://apievangelist.com).
> API Evangelist does not operate, host, resell, or support this company's APIs, and is not
> affiliated with or endorsed by the company unless stated on the profile.
>
> **Where the information came from.** Everything here is assembled from material a member of the
> public can reach with a browser and no credentials — the company's own website, developer portal
> and documentation, the specifications it publishes for public use (OpenAPI, AsyncAPI, JSON Schema,
> `apis.json`, `llms.txt` and similar), its public repositories, and its public status, pricing and
> changelog pages. **Nothing here is obtained by breaching a system, defeating an access control, or
> using credentials of any kind.**
>
> **The rating is an independent assessment.** The Kin Score and Agent Readiness rating are
> independently calculated scores of a company's *public* API artifacts, produced by API Evangelist
> against a published rubric. They are not certifications, endorsements, security assessments, or
> audits, and they score published artifacts — not the quality, safety, or security of the software.
>
> **Corrections, re-scores, and removal are free.** No partnership, contract, or purchase is
> required, and you do not need to justify the request.
>
> - **Something wrong?** Open an issue on this repository, or email
>   [info@apievangelist.com](mailto:info@apievangelist.com).
> - **Published something new?** Ask for a re-score and we will re-run the rating.
> - **Want the listing taken down?** Say so and we will honor it. The profile is reduced to your
>   company name, a factual description, and a link to your own site, and the company is recorded as
>   **unrated** — never scored zero for having asked.
>
> **Response times.** Acknowledgement within **one business day**; removal or restriction within
> **two business days**; corrections and re-scores within **five business days**.
>
> **Not from the company, and here with a question?** You are welcome here — we would rather be the
> front line and point you the right way than have a good report go nowhere. What this repository
> can answer is narrow, though, so it is worth knowing who you are actually looking for:
>
> - **A question about how the API works, an account, billing, or a bug in the service** — that is
>   the company's own support, not us. We profile this API; we do not operate it and cannot see
>   your account.
> - **A bug in an open-source project we only catalog** — file it on that project's own repository.
>   This has happened with a real and correct bug report that reached us instead of the people who
>   could fix it, which helped nobody.
> - **Anything about this listing itself** — the description, the tags, the rating, a missing or
>   wrong artifact — is ours. Open an issue here.
> - **Not sure, or something general about API Evangelist or APIs.io** — open an issue on the
>   [APIs.io Inbox](https://github.com/api-search/inbox) and we will route it.
>
> **This repository contains no software, and we will never ask you to download anything.** There is
> no build, release, installer, or binary here — only text and machine-readable API descriptions, so
> there is nothing here that can be "corrupt" or need "repairing". Any issue, comment, or email
> claiming otherwise and offering a download link is not from us and is hostile. Do not follow the
> link; it is a lure. Report it to GitHub and, if you like, tell us at
> [info@apievangelist.com](mailto:info@apievangelist.com) so we can take it down.
>
> **On a security or compliance team?** Email
> [info@apievangelist.com](mailto:info@apievangelist.com) with *security* in the subject line and
> you will get a person, not a form. We will tell you exactly which public URLs this profile was
> built from so your team can see the same surface we did, and we will take the listing down on
> request while you work through it.
>
> Full detail: **[Where this data comes from](https://apievangelist.com/about/where-our-data-comes-from)**
<!-- API-EVANGELIST-PROVENANCE:END -->

PostHog is an open source product analytics platform that provides event tracking, session recording, feature flags, A/B testing, and user surveys in a single platform. It can be self-hosted or used as a cloud service, giving teams full control over their product data while providing comprehensive tools for understanding and improving user experiences.

**APIs.json:** [https://raw.githubusercontent.com/api-evangelist/posthog/refs/heads/main/apis.yml](https://raw.githubusercontent.com/api-evangelist/posthog/refs/heads/main/apis.yml)

## Scope

- **Type:** Index

## Tags

- A/B Testing
- Analytics
- Feature Flags
- Open Source
- Product Analytics
- Session Recording

## Timestamps

- **Created:** 2026-03-26
- **Modified:** 2026-05-30

## APIs

### PostHog API

The PostHog API is a single consolidated REST interface that exposes every PostHog capability available in the UI, including event capture, person and group profiles, cohorts, feature flags, experiments, insights, dashboards, annotations, sessions, surveys, web analytics, and HogQL queries. It supports project-scoped and personal API key authentication for both PostHog Cloud and self-hosted deployments.

- **Human URL:** [https://posthog.com/docs/api](https://posthog.com/docs/api)
- **Base URL:** `https://app.posthog.com/api`

#### Tags

- Analytics
- Annotations
- Cohorts
- Dashboards
- Events
- Experimentation
- Feature Flags
- HogQL
- Insights
- Persons
- Product Analytics
- Surveys

#### Properties

- [Documentation](https://posthog.com/docs/api)
- [Getting Started](https://posthog.com/docs/api)
- [Authentication](https://posthog.com/docs/api#authentication)
- [OpenAPI](openapi/posthog-openapi.yml) — [OpenAPI Specification](https://spec.openapis.org/oas/latest.html)
- [Postman Collection](collections/posthog.postman_collection.json) — [Postman Collection 2.1](https://schema.getpostman.com/json/collection/v2.1.0/collection.json)
- [Open Collection](collections/posthog.opencollection.json) — [Open Collection 1.0](https://schema.opencollection.com/opencollection/v1.0.0.json)
- [AsyncAPI](asyncapi/posthog-webhooks-asyncapi.yml) — [AsyncAPI Specification](https://www.asyncapi.com/docs/reference/specification/latest)

### PostHog Capture API

The PostHog Capture API allows developers to send events, identify users, and set user or group properties from any server or client. It is the primary ingestion endpoint for sending analytics data to PostHog and supports batch event submission, feature flag evaluation, and alias operations for identity resolution.

- **Human URL:** [https://posthog.com/docs/api/capture](https://posthog.com/docs/api/capture)

#### Tags

- Analytics
- Events
- Ingestion
- Tracking

#### Properties

- [Documentation](https://posthog.com/docs/api/capture)
- [Postman Collection](collections/posthog.postman_collection.json) — [Postman Collection 2.1](https://schema.getpostman.com/json/collection/v2.1.0/collection.json)
- [Open Collection](collections/posthog.opencollection.json) — [Open Collection 1.0](https://schema.opencollection.com/opencollection/v1.0.0.json)

### PostHog Query API

The PostHog Query API provides programmatic access to run HogQL queries against your PostHog data. HogQL is PostHog's SQL-like query language that enables custom analytics queries, funnel analysis, retention analysis, and trend calculations. This API allows developers to build custom dashboards and reporting tools powered by PostHog data.

- **Human URL:** [https://posthog.com/docs/api/query](https://posthog.com/docs/api/query)

#### Tags

- Analytics
- HogQL
- Queries
- Reporting

#### Properties

- [Documentation](https://posthog.com/docs/api/query)
- [Postman Collection](collections/posthog.postman_collection.json) — [Postman Collection 2.1](https://schema.getpostman.com/json/collection/v2.1.0/collection.json)
- [Open Collection](collections/posthog.opencollection.json) — [Open Collection 1.0](https://schema.opencollection.com/opencollection/v1.0.0.json)

### PostHog Persons API

The PostHog Persons API allows developers to retrieve, search, and manage person profiles in PostHog. It supports listing persons with filters, retrieving individual person details, viewing associated events and properties, and managing person data including deletions for privacy compliance.

- **Human URL:** [https://posthog.com/docs/api/persons](https://posthog.com/docs/api/persons)

#### Tags

- Analytics
- Identity
- Profiles
- Users

#### Properties

- [Documentation](https://posthog.com/docs/api/persons)
- [Postman Collection](collections/posthog.postman_collection.json) — [Postman Collection 2.1](https://schema.getpostman.com/json/collection/v2.1.0/collection.json)
- [Open Collection](collections/posthog.opencollection.json) — [Open Collection 1.0](https://schema.opencollection.com/opencollection/v1.0.0.json)

### PostHog Feature Flags API

The PostHog Feature Flags API enables developers to create, manage, and evaluate feature flags programmatically. It supports boolean and multivariate flags, percentage-based rollouts, user targeting with property filters, and integration with PostHog's experimentation framework for A/B testing.

- **Human URL:** [https://posthog.com/docs/api/feature-flags](https://posthog.com/docs/api/feature-flags)

#### Tags

- A/B Testing
- Experimentation
- Feature Flags
- Rollouts

#### Properties

- [Documentation](https://posthog.com/docs/api/feature-flags)
- [Postman Collection](collections/posthog.postman_collection.json) — [Postman Collection 2.1](https://schema.getpostman.com/json/collection/v2.1.0/collection.json)
- [Open Collection](collections/posthog.opencollection.json) — [Open Collection 1.0](https://schema.opencollection.com/opencollection/v1.0.0.json)

### PostHog Experiments API

The PostHog Experiments API provides programmatic management of A/B tests and experiments. It supports creating experiments with control and test variants, defining goal metrics, launching and stopping experiments, and retrieving experiment results with statistical significance calculations.

- **Human URL:** [https://posthog.com/docs/api/experiments](https://posthog.com/docs/api/experiments)

#### Tags

- A/B Testing
- Experimentation
- Metrics
- Variants

#### Properties

- [Documentation](https://posthog.com/docs/api/experiments)
- [Postman Collection](collections/posthog.postman_collection.json) — [Postman Collection 2.1](https://schema.getpostman.com/json/collection/v2.1.0/collection.json)
- [Open Collection](collections/posthog.opencollection.json) — [Open Collection 1.0](https://schema.opencollection.com/opencollection/v1.0.0.json)

### PostHog Insights API

The PostHog Insights API allows developers to create, retrieve, and manage saved insights such as trends, funnels, retention charts, paths, and lifecycle analyses. It provides programmatic access to the same analytics visualizations available in the PostHog UI, enabling integration with external dashboards and automated reporting workflows.

- **Human URL:** [https://posthog.com/docs/api/insights](https://posthog.com/docs/api/insights)

#### Tags

- Analytics
- Dashboards
- Insights
- Reporting

#### Properties

- [Documentation](https://posthog.com/docs/api/insights)
- [Postman Collection](collections/posthog.postman_collection.json) — [Postman Collection 2.1](https://schema.getpostman.com/json/collection/v2.1.0/collection.json)
- [Open Collection](collections/posthog.opencollection.json) — [Open Collection 1.0](https://schema.opencollection.com/opencollection/v1.0.0.json)

### PostHog Cohorts API

The PostHog Cohorts API enables developers to create and manage cohorts, which are groups of users defined by shared properties or behavioral patterns. Cohorts can be used for targeting feature flags, filtering analytics queries, and segmenting experiment populations.

- **Human URL:** [https://posthog.com/docs/api/cohorts](https://posthog.com/docs/api/cohorts)

#### Tags

- Analytics
- Cohorts
- Segmentation
- Users

#### Properties

- [Documentation](https://posthog.com/docs/api/cohorts)
- [Postman Collection](collections/posthog.postman_collection.json) — [Postman Collection 2.1](https://schema.getpostman.com/json/collection/v2.1.0/collection.json)
- [Open Collection](collections/posthog.opencollection.json) — [Open Collection 1.0](https://schema.opencollection.com/opencollection/v1.0.0.json)

### PostHog Annotations API

The PostHog Annotations API allows developers to create and manage annotations that mark significant events on PostHog charts and dashboards. Annotations provide context for data changes by recording deployments, marketing campaigns, incidents, and other events that may affect metrics.

- **Human URL:** [https://posthog.com/docs/api/annotations](https://posthog.com/docs/api/annotations)

#### Tags

- Analytics
- Annotations
- Charts

#### Properties

- [Documentation](https://posthog.com/docs/api/annotations)
- [Postman Collection](collections/posthog.postman_collection.json) — [Postman Collection 2.1](https://schema.getpostman.com/json/collection/v2.1.0/collection.json)
- [Open Collection](collections/posthog.opencollection.json) — [Open Collection 1.0](https://schema.opencollection.com/opencollection/v1.0.0.json)

## Common Properties

- [LinkedIn](https://www.linkedin.com/company/posthog)
- [Website](https://posthog.com)
- [Documentation](https://posthog.com/docs)
- [A P I Documentation](https://posthog.com/docs/api)
- [Getting Started](https://posthog.com/docs/getting-started/install)
- [Blog](https://posthog.com/blog)
- [Pricing](https://posthog.com/pricing)
- [Git Hub](https://github.com/PostHog/posthog)
- [Login](https://app.posthog.com/login)
- [Sign Up](https://app.posthog.com/signup)
- [Support](https://posthog.com/questions)
- [Changelog](https://posthog.com/changelog)
- [S D Ks](https://posthog.com/docs/libraries)
- [Self Hosted](https://posthog.com/docs/self-host)
- [Status Page](https://status.posthog.com)
- [Slack](https://posthog.com/slack)
- [Terms of Service](https://posthog.com/terms)
- [Privacy Policy](https://posthog.com/privacy)
- [Features](undefined)
- [L L Ms Txt](https://posthog.com/llms.txt)

## Maintainers

**FN:** Kin Lane
**Email:** kin@apievangelist.com
