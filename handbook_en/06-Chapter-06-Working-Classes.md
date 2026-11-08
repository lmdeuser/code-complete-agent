# Chapter 6: Working Classes

[← Previous: Chapter 5](05-Chapter-05-Design-in-Construction.md) | [Table of Contents](00-Table-of-Contents.md) | [Next: Chapter 7 →](07-Chapter-07-High-Quality-Routines.md)

---

> **Unofficial adaptation:** Project instructions and domain requirements take precedence. Rules below are guidance; numeric values are review heuristics unless the project explicitly adopts them as standards.

## 1. Executive Summary & Core Principle

A class is a collection of data and routines that share a cohesive, well-defined responsibility. The foundation of modern object-oriented and modular programming is the **Abstract Data Type (ADT)**. Creating a class is not merely grouping functions together; it is erecting a conceptual wall that provides a high-level, consistent abstraction while strictly encapsulating internal machinery.

For an AI Agent, creating classes requires rigorous adherence to **Single Responsibility, Encapsulation Barricades, and Containment over Inheritance**. Inheritance is often abused; favor composition ("has a") over inheritance ("is a").

---

## 2. Theoretical Foundations & Key Concepts

### Abstract Data Types (ADTs)
An ADT is a set of data values and operations on those values. Without ADTs, code directly manipulates raw primitives (e.g. integers, strings, arrays) with zero domain safety.
- *Example*: Instead of passing a raw `int` for font size, create a `FontSize` ADT with validation, boundary checks, and conversion methods.

### Good Class Interfaces: Abstraction and Encapsulation
1. **Consistent Abstraction**: All methods in a class interface must operate at the same conceptual level.
   - *Smell*: A `User` class having methods `getName()`, `changePassword()`, and `writeRawBytesToDiskSocket()`. The last method operates at a physical I/O layer, breaking abstraction.
2. **Complete & Minimal**: Provide all necessary operations for the domain without gratuitously bloating the interface.
3. **Encapsulation Barricades**:
   - Never expose public fields/attributes.
   - Do not return mutable internal references without cloning/defensive copying.
   - Do not make assumptions about class callers in internal routines.

### Containment ("Has a") vs. Inheritance ("Is a")
- **Containment / Composition**: Preferred by default. A `Car` *has an* `Engine`.
- **Inheritance**: Legitimate only when true substitutability holds: the subclass conforms strictly to the **Liskov Substitution Principle (LSP)**. A `Square` should *not* inherit from `Rectangle` if changing width independently breaks invariants.
- **Deep Inheritance Warning**: Avoid inheritance hierarchies deeper than 2–3 levels. Deep hierarchies exponentially increase mental complexity and tightly couple distant code.

---

## 3. Prescriptive Rules for AI Agents

1. **RULE 6.1 — Single Responsibility Principle (SRP)**: Each class should have a clear, cohesive purpose. The word "and" in a description is a review prompt, not proof that a class must be split.
2. **RULE 6.2 — Encapsulate Mutable State**: Keep internal mutable state behind the language and project’s encapsulation mechanisms. Expose intentional read-only views or query/command methods rather than unrestricted mutation.
3. **RULE 6.3 — Favor Composition over Inheritance**: Implement code reuse through member composition and interface delegation rather than subclassing, unless true subtyping (LSP) applies.
4. **RULE 6.4 — Enforce Class Invariants in Constructors**: Constructors must initialize all member variables into a valid state. Do not leave instances in half-initialized or invalid states.
5. **RULE 6.5 — Minimize Method Count**: Avoid "god classes" with 50+ methods. Keep class interfaces lean and focused (typically <= 15-20 public methods).

---

## 4. Operational Checklist Derived from McConnell: Class Quality

- [ ] Does the class have a central, well-focused purpose (high cohesion)?
- [ ] Does the class represent a true Abstract Data Type (ADT)?
- [ ] Does the interface present a consistent level of abstraction?
- [ ] Is internal state strictly encapsulated (no public mutable fields)?
- [ ] Does the class favor containment over inheritance?
- [ ] If inheritance is used, is it strictly an "is-a" relationship conforming to LSP?
- [ ] Is the inheritance tree shallow (<= 2-3 levels)?
- [ ] Are all member variables initialized in every constructor?

---

## 5. Heuristics & Metrics

- **LCOM (Lack of Cohesion in Methods)**: Groups of methods manipulating disjoint state can indicate low cohesion and justify review; the metric alone does not prove that a split is beneficial.
- **Coupling Factor**: Review classes with many direct dependencies for unclear responsibilities, but do not enforce a universal dependency count.

---

## 6. Code Patterns & Anti-patterns

### Anti-pattern: Leaky Encapsulation and Broken Abstraction
```typescript
// BAD: Public mutable array, inconsistent abstraction levels, leaky internals
export class EmployeeManager {
  public employees: any[] = []; // Direct exposure of internal collection!

  public addEmployee(name: string, salary: number): void {
    this.employees.push({ name, salary });
  }

  // Broken abstraction: low-level socket protocol mixed into employee management!
  public sendRawTcpPacket(ip: string, port: number): void {
    // ...
  }
}
```

### Pattern: Disciplined ADT with Strict Encapsulation
```typescript
// GOOD: Immutable types, private state, domain-specific methods, consistent abstraction
export interface Employee {
  readonly id: string;
  readonly name: string;
  readonly salaryCents: number;
}

export class EmployeeRoster {
  private readonly _records = new Map<string, Employee>();

  public register(employee: Employee): void {
    if (!employee.id || employee.salaryCents < 0) {
      throw new RangeError(`Invalid employee data for ID: ${employee.id}`);
    }
    if (this._records.has(employee.id)) {
      throw new Error(`Employee with ID ${employee.id} already exists`);
    }
    this._records.set(employee.id, employee);
  }

  public getById(id: string): Employee | undefined {
    return this._records.get(id);
  }

  public get all(): readonly Employee[] {
    return Object.freeze(Array.from(this._records.values()));
  }
}
```

---

## 7. Inspection & Verification Protocol

1. Verify that all fields are marked private/protected and initialized in constructors.
2. Check for "god classes" and split any class handling multiple disparate concerns.
3. Verify that inheritance is only used for true polymorphic substitution.
