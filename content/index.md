---
title: Portfolio · HouseMate
type: portfolio
goal: Creating professional IT products
cssclasses:
  - portfolio
---
# Portfolio · HouseMate

> [!abstract] Goal description
> You create several professional IT products. To do so, you **analyse** the problem context, **advise** on choices to be made, **design** a suitable solution, **realise** it and **validate** its operation.

> [!abstract] At a glance
> **Project:** HouseMate, an app to share a house: members, shared expenses, and tenant and landlord roles.
> **My part:** the Android app (Java) and the Spring Boot REST API.
> **Current sprint:** [[Sprint 3]] (16–22 November).

## Introduction
HouseMate lets tenants join a house with a code, and landlords create houses. This document follows the five steps of the goal. For each step it shows what I did, the notes behind it, and the quality criteria I checked.

---

## 1. Analyse
*Research the assignment, understand the preconditions, and record the requirements and quality criteria in a standard way.*

### 1.1 Requirements
| # | Requirement | Source |
| --- | --- | --- |
| R1 | Every user has a role, tenant (`User`) or landlord (`Landlord`), saved in the database | [[Role type]] |
| R2 | The app receives the role at login and opens the right home screen | [[Rol saved from Login]] |
| R3 | Only landlords create houses, and the landlord becomes the owner | [[Landlord rol]] |
| R4 | Only tenants join a house with a code | [[User rol]] |
| R5 | A user cannot see the views of the other role | [[Role-based screens]] |

### 1.2 Quality criteria
| Criterion | How I check it |
| --- | --- |
| The data of existing users stays valid | Check the rows before adding a required field ([[Null enum in DB]]) |
| The wrong role is refused | The back end returns 403 for the wrong role |
| Each screen gets the data it needs | Trace the Intent from the login to the next screen ([[Fail with Intents through activites]]) |
| The work fits the sprint | Blocks with a "done when" sentence ([[Sprint 3]]) |

### 1.3 Scope
The sprint had seven blocks in one week. The roles came first, because the views depend on them. The IoT block depends on data from another team member ([[Group_sprint 3]]), so I treated it as a dependency, not as a step I control.

---

## 2. Advise
*Explain why the products and the choices contribute to solving the problem.*

### 2.1 Choices and why
| Choice | Why it solves the problem | Note |
| --- | --- | --- |
| A Java enum for the role | Named values, and the compiler rejects invalid roles | [[Role type]] |
| The role saved at login | The role is needed after the app is closed | [[Rol saved from Login]] |
| One host activity with fragments | Login and profile are not copied, and the role logic stays in one place | [[Role-based screens]] |
| SharedPreferences for the session | Keeps the session between launches | [[SharedPreferences session]] |
| BCrypt for passwords | A leaked database does not reveal the passwords | [[BCrypt for passwords]] |
| Retrofit for the API calls | One Java interface per API area | [[Retrofit for API calls]] |
| One shared PostgreSQL database | One set of data for the whole group | [[PostgreSQL as shared database]] |
| Java for the app and the back end | One language for the team | [[Java instead of Kotlin]] |

### 2.2 Advice for the group
1. **Check the data before adding a required field.** Existing rows will not have the new value.
2. **Decide the Must and Should blocks on the first day of the sprint**, and give each dependency an owner and a date.
3. **Keep passwords out of the code.** The database password should come from an environment variable.

---

## 3. Design
*Devise a solution based on the requirements and quality criteria, and record the choices.*

### 3.1 Role model
- `RoleType { User, Landlord }` is stored as text in `users.role` ([[Role type]]).
- The role is returned by the login endpoint, and saved on the phone ([[Rol saved from Login]]).
- The house is linked to its owner, the landlord. A tenant joins it with the join code ([[Endpoints]]).

### 3.2 Screens
- After login, the app opens the home screen for the role ([[Role-based screens]]).
- The House screen shows the join-code form to tenants, and the create-house form to landlords ([[Landlord rol]], [[User rol]]).

### 3.3 Back end rules
- `createHouse`: only `Landlord` can create a house. Otherwise the back end returns 403.
- `joinHouse`: only `User` can join a house with a code. Otherwise the back end returns 403.

---

## 4. Realise
*Build the solution from the design, paying attention to the quality criteria.*

### 4.1 What I built
- The `role` column and the `RoleType` enum in the back end ([[Role type]]).
- The role saved at login, and the role-based home screens ([[Role-based screens]]).
- The House screen, with the buttons and forms per role.
- The 403 rule in the back end, and the message in the app.

### 4.2 Problems during the build
- **The role column:** existing users had no role, so the new required column failed. I set a value for old users ([[Null enum in DB]]).
- **The House button:** the screen closed straight away. The login sent the data to SharedPreferences, but not to the Intent that opens the next screen. I added the four values to the Intent ([[Fail with Intents through activites]]).

---

## 5. Validate
*Test the product with standard techniques, record the results, and improve the product where necessary.*

### 5.1 Tests
| Test | Expected | Result |
| --- | --- | --- |
| Login as a tenant opens the tenant home | Tenant home | **Not recorded**: the Sprint 3 note marks the views as done, but there is no test result for it |
| Login as a landlord opens the landlord home | Landlord home | **Not recorded**: same as above |
| The House button opens the House screen | House screen opens | **Pending**: compiled, not yet run on the emulator after the fix |
| A tenant cannot create a house | 403 | Back end rule in place, not yet tested from the app |
| A landlord cannot join a house | 403 | Back end rule in place, not yet tested from the app |

### 5.2 Results and improvements
- The roles and the views are marked as done in [[Sprint 3]], but I have not recorded the test results yet. The bugs are traced and recorded ([[Null enum in DB]], [[Fail with Intents through activites]]).
- The House button fix needs to be confirmed on the emulator before the bug is closed.
- The 403 rules need a test from the app, with a tenant and a landlord account.

---

## Conclusion
The analysis, the design and the build of the role model are done. The validation is not finished: the House button fix and the 403 rules still need to be tested on the emulator. The next sprint starts with those tests, and then logout ([[Sprint 3]]).
