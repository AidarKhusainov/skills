# Spring Boot

## Decision rules

Preserve Spring framework semantics deliberately:

- bean names, qualifiers, profiles, conditions, and configuration-property binding are runtime behavior;
- prefer constructor injection and explicit dependencies;
- keep business policy out of controllers, configuration classes, filters, mappers, and persistence adapters;
- validate external input at system boundaries and preserve established response/error semantics;
- prefer typed, validated `@ConfigurationProperties` for owned configuration instead of scattered string lookups;
- preserve profile/environment behavior; do not introduce silent production defaults for required configuration;
- treat `@Transactional` as a framework mechanism implementing a data consistency boundary, not as a generic service annotation;
- before changing propagation/isolation/rollback behavior, reason from the business invariant and persistence semantics;
- avoid remote calls inside a database transaction unless the consistency/failure trade-off is explicit;
- outbound clients must inherit verified timeout behavior or configure it explicitly; retry/idempotency semantics belong to the resilience reference;
- treat `@Async`, schedulers, listeners, and managed executors as lifecycle/runtime behavior, including shutdown and context propagation.

## Failure modes

Watch for:

- field injection or hidden optional dependencies;
- business logic inside controllers/configuration/bean factory methods;
- broad `@Transactional` scope around remote or long-running work;
- changed Jackson/validation/error-handler behavior that alters an external contract accidentally;
- required configuration replaced by permissive fallback values;
- custom error handling bypassing established exception semantics;
- Actuator exposure or security changes made as incidental wiring;
- `@Async`, scheduler, listener, or executor changes without lifecycle/failure visibility.

## Verification

Choose the narrowest Spring-aware proof that exercises the changed framework semantics: MVC/WebFlux slice for web behavior, data slice or real-database integration for persistence, property/context test for configuration/wiring, or integration test for lifecycle/client behavior. Do not use `@SpringBootTest` when a narrower test proves the same contract.
