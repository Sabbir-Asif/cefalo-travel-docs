#### 1. **Controllers**

**Yes.**
Test that the controller:

* Returns the correct HTTP status codes
* Passes correct data to the service
* Handles validation and errors properly

You can use **Supertest + Jest** to test controller routes with Express.

---

#### 2. **Services**

**Yes.**
This is where business logic lives. Service unit tests should:

* Verify core logic under various conditions
* Mock the repository layer to isolate tests

Use **Jest** for this. These are classic unit tests.

---

#### 3. **Repositories**

**Optional, but recommended.**
Test only if your repository has logic beyond raw queries, such as:

* Custom filters
* Pagination
* Query building based on conditions

If your repo is a thin wrapper over Knex with no logic, test it indirectly via service/integration tests.

---

#### 4. **DTOs (Data Transfer Objects)**

**No need to test directly.**
They are just type definitions or shape descriptors. Instead:

* Validate DTO usage via controller/service tests
* If DTOs are coupled with Zod, test the Zod schema validation

---

#### 5. **Migrations**

**No.**
Migrations are not meant to be unit tested. Instead:

* Test via **integration tests**: run against a real or test database
* Validate table structure by using it in your real module tests

---

#### 6. **Interfaces (e.g., `IService`, `IRepository`)**

**No.**
Interfaces are purely compile-time constructs in TypeScript. They don’t exist at runtime, so they can’t and don’t need to be tested.

---

### Summary

| Component  | Should You Unit Test It? | Notes                       |
| ---------- | ------------------------ | --------------------------- |
| Controller | Yes                      | Use Supertest + Jest        |
| Service    | Yes                      | Use Jest with mocks         |
| Repository | Optional                 | Only if logic exists        |
| DTOs       | No                       | Test usage, not definitions |
| Migrations | No                       | Use integration testing     |
| Interfaces | No                       | TS compile-time only        |
