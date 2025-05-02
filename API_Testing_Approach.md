# API Testing Approach and Response Validation

This document addresses requirement to **"Create a Postman collection to test a RESTful API. 
Include tests for various endpoints and explain your approach to validating responses."**

## Overview of API Testing Strategy

Postman collection implements a comprehensive testing strategy for RESTful API endpoints using the JSONPlaceholder and GitHub APIs as examples. 
The collection demonstrates different types of requests (GET, POST) and various validation approaches for each endpoint.

## Endpoints Tested

The collection tests the following endpoints:

1. **GET /users** - Retrieve a list of all users
2. **GET /users/{userId}** - Retrieve a specific user by ID
3. **POST /posts** - Create a new post
4. **GET /nonexistentendpoint** - Test error handling with a non-existent endpoint
5. **GET /rate_limit** - Test rate limiting handling (GitHub API)
6. **GET /user** - Test authenticated user information (GitHub API)
7. **Mock server endpoint** - Test mock server implementation

## Response Validation Approach

Approach to validating API responses as below :

### 1. Status Code Validation

All endpoints include status code validation to ensure the correct HTTP response:

```javascript
pm.test("Status code is 200", function () {
  pm.response.to.have.status(200);
});
```

For POST requests, we validate for status 201 (Created):

```javascript
pm.test("Status code is 201", function () {
  pm.response.to.have.status(201);
});
```

For error testing, we validate for the expected error code:

```javascript
pm.test("Should return 404", function () {
  pm.response.to.have.status(404);
});
```

### 2. Response Header Validation

validate response headers to ensure proper content type and other important metadata:

```javascript
pm.test("Content-Type is present", function () {
  pm.response.to.have.header("Content-Type");
});
```

### 3. Response Structure Validation

validate the overall structure of responses to ensure they match expected formats:

```javascript
// For array responses
pm.test("Response is an array", function () {
  var jsonData = pm.response.json();
  pm.expect(jsonData).to.be.an("array");
});

// For object responses
pm.test("Response has an ID", function () {
  var jsonData = pm.response.json();
  pm.expect(jsonData).to.have.property("id");
});
```

### 4. Schema Validation

For complex responses, implemented full schema validation using JSON Schema:

```javascript
pm.test("User schema is valid", function () {
  const schema = {
    type: "object",
    required: ["id", "name", "email", "address", "phone", "website", "company"],
    properties: {
      id: { type: "number" },
      name: { type: "string" },
      email: { type: "string" },
      address: {
        type: "object",
        required: ["street", "suite", "city", "zipcode", "geo"],
        properties: {
          // nested schema properties
        },
      },
    },
  };

  var jsonData = pm.response.json();
  pm.expect(tv4.validate(jsonData, schema)).to.be.true;
});
```

### 5. Data Content Validation

Validate the actual content of the data to ensure business rules are followed:

```javascript
// Format validation
pm.test("Email format is valid", function () {
  var jsonData = pm.response.json();
  pm.expect(jsonData.email).to.match(
    /^[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}$/
  );
});

// Data matching validation
pm.test("Response contains correct data", function () {
  var jsonData = pm.response.json();
  pm.expect(jsonData.title).to.eql(pm.environment.get("title"));
  pm.expect(jsonData.body).to.eql(pm.environment.get("body"));
  pm.expect(jsonData.userId).to.eql(Number(pm.environment.get("userId")));
});
```

### 6. Performance Validation

Basic performance testing to ensure response times are within acceptable limits:

```javascript
pm.test("Response time is acceptable", function () {
  pm.expect(pm.response.responseTime).to.be.below(1000);
});
```

### 7. Error Handling Validation

Validate proper error handling by intentionally triggering errors and verifying appropriate responses:

```javascript
// Debugging error responses
console.log("Response body:", pm.response.text());
console.log("Response headers:", pm.response.headers.all());
console.log("Response time:", pm.response.responseTime + "ms");
```

## Advanced Testing Techniques

### 1. Data-Driven Testing

Test collection demonstrates data-driven testing by:

- Using environment variables to store test data
- Generating dynamic test data in pre-request scripts
- Passing data between requests in a testing sequence

```javascript
// Generating dynamic test data
pm.environment.set("title", "New Post " + Date.now());
pm.environment.set(
  "body",
  "This is a dynamically generated body for testing purposes created on " +
    new Date().toISOString()
);
```

### 2. Test Sequence and Dependency Management

We manage test dependencies by:

- Setting variables from one request for use in subsequent requests
- Using a logical sequence of requests that build upon each other

```javascript
// Setting a variable for later use
if (pm.response.json().length > 0) {
  pm.environment.set("userId", pm.response.json()[0].id);
  console.log("Set userId variable to: " + pm.environment.get("userId"));
}
```

### 3. Test Reporting

Implemented collection-level test reporting to track test results:

```javascript
// Collection-level test reporting
if (pm.info.requestName === "7. Mock Server Example - Users") {
  console.log("Test Run Summary:");
  console.log("Run ID: " + pm.collectionVariables.get("testRunId"));
  console.log("Total Tests: " + pm.collectionVariables.get("testsRun"));
  console.log("Tests Passed: " + pm.collectionVariables.get("testsPassed"));
  console.log("Tests Failed: " + pm.collectionVariables.get("testsFailed"));
  console.log(
    "Success Rate: " + ((testsPassed / testsRun) * 100).toFixed(2) + "%"
  );
}
```

## Testing Challenges and Solutions

### 1. Authentication Testing

Challenge: Testing authenticated endpoints requires managing tokens and credentials.

Solution: Implemented token-based authentication with environment variables for secure credential handling.

### 2. Rate Limiting

Challenge: APIs often implement rate limiting which can disrupt testing.

Solution: Implemented exponential backoff and retry logic to handle rate limiting gracefully:

```javascript
// Calculate backoff time (exponential backoff)
let delay =
  parseInt(pm.variables.get("retryDelay")) * Math.pow(2, currentRetry - 1);
console.log(
  `Rate limited. Retrying in ${delay}ms (Attempt ${currentRetry} of ${maxRetries})`
);
```

### 3. Environment-Specific Testing

Challenge: Tests need to work across different environments (development, staging, production).

Solution: Used environment variables to abstract environment-specific values.

## Conclusion

Our approach to API testing demonstrates a comprehensive strategy that covers:

1. **Functional correctness** - Ensuring endpoints return expected data
2. **Data validation** - Verifying response content matches requirements
3. **Error handling** - Testing how the API handles invalid inputs
4. **Performance** - Basic response time validation
5. **Security** - Testing authentication and authorization

This multi-layered testing approach ensures thorough validation of API responses across various scenarios, providing confidence in the API's functionality and reliability.
