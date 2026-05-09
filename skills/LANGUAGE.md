# Language

Shared vocabulary across every skill in this set. Use these terms exactly — don't substitute "component," "service," "API," or "boundary." Consistent language is the whole point.

This file is the canonical source. Skills that need this vocabulary (`design`, `system-design`, `improve-codebase-architecture`, `grill-plan`) link here rather than duplicating definitions.

## Architecture terms

**Module**
Anything with an interface and an implementation. Deliberately scale-agnostic — applies equally to a function, class, package, or tier-spanning slice.
_Avoid_: unit, component, service.

**Interface**
Everything a caller must know to use the module correctly. Includes the type signature, but also invariants, ordering constraints, error modes, required configuration, and performance characteristics.
_Avoid_: API, signature (too narrow — those refer only to the type-level surface).

**Implementation**
What's inside a module — its body of code. Distinct from **Adapter**: a thing can be a small adapter with a large implementation (a Postgres repo) or a large adapter with a small implementation (an in-memory fake). Reach for "adapter" when the seam is the topic; "implementation" otherwise.

**Depth**
Leverage at the interface — the amount of behaviour a caller (or test) can exercise per unit of interface they have to learn. A module is **deep** when a large amount of behaviour sits behind a small interface. A module is **shallow** when the interface is nearly as complex as the implementation.

**Seam** _(from Michael Feathers)_
A place where you can alter behaviour without editing in that place. The *location* at which a module's interface lives. Choosing where to put the seam is its own design decision, distinct from what goes behind it.
_Avoid_: boundary (overloaded with DDD's bounded context).

**Adapter**
A concrete thing that satisfies an interface at a seam. Describes *role* (what slot it fills), not substance (what's inside).

**Leverage**
What callers get from depth. More capability per unit of interface they have to learn. One implementation pays back across N call sites and M tests.

**Locality**
What maintainers get from depth. Change, bugs, knowledge, and verification concentrate at one place rather than spreading across callers. Fix once, fixed everywhere.

## Topology terms (greenfield system shape)

**Responsibility**
What a module does, stated in one sentence. If you can't say it in one sentence, the module is too big — split or merge.

**Dependency direction**
Module A depends on B if A imports, calls, or relies on B's interface. Directional. The dependency graph at the system level is the topology.

**Acyclic**
The dependency graph has no cycles. If A → B → A, the modules aren't really separate — merge them, or insert a third module to break the cycle.

**Port** *(from ports & adapters / hexagonal architecture)*
An interface defined by a domain module so that infrastructure can implement it without the domain depending on infrastructure. The port lives in the domain; the adapter lives in the infra. Inverts the naive dependency direction.

## Principles

- **Depth is a property of the interface, not the implementation.** A deep module can be internally composed of small, mockable, swappable parts — they just aren't part of the interface. A module can have **internal seams** (private to its implementation, used by its own tests) as well as the **external seam** at its interface.
- **The deletion test.** Imagine deleting the module. If complexity vanishes, the module wasn't hiding anything (it was a pass-through). If complexity reappears across N callers, the module was earning its keep.
- **The interface is the test surface.** Callers and tests cross the same seam. If you want to test *past* the interface, the module is probably the wrong shape.
- **One adapter means a hypothetical seam. Two adapters means a real one.** Don't introduce a seam unless something actually varies across it.
- **Domain doesn't depend on infrastructure.** Use ports — domain defines the interface, infrastructure implements it. Keeps domain logic runnable in tests with no infra.

## Relationships

- A **Module** has exactly one **Interface** (the surface it presents to callers and tests).
- **Depth** is a property of a **Module**, measured against its **Interface**.
- A **Seam** is where a **Module**'s **Interface** lives.
- An **Adapter** sits at a **Seam** and satisfies the **Interface**.
- **Depth** produces **Leverage** for callers and **Locality** for maintainers.
- A **Port** is an **Interface** defined by a domain **Module** for an infra **Adapter** to implement.

## Rejected framings

- **Depth as ratio of implementation-lines to interface-lines** (Ousterhout's original): rewards padding the implementation. We use depth-as-leverage instead.
- **"Interface" as the TypeScript `interface` keyword or a class's public methods**: too narrow — interface here includes every fact a caller must know.
- **"Boundary"**: overloaded with DDD's bounded context. Say **seam** or **interface**.
- **Layer-named modules** (`controllers`, `services`, `repositories`): a category split, not a responsibility split. Prefer feature slicing (`ordering/`, `billing/`, `auth/`).
