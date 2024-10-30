# API Gateway

- Create and manage APIs.
- `Endpoint`/entry-point for applications.
- Sits between `applications` & `integrations` (services).
- Highly available, scalable, handles `authorization`, throttling, caching, CORS, transformations, `OpenAPI` spec, direct integration and much more.
- Can connect to services/endpoints in `AWS`on `on-premises`.
- `HTTP` APIs, `REST` APIs and `WebSocket` APIs.

***throttling*** how often individuals can use APIs.

## Overview

- Mobile, Other APIs, Web Applications served as `JS - S3 Static Hosting` can connect to `API Endpoint DNS`.
- API Gateway
  - Can connect to `DDB`, `SNS`, `Step Functions`, `HTTP Endpoints` and `Lambda`. (Integrations).
  - `API Gateway` is an intermediary between `Clients` and `Integrations`.
- There are three phases:
  - Request
    - Authorize --> Validate --> Transform --> Integrations.
  - Integrations
  - Response
    - Integrations --> Transform --> Prepare --> Return.
- Integrates with `CloudWatch`
  - `CloudWatch Logs` can store and manage full stage request and response logs.
  - `CloudWatch` can store `metrics` for client and integration sides.
- `API Cache`
  - `API Gateway Cache` can be used to `reduce` the number of calls made to `backend` integrations and `improve client performance`.

## Authentications

- `Client` authenticates with Cognito and receive a token.
- `Client` passes token with Request to `API Gateway`.
- `API Gateway` verify token validity with `Cognito`.
- `Client` calls `API Gateway` with a bearer token (ID).
  - `API Gateway` doesn't know how to handle this, so calls a `Lambda Authorizer`.
  - `Lambda Authorizer` calls `External ID Provider` or `Compute Based Check of ID`.
  - If everything is OK, they return an `IAM Policy and Principal Identifier` to `API Gateway`.
  - `API Gateway` validates the information handles the request via `Lambda` integration or `returns` 403 ACCESS_DENIED to client.

## Endpoint Types

- Edge-Optimized.
- Routed to the nearest CloudFront POP (Point of presence).
- `Regional` - Clients in the same region.
- `Private` - Endpoint accessible only within a VPC via interface endpoint.

## Stages

- APIs are `deployed` to `stages`, each stage has `one deployment`. (dev, prod, etc).
- `Customers` --> api.godeveloper.io/prod <-- `v1` --> `Lambdav1`.
- `Developers` --> api.godeveloper.io/dev <-- `v2` --> `Lambdav2`.
- Stages can be `enabled` for `canary deployments`. If done, `deployments` are made to the `canary` not the stage. To a subpart of that stage.
- Stages enabled for canary deployments can be configured `so a certain percentage of traffic is sent to the canary`. This can be adjusted over time - or the canary can be promoted to make it the `new base stage`.
  - `v2` -...-> api.godeveloper.io/prod.
- Stages are logical configurations which exist within API Gateway.
- APIs are deployed into stages.
- Can be used for different versions or lifecycle points for an API.
- Each stage has its own configuration.

## Errors

- `4XX` - `Client Error` - Invalid request on `client` side.
- `5XX` - `Server Error` - Valid request, `backend` issue.
- `400` - Bad Request - Generic client side error.
- `403` - Access Denied - Authorizer denies... WAF Filtered.
- `429` - API Gateway can throttle - this means you've exceeded that amount. (throttling is occurring).
- `502` - Bad Gateway Exception - bad output returned by Lambda.
- `503` - Service Unavailable - backing endpoint offline? Major service issue.
- `504` - Integration Failure/Timeout - 29s limit.

## Caching

- `Caching` is configured per `stage`.
- Cache `TTL` default is `300` seconds.
- Configurable min `0` and max `3600`s.
- Can be encrypted.
- Cache size `500MB` to `237GB`.
- Calls are only made to backend integrations if request is a `cache miss`.
- Reduced `load` & `cost`. `Improved performance`.

## Methods and Resources

- `URL` - https://7np8t33z77.execute-api.us-east-1.amazonaws.com/dev/listproducts
- Invoke URL points at the:
  - `API Gateway Endpoint` - https://7np8t33z77.execute-api.us-east-1.amazonaws.com
  - `stage` - dev
  - `resource` - listproducts
    - `/` - is the root resource
    - `/listproducts` - is other resource
    - Think of resources as `points in the API tree` or `bits of functionality` within your API.
  - `methods` - get
    - In a resource you create methods like `get`.
    - Methods are the `desired action` to be performed. (`HTTP Verbs`).
    - Methods are where `integrations` are configured which provide the functionality of an API e.g. `Lambda`, `HTTP`, & `AWS Service`.
      - resource `listproducts` --> calls `get` method --> Invokes the `Lambda` integration `api-listproducts`.

## Integrations

- Dynamo DB, SNS, Step Functions, HTTP Endpoints, Lambda.
- API Methods (client) are integrated with a backend endpoint.
- Different types of integrations are available...
- `MOCK` - Used for testing, no backend involvement.
- `HTTP` - Backend HTTP Endpoint.
- `HTTP Proxy` - Pass through to integration unmodified, return to the client unmodified (backend need to use supported format).
- `AWS` - Lets an API expose AWS service actions.
- `AWS_PROXY` (Lambda) - Low admin overhead Lambda endpoint.

### Components of the Integration and Methods

- `Method Request` --> `Integration Request` --> Integration
- `Method Response` <-- `Integration Response` <-- Integration

### Mapping Templates

- Used for `AWS` and `HTTP` (non PROXY) integrations.
- `Modify` or `Rename Parameters`.
- Modify the `body` or `headers` of the request.
- `Filtering` - removing anything which isn't needed.
- Uses Velocity Template Language (VTL).
- Common exam scenario is REST API (on API Gateway) to a SOAP API .. transform the request using mapping template.

## Stages & Deployments

- Changes made in `API Gateway` are `not LIVE`.
- The current `API State` needs to be `deployed` to a `stage`.
- Stages can be environments (`PROD`, `DEV`, `TEST`).
- .. or versions (`v1`, `v2`, `v3`) - for `breaking` changes.
- Each stage has its `own configuration`.
- .. not immutable, can be overwritten and rolled back.

### How Stages Work

- `Production` Users, production app, accessing `production API`. --> https://api.godeveloper.io/v1/timeline --> `v1` Stage --> `v1` Compatible Lambda.
  - Original `Production` API backed by current `main code branch`.
- API `Configuration` Deployments to Stages --> `v1` Stage.
- API `Configuration` Deployments to Stages --> `v2` Stage.
  - `BETA` Users, BETA app, accessing BETA API. --> https://api.godeveloper.io/v12/timeline --> `v2` Stage --> `v2` Compatible Lambda
  - Development API Backed by `development code branch` (`breaking changes`).

### Stages Variables

- `ENV` = PROD || BETA || DEV defined on each stage (STAGE VARIABLES).
- Integration points at function `Alias` using the `ENV` Stage `variable`.
- Configuration changes happen `via changes to aliases`.
- timeline-lambda `$LATEST`.
- `Mutable` .. last `saved/deployed` state of code.
- Optional A/B testing via `weighted Alias`. Pointing a portion of the traffic to another `timeline-lambda` `Version = 2`.

## Swagger & OpenAPI

The OpenAPI Specification (`OAS`) defines a `standard`, `language-agnostic` interface to RESTful APIs which allows both humans and computers to discover and understand the capabilities of the service without access to source code, documentation, or through network traffic inspection.

- OpenAPI (`OAS`) formally known as `swagger`.
- `Swagger` = OpenAPI v2.
- `OpenAPI v3` is a more recent version.
- `API Description format` for REST API's.
- Endpoints (`/listproducts`) and Operations (`GET /listproducts`).
- `Input` and `Output` Parameters & `Authentication` Methods.
- Non-technical information - Contact Info, License, Term of use...
- `API Gateway` can `export` to Swagger/OpenAPI & `import` from them.