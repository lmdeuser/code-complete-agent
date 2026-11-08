# Chapter 13: Unusual Data Types

[← Previous: Chapter 12](12-Chapter-12-Fundamental-Data-Types.md) | [Table of Contents](00-Table-of-Contents.md) | [Next: Chapter 14 →](14-Chapter-14-Organizing-Straight-Line-Code.md)

---

> **Unofficial adaptation:** Project instructions and domain requirements take precedence. Rules below are guidance; numeric values are review heuristics unless the project explicitly adopts them as standards.

## 1. Executive Summary & Core Principle

Unusual data types—structures/records, pointers/references, and global data—provide advanced capabilities but introduce severe architectural hazards if misused. Pointers and manual memory management are the historical origin of the majority of software vulnerabilities (buffer overflows, use-after-free, dangling references). Global data introduces hidden dependencies and makes concurrency and isolated testing harder.

For an AI Agent, the operational directives are clear: **Leverage modern type-safe records/dataclasses, encapsulate pointers behind RAII/smart pointer abstractions, and minimize unnecessary mutable global state**.

---

## 2. Theoretical Foundations & Key Concepts

### 1. Structures and Records
- Use records/structures to bundle logically related data together into cohesive data packets.
- Benefits:
  - Clarifies routine interfaces: pass one well-defined record instead of 8 separate parameters.
  - Simplifies maintenance: adding a field to a record does not break routine signatures.
  - Enforces relational integrity among dependent values.

### 2. Pointers and References
Pointer management rules:
- **Minimize pointer operations**: Encapsulate pointer mechanics inside specialized data structures.
- **Check for NULL / None**: Always validate pointers before dereferencing.
- **Allocate and deallocate at the same scoping level**: The routine or class that allocates a resource should be responsible for releasing it (RAII pattern).
- **Set freed pointers to NULL**: Prevent dangling pointer bugs by zeroing pointer variables immediately after freeing memory.
- **Smart Pointers**: In C++, use `std::unique_ptr` by default; use `std::shared_ptr` only when shared ownership is strictly required. Never use raw owning pointers.

### 3. Global Data (The Perils of Shared State)
Why global data is harmful:
- **Hidden Coupling**: Routines communicate through invisible back channels instead of explicit parameters.
- **Concurrency Hazards**: Unsynchronized global variables create race conditions and data corruption in multithreaded environments.
- **Testing Impossibility**: Tests cannot be isolated or run in parallel because global state leaks across test boundaries.
- **Loss of Mental Control**: Changing a global variable in one file can cause mysterious crashes in distant, unrelated subsystems.

### Legitimate Uses and Encapsulation of Global State
If global data is unavoidable (e.g. system-wide configuration or application logging):
- Make it **Read-Only / Constant**.
- Encapsulate it behind **Access Routines**: never allow direct access to global fields; provide accessor functions (`GetAppConfig()`) to control mutations and synchronize threads.

---

## 3. Prescriptive Rules for AI Agents

1. **RULE 13.1 — Minimize Mutable Global State**: Prefer immutable constants and instance-owned or explicitly injected state. Process-wide mutable services need explicit ownership, synchronization, and test boundaries.
2. **RULE 13.2 — Explicit Resource Ownership**: Use the language’s ownership and RAII idioms for pointers, sockets, and file handles. In modern C++, prefer smart pointers and containers over manual owning `new`/`delete`.
3. **RULE 13.3 — Null-Pointer Verification**: Always check references for null/None at public boundaries, or leverage language-level non-nullable types (`Option<T>`, `Optional<T>`).
4. **RULE 13.4 — Group Related Parameters into Structures**: When parameters form a coherent concept and travel together, consider a dedicated dataclass, record, or struct if that improves the interface.
5. **RULE 13.5 — Access Routines for Shared State**: If shared configuration is necessary, access it strictly through functions or interfaces that permit mocking in tests.

---

## 4. Operational Checklist Derived from McConnell: Unusual Data Types

- [ ] Are related data items grouped into structured records rather than scattered primitives?
- [ ] Are pointers and dynamic resources encapsulated within RAII objects?
- [ ] Are pointers verified against null prior to dereferencing?
- [ ] Are dynamic resources allocated and freed at the same level of abstraction?
- [ ] Is mutable global data completely eliminated or hidden behind access routines?
- [ ] Are shared resources thread-safe and protected against race conditions?

---

## 5. Heuristics & Metrics

- **Global Variable Count**: Minimize unnecessary mutable global state; legitimate process-wide services require explicit ownership, synchronization, and test boundaries rather than a simplistic count.
- **Pointer Lifespan**: Transfer ownership to a managing object immediately at allocation wherever the language supports it.

---

## 6. Code Patterns & Anti-patterns

### Anti-pattern: Raw Pointers and Global State Pollution
```cpp
// BAD: Naked global mutable state, raw pointer without RAII
int g_userCount = 0; // Unsafe global variable!
char* g_buffer = nullptr;

void initBuffer(int size) {
    g_buffer = new char[size]; // Raw allocation without ownership or cleanup
    g_userCount++;
}
```

### Pattern: Modern RAII, Dataclasses, and Dependency Injection
```cpp
// GOOD: Safe RAII smart pointers, structured records, no global variables
#include <memory>
#include <string>
#include <vector>
#include <stdexcept>

struct UserRecord {
    std::string id;
    std::string email;
    int permissionLevel;
};

class UserSessionPool {
public:
    explicit UserSessionPool(size_t capacity)
        : buffer_(std::make_unique<std::vector<uint8_t>>(capacity)) {}

    void registerUser(const UserRecord& user) {
        if (user.id.empty()) {
            throw std::invalid_argument("User ID cannot be empty");
        }
        users_.push_back(user);
    }

    size_t activeCount() const noexcept {
        return users_.size();
    }

private:
    std::unique_ptr<std::vector<uint8_t>> buffer_;
    std::vector<UserRecord> users_;
};
```

---

## 7. Inspection & Verification Protocol

1. Review global mutable variables for hidden coupling and lifecycle issues; refactor only where encapsulation improves the current design.
2. Verify that all dynamic resource allocations use safe wrappers (`std::unique_ptr`, `with` blocks, `using` statements).
3. Confirm that related data elements are unified into explicit records.
