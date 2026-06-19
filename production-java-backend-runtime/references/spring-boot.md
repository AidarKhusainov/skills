# Spring Boot

## Load this when

Load this reference for Spring Boot configuration, beans, controllers, REST clients, Actuator, properties, profiles, startup behavior, dependency injection, transactions, security integration, or framework wiring.

## Non-negotiable checks

- Do not change Spring wiring casually.
- Preserve bean names, qualifiers, profiles, conditional configuration, and configuration properties semantics unless intentionally changed.
- Prefer constructor injection.
- Keep business logic out of controllers, configuration classes, filters, mappers, and persistence adapters.
- Keep framework details out of core domain logic where practical.
- Treat configuration properties as runtime contracts.
- Treat Actuator health and metrics as production-facing behavior.
- Avoid changing startup behavior without checking probes and deployment assumptions.

## Preferred approach

For controllers:

- Validate external input at boundaries.
- Use clear request/response models.
- Preserve HTTP status semantics.
- Preserve error response shape unless intentionally changed.
- Keep controller logic thin.

For services/use cases:

- Keep business behavior explicit.
- Keep transaction boundaries deliberate.
- Avoid remote calls inside transactions unless justified.
- Avoid mixing orchestration, persistence, mapping, and policy in one method.

For configuration:

- Prefer typed `@ConfigurationProperties` over scattered string lookups.
- Validate required properties.
- Preserve profile-specific behavior.
- Avoid environment-dependent defaults that change production behavior silently.

For clients:

- Ensure timeouts exist or are inherited from verified defaults.
- Ensure errors are mapped deliberately.
- Preserve correlation/request IDs when conventions exist.
- Add metrics/tracing for high-value dependencies when appropriate.

## Red flags

- `@Transactional` added without checking propagation, isolation, rollback behavior, or remote calls.
- Broad `@SpringBootTest` where a slice or unit test would be enough.
- Logic inside `@Configuration` or bean factory methods.
- Field injection.
- Silent fallback defaults for required production configuration.
- `@Async`, schedulers, consumers, or thread pools without lifecycle and observability.
- Custom error handling that bypasses existing exception conventions.
- Actuator exposure changes without security review.

## Verification

Prefer the narrowest Spring test that proves the behavior:

- Unit test for pure logic.
- MVC/WebFlux slice test for web behavior.
- Data slice test for repository/query behavior.
- Application context test for wiring/configuration.
- Integration test for real DB/messaging/client wiring.

Run configuration/property binding tests when changing typed config.

## Final response notes

Mention:

- Spring components changed.
- Configuration or profile assumptions.
- Whether application context or relevant slice test was run.
- Any startup/runtime behavior not verified.
