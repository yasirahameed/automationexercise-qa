# Automation Exercise QA Project

## Overview

This repository is a hands-on Software Quality Assurance project built using the [Automation Exercise](https://automationexercise.com/) web application.

The purpose of this project is to practice and demonstrate an end-to-end QA workflow, starting with application analysis and manual testing and progressing toward API and UI automation.

The project will cover:

- Manual Testing
- Exploratory Testing
- API Testing with Postman
- Java for Test Automation
- TestNG
- REST Assured
- Selenium WebDriver
- API and UI Integration Testing
- Test Data Management
- Automation Framework Design
- CI/CD Integration
- Quality Metrics
- Generative AI usage in the testing lifecycle

---

## Application Under Test

**Application:** Automation Exercise

**URL:** https://automationexercise.com/

Automation Exercise provides functionality including:

- User Registration
- Login and Logout
- Product Browsing
- Product Details
- Product Search
- Categories
- Brands
- Shopping Cart
- Checkout
- Contact Form
- Subscription
- Product Reviews
- Account Management
- REST APIs

---

## Project Objectives

The main objectives of this project are to:

1. Understand the application before automation.
2. Identify business-critical workflows and risks.
3. Design manual test scenarios and detailed test cases.
4. Perform positive, negative, boundary, and exploratory testing.
5. Document defects professionally.
6. Test REST APIs manually using Postman.
7. Build API automation using Java, TestNG, and REST Assured.
8. Build UI automation using Selenium WebDriver.
9. Design maintainable automation frameworks.
10. Combine API and UI automation for efficient end-to-end testing.
11. Organize tests into smoke and regression suites.
12. Integrate automated tests with CI/CD.
13. Track and communicate quality metrics.
14. Use Generative AI responsibly to improve the testing lifecycle.

---

## Technology Stack

| Area | Technology |
|---|---|
| Manual Testing | Markdown, Test Scenarios, Test Cases |
| API Manual Testing | Postman |
| Programming Language | Java |
| Test Framework | TestNG |
| API Automation | REST Assured |
| UI Automation | Selenium WebDriver |
| Build Tool | Maven |
| Version Control | Git |
| Repository Hosting | GitHub |
| Test Data | JSON, CSV, TestNG DataProvider |
| Reporting | TestNG / Allure or Extent Reports |
| CI/CD | GitHub Actions or Jenkins |
| AI Assistance | Generative AI tools |

---

## Repository Structure

```text
automationexercise-qa/
│
├── README.md
├── .gitignore
│
├── docs/
│   ├── 01-application-analysis/
│   ├── 02-test-strategy/
│   ├── 03-test-scenarios/
│   ├── 04-test-cases/
│   ├── 05-bug-reports/
│   └── 06-traceability/
│
├── postman/
│   ├── collections/
│   └── environments/
│
├── automation/
│
├── test-data/
│
└── reports/
```

The structure will evolve as the project moves from manual testing toward automation.

---

## QA Workflow

The project follows this progression:

```text
Application Analysis
        ↓
System Context
        ↓
Test Strategy
        ↓
Manual Test Scenarios
        ↓
Detailed Test Cases
        ↓
Exploratory Testing
        ↓
Defect Reporting
        ↓
Postman API Testing
        ↓
Java Fundamentals
        ↓
TestNG
        ↓
REST Assured
        ↓
API Automation Framework
        ↓
Selenium WebDriver
        ↓
UI Automation Framework
        ↓
API + UI Integration
        ↓
CI/CD
        ↓
Reporting and Quality Metrics
```

---

## Practice Roadmap

| Practice | Topic | Status |
|---|---|---|
| 01 | Application Analysis | Planned |
| 02 | System Context | Planned |
| 03 | Test Strategy | Planned |
| 04 | Manual Test Scenarios | Planned |
| 05 | Detailed Test Cases | Planned |
| 06 | Exploratory Testing | Planned |
| 07 | Bug Reporting | Planned |
| 08 | Postman Fundamentals | Planned |
| 09 | API Negative Testing | Planned |
| 10 | API Account Lifecycle | Planned |
| 11 | Java Fundamentals | Planned |
| 12 | Java OOP for Automation | Planned |
| 13 | TestNG | Planned |
| 14 | REST Assured Fundamentals | Planned |
| 15 | REST Assured Framework | Planned |
| 16 | Data-Driven API Testing | Planned |
| 17 | Selenium Fundamentals | Planned |
| 18 | Selenium Synchronization | Planned |
| 19 | Selenium Interactions | Planned |
| 20 | Page Object Model | Planned |
| 21 | Selenium Framework | Planned |
| 22 | API + UI Hybrid Testing | Planned |
| 23 | Frontend / Backend Validation | Planned |
| 24 | Test Suites | Planned |
| 25 | CI/CD | Planned |
| 26 | Quality Metrics and GenAI | Planned |

Statuses will be updated as the project progresses.

---

## Testing Approach

The project will use a risk-based testing approach.

Testing will include:

### Functional Testing

Validation of expected application behaviour against business workflows.

### Positive Testing

Validation using valid inputs and expected user behaviour.

### Negative Testing

Validation using invalid, missing, malformed, or unexpected inputs.

### Boundary Testing

Testing values around identified input limits.

### Exploratory Testing

Investigation beyond predefined test cases to identify unexpected behaviour and risks.

### API Testing

Validation of:

- HTTP methods
- Status codes
- Response bodies
- Headers
- Response schemas
- Business rules
- Error handling
- Response times
- Request parameters

### UI Automation

Validation of critical user journeys using Selenium WebDriver.

### Integration Testing

Validation of consistency between API responses and frontend behaviour where supported by the application.

---

## Automation Strategy

Automation will focus on stable, repeatable, business-critical workflows.

Priority automation candidates include:

- User Registration
- Valid Login
- Invalid Login
- Logout
- Product Listing
- Product Search
- Product Details
- Category Filtering
- Brand Filtering
- Add to Cart
- Cart Quantity
- Remove from Cart
- Checkout
- Account API Lifecycle

Not every manual test will be automated.

Automation decisions will consider:

- Business criticality
- Regression frequency
- Stability
- Execution time
- Maintenance cost
- Test-data requirements
- Automation return on investment

---

## Planned Automation Architecture

The automation project is expected to evolve toward a structure similar to:

```text
automation/
│
├── pom.xml
├── testng.xml
│
└── src/
    ├── main/java/
    │   ├── pages/
    │   ├── api/
    │   ├── models/
    │   └── utils/
    │
    └── test/java/
        ├── ui/
        ├── api/
        ├── integration/
        └── base/
```

The architecture will be introduced progressively rather than created prematurely.

---

## Test Execution Strategy

Automated tests will eventually be organized into groups such as:

- Smoke
- Regression
- API
- UI
- Integration

Example future execution command:

```bash
mvn clean test
```

TestNG suites may also be used to execute specific groups or testing layers.

---

## Test Data Strategy

Test data may be managed using:

- TestNG DataProvider
- Java objects
- JSON files
- CSV files
- Runtime-generated unique data

Dynamic data will be preferred where account creation or reusable test environments require unique values.

Sensitive information must not be committed to the repository.

---

## Reporting

The project will eventually include reporting for:

- Total tests executed
- Passed tests
- Failed tests
- Skipped tests
- Execution duration
- Defects
- Smoke coverage
- Regression coverage
- API automation coverage
- UI automation coverage
- Failed-test screenshots

---

## Database Testing Limitation

Automation Exercise does not provide direct database access.

Therefore, this project will not claim direct SQL/database validation against the application.

Backend validation will instead focus on:

```text
API
 ↕
Application State
 ↕
UI
```

Direct database testing should be practiced separately against an application or environment that provides controlled database access.

---

## Generative AI Usage

Generative AI may be used as a testing accelerator for activities such as:

- Requirement analysis
- Identifying missing test scenarios
- Boundary-condition brainstorming
- Test-data generation
- Automation code review
- Failure analysis
- Documentation improvements

AI-generated outputs will be reviewed and validated before being used in the test project.

Sensitive or production customer information should not be submitted to unapproved AI services.

---

## Current Project Status

**Phase:** Project Setup

Current activities:

- [x] GitHub repository created
- [x] Initial README created
- [x] `.gitignore` created
- [ ] Application analysis
- [ ] System context
- [ ] Test strategy
- [ ] Manual test scenarios
- [ ] Manual test cases
- [ ] API testing
- [ ] API automation
- [ ] UI automation
- [ ] CI/CD

---

## Next Practice

The next activity is:

**Practice 01: Application Analysis**

Planned document:

```text
docs/01-application-analysis/application-analysis.md
```

The goal is to understand the application's modules, workflows, dependencies, risks, and testable functionality before designing detailed tests or automation.
