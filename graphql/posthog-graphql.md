# PostHog GraphQL Schema

## Overview

PostHog does not expose a public GraphQL endpoint. Its primary API surface is a REST API documented at https://posthog.com/docs/api, with an additional HogQL query layer (SQL-like) accessible via `POST /api/projects/:project_id/query`.

This schema document models the PostHog domain using GraphQL SDL to provide a canonical, machine-readable representation of its core types. It is derived directly from the open-source PostHog codebase, specifically:

- `frontend/src/queries/schema/schema-general.ts` — the authoritative query/node type definitions
- `posthog/schema.py` — generated Python models (Pydantic) corresponding to the TypeScript types
- REST API endpoint surface at https://posthog.com/docs/api

Source repository: https://github.com/PostHog/posthog

## Schema File

The full GraphQL SDL is in `posthog-schema.graphql`.

## Core Domain Types (58 total)

### Analytics Primitives
| Type | Description |
|------|-------------|
| `Event` | A captured product analytics event stored in ClickHouse |
| `Person` | A known user identified by one or more distinct IDs |
| `Group` | A collection of users sharing a common identifier (company, org, workspace) |
| `Session` | A browser or mobile session captured via session replay |
| `Element` | A DOM element captured during autocapture |

### Segmentation
| Type | Description |
|------|-------------|
| `Cohort` | A static or dynamic group of persons defined by shared properties or behavior |
| `PropertyFilter` | A property filter used to match or segment users, events, or groups |
| `PropertyGroup` | A group of property filters combined with AND/OR logic |
| `PropertyGroupValue` | A nested property filter set within a group |
| `Breakdown` | A single breakdown dimension for a query |
| `BreakdownFilter` | Breakdown configuration for trends, funnels, and other insight types |

### Feature Management
| Type | Description |
|------|-------------|
| `FeatureFlag` | A feature flag that can be conditionally enabled for users or groups |
| `FeatureFlagFilters` | Rollout conditions for a feature flag |
| `FeatureFlagCondition` | A single rollout condition group within a feature flag |
| `MultivariateOptions` | Options for multivariate (A/B/n) feature flags |
| `MultiVariateVariant` | A single variant within a multivariate feature flag |
| `EarlyAccessFeature` | An early access feature gated behind a feature flag |

### Experimentation
| Type | Description |
|------|-------------|
| `Experiment` | An A/B experiment tied to a feature flag |
| `ExperimentParameters` | Configuration parameters for an experiment |
| `ExperimentMetric` | A metric tracked in an experiment |
| `ExperimentStatsConfig` | Statistical method configuration for an experiment |
| `ExperimentResult` | Results of a completed experiment |
| `ExperimentVariantResult` | Per-variant result statistics |

### Insights and Dashboards
| Type | Description |
|------|-------------|
| `Insight` | A saved analytics insight (trend, funnel, retention, paths, etc.) |
| `Dashboard` | A dashboard containing one or more insight tiles |
| `DashboardTile` | A tile on a dashboard referencing a saved insight |
| `DashboardText` | A text tile on a dashboard |
| `Annotation` | An annotation marking a significant event on charts |
| `Subscription` | A subscription to a PostHog insight delivered via email or Slack |
| `AlertThreshold` | An alert that monitors an insight metric and fires when a threshold is crossed |
| `AlertCondition` | The condition type for an alert |
| `AlertThresholdBound` | A bound (upper or lower) for an alert threshold |

### Query Types
| Type | Description |
|------|-------------|
| `HogQLQuery` | A HogQL (SQL-like) query executed against PostHog event data |
| `HogQLQueryResponse` | Response from a HogQL query execution |
| `HogQLFilters` | Filter object for scoping a HogQL query |
| `HogQLVariable` | A named variable substituted into a HogQL query |
| `EventsQuery` | An events query with column selection and filtering |
| `EventsNode` | An event source node within a query (trends, funnels, etc.) |
| `ActorsQuery` | An actors (persons) query |
| `TrendsQuery` | A trends insight query (line/bar chart over time) |
| `FunnelsQuery` | A funnels insight query (conversion funnel) |
| `RetentionQuery` | A retention insight query (cohort retention over time) |
| `StickinessQuery` | A stickiness insight query (days-per-interval return rate) |
| `LifecycleQuery` | A lifecycle insight query (new, returning, resurrecting, dormant) |
| `DateRange` | Date range specification for queries |
| `QueryTiming` | Measured timing for a phase of query execution |

### Query Filters
| Type | Description |
|------|-------------|
| `TrendsFilter` | Display and aggregation options for trends queries |
| `FunnelsFilter` | Configuration for funnel queries including order and window |
| `FunnelExclusion` | An event excluded from funnel conversion counting |
| `RetentionFilter` | Configuration for retention queries |
| `StickinessFilter` | Display options for stickiness queries |
| `LifecycleFilter` | Toggle options for lifecycle queries |
| `PathFilter` | Filters for path (user flow) analytics |
| `FunnelStep` | A funnel step definition |

### Error Tracking
| Type | Description |
|------|-------------|
| `ErrorTrackingIssue` | An error tracking issue aggregating multiple occurrences |
| `ErrorTrackingEvent` | A single error occurrence linked to an issue |

### Surveys
| Type | Description |
|------|-------------|
| `Survey` | A user survey with one or more questions |
| `SurveyQuestion` | A single question within a survey |
| `SurveyConditions` | Targeting conditions for survey display |

### Platform
| Type | Description |
|------|-------------|
| `Team` | A PostHog project |
| `Organization` | An organization containing multiple PostHog projects |
| `User` | A PostHog platform user |
| `Plugin` | A PostHog plugin (data pipeline app or connector) |
| `PluginConfig` | An installed and configured plugin instance |
| `WebhookConfig` | Webhook configuration for sending action events to external services |
| `Notebook` | A PostHog Notebook (document-style collaborative analytics page) |
| `Comment` | A comment on a notebook, insight, dashboard, or recording |

### Data Warehouse
| Type | Description |
|------|-------------|
| `DataWarehouseTable` | A data warehouse table linked to an external source |
| `DataWarehouseCredential` | Credentials for accessing a data warehouse table |
| `ExternalDataSource` | An external data source synced into the PostHog data warehouse |
| `ExternalDataSchema` | A schema within an external data source |

### Retention Results
| Type | Description |
|------|-------------|
| `RetentionResult` | A retention result row (cohort date + data points) |
| `RetentionDataPoint` | A single data point within a retention result |

## Enums (19)

| Enum | Values (sample) |
|------|-----------------|
| `NodeKind` | EventsNode, HogQLQuery, TrendsQuery, FunnelsQuery, RetentionQuery, … |
| `IntervalType` | minute, hour, day, week, month |
| `BreakdownType` | cohort, person, event, group, session, hogql, data_warehouse |
| `FilterLogicalOperator` | AND, OR |
| `PropertyOperator` | Exact, IsNot, IContains, Regex, GreaterThan, IsSet, In, … |
| `FunnelVizType` | steps, time_to_convert, trends |
| `FunnelOrderType` | ordered, unordered, strict |
| `RetentionType` | retention_recurring, retention_first_time |
| `PathType` | pageview, autocapture, screen, custom_event, hogql |
| `ChartDisplayType` | ActionsLineGraph, ActionsBar, ActionsPie, BoldNumber, WorldMap, … |
| `HogLanguage` | hog, hogJson, hogQL, hogQLExpr, hogTemplate, liquid |
| `LifecycleToggle` | new, resurrecting, returning, dormant |
| `ExperimentSignificanceCode` | significant, not_enough_exposure, low_win_probability |
| `ExperimentMetricType` | primary, secondary, guardrail |
| `ExperimentStatsMethod` | bayesian, frequentist |
| `AlertState` | firing, not_firing, errored, snoozed |
| `AlertConditionType` | absolute_value, relative_increase, relative_decrease |
| `AlertCalculationInterval` | hourly, daily, weekly, monthly |
| `ErrorTrackingIssueStatus` | active, archived, resolved, pending_release, suppressed |

## Scalars

| Scalar | Usage |
|--------|-------|
| `DateTime` | ISO 8601 timestamps |
| `JSON` | Arbitrary JSON payloads (properties, filters, configs) |
| `UUID` | UUID v4 identifiers |

## Notes on API Access

PostHog's actual API is REST-based. Key authentication patterns:

- **Personal API Key**: Bearer token in `Authorization: Bearer phx_...` header
- **Project API Key**: Used for event capture (`POST /capture/`)
- **Rate limit**: 240 requests/minute/user on PostHog Cloud

Key REST endpoints corresponding to these types:

| Resource | Endpoint |
|----------|----------|
| Events | `GET /api/projects/:id/events/` |
| Persons | `GET /api/projects/:id/persons/` |
| Cohorts | `GET /api/projects/:id/cohorts/` |
| Feature Flags | `GET /api/projects/:id/feature_flags/` |
| Experiments | `GET /api/projects/:id/experiments/` |
| Insights | `GET /api/projects/:id/insights/` |
| Dashboards | `GET /api/projects/:id/dashboards/` |
| Surveys | `GET /api/projects/:id/surveys/` |
| Session Recordings | `GET /api/projects/:id/session_recordings/` |
| HogQL Query | `POST /api/projects/:id/query/` |
| Event Capture | `POST /capture/` |

## References

- API Docs: https://posthog.com/docs/api
- Query API: https://posthog.com/docs/api/query
- GitHub: https://github.com/PostHog/posthog
- Schema source: `frontend/src/queries/schema/schema-general.ts`
