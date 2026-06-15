# PostHog (posthog)

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
