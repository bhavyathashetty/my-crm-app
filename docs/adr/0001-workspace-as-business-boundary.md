# Keep Workspace as the Business Boundary

The CRM will model a Workspace as the boundary for users and CRM records even though the initial deployment targets one business. This preserves a clear ownership and visibility boundary while avoiding the operational complexity of launching a multi-tenant SaaS product before the core CRM workflow is proven.

## Considered Options

- Remove the business boundary for a single-business application.
- Launch as a fully operational multi-tenant SaaS product.

## Consequences

The first release must associate CRM records and users with a workspace. Public self-signup, cross-workspace behavior, and advanced tenant administration remain out of scope.
