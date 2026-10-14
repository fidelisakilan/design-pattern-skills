# Pattern catalog

Source: https://refactoring.guru/design-patterns

## Classification

**By scale** (refactoring.guru/design-patterns/classification):

- **Idiom**: lowest level, tied to one language (Python context managers, Go `errgroup`, Ruby blocks). Prefer the idiom over a pattern when the language already has one.
- **Design pattern**: language-agnostic, class-and-object level. The 22 below.
- **Architectural pattern**: whole-system shape (MVC, layered, event-driven, microservices). Out of scope here.

**By intent**: creational (how objects are made), structural (how objects compose), behavioral (how objects talk and divide responsibility).

## Creational

| Pattern | Intent | Reach for when | Skip when |
|---|---|---|---|
| Factory Method | Superclass defines creation interface, subclasses pick the concrete type | A class must create objects whose exact type it cannot know ahead | A plain function or constructor suffices |
| Abstract Factory | Produce families of related objects without naming concrete classes | Products must be used together and vary as a set (UI toolkit per OS) | Only one family exists |
| Builder | Construct complex objects step by step | Many optional parts, telescoping constructors, same steps yield different representations | Keyword args or a dataclass cover it |
| Prototype | Copy existing objects without coupling to their class | Cloning is cheaper or safer than constructing, or class is unknown at runtime | Language has `copy`/`clone` built in |
| Singleton | One instance, global access point | A shared resource genuinely must be unique (connection pool, config) | A module-level value or DI container does it; most uses are hidden globals |

## Structural

| Pattern | Intent | Reach for when | Skip when |
|---|---|---|---|
| Adapter | Let incompatible interfaces work together | Wrapping a third-party or legacy interface you cannot change | You own both sides, just fix the interface |
| Bridge | Split abstraction from implementation so both vary independently | Two dimensions of variation would otherwise multiply subclasses (Shape x Renderer) | Only one dimension varies |
| Composite | Tree of objects treated uniformly as one object | Part-whole hierarchies where clients ignore leaf vs container (file systems, UI trees) | Structure is flat |
| Decorator | Attach behavior by wrapping, stackable | Add responsibilities at runtime without subclass explosion (streams, middleware) | One fixed addition, subclass or a flag is shorter |
| Facade | Simple interface over a complex subsystem | Clients need a small surface of a large library | Subsystem is already small |
| Flyweight | Share common state across many objects | Memory is measured to be the problem and objects share intrinsic state | You have not measured |
| Proxy | Placeholder that controls access to the real object | Lazy init, access control, caching, logging, remote access | A direct call plus a decorator is clearer |

## Behavioral

| Pattern | Intent | Reach for when | Skip when |
|---|---|---|---|
| Chain of Responsibility | Pass a request along handlers until one takes it | Multiple handlers, order matters, set may change at runtime (middleware, event bubbling) | Handlers are fixed and few, an `if` chain is shorter |
| Command | Turn a request into an object | Need undo, queueing, logging, or scheduling of operations | A plain callback does it |
| Iterator | Traverse a collection without exposing its structure | Custom traversal order or lazy production | Language iterators/generators already cover it |
| Mediator | Objects talk through a hub, not each other | Many-to-many coupling among components (dialog widgets, chat rooms) | Two or three objects, let them talk directly |
| Memento | Snapshot and restore state without breaking encapsulation | Undo/rollback that must not expose internals | State is a plain value, just copy it |
| Observer | Subscribe to events from a subject | One-to-many change notification where subscribers vary at runtime | Language or framework has an event system, use it |
| State | Object changes behavior when its internal state changes | A state machine with per-state behavior, `switch(state)` spreading across methods | Two states, a boolean is fine |
| Strategy | Family of interchangeable algorithms behind one interface | Algorithm chosen at runtime, or you want to test variants in isolation | One algorithm, or a function argument does it |
| Template Method | Skeleton in a superclass, steps overridden in subclasses | Several classes share an algorithm shape and differ in a few steps | Composition (Strategy) avoids inheritance |
| Visitor | Add operations to an object structure without changing its classes | Many operations over a stable set of node types (AST passes) | Node types change often, or one operation only |

## Depth

Each pattern has a page at `https://refactoring.guru/design-patterns/<name>` (kebab-case, e.g. `factory-method`, `chain-of-responsibility`) with structure, pseudocode, pros and cons, and relations to other patterns. Fetch only the one you chose.
