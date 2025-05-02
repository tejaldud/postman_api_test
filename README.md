# Postman Technical Interview Collection

This repository contains a Postman collection that addresses the requirements for the Postman Technical Interview. The collection demonstrates various Postman capabilities including API testing, debugging, authentication, scripting, and more.

## Contents

- `API_Test_Collection.json` - The Postman collection with requests, tests, and scripts
- `API_Test_Environmentjson` - Environment variables for the collection
- `OAuth_2.0_Setup_Process.md` - Detailed documentation of OAuth 2.0 setup process and challenges
- `API_Testing_Approach.md` - Comprehensive explanation of API testing strategy and response validation

## How to Import and Use

### Importing the Collection and Environment

1. Open Postman
2. Click "Import" in the top left corner
3. Drag and drop both JSON files or use the file browser to select them
4. Both the collection and environment will be imported

### Setting Up the Environment

1. Click the environment dropdown in the top right corner of Postman
2. Select " API Tests Environment"
3. For the GitHub API authentication:
   - Create a personal access token at GitHub Developer Settings (https://github.com/settings/personal-access-tokens)
   - Select the "user" scope when creating your token
   - Update the `github_access_token` environment variable with your token
   - For details about our OAuth 2.0 setup process and challenges, see the `OAuth_2.0_Setup_Process.md` file

### Setting Up a Mock Server (Optional)

1. In Postman, click on "Mock Servers" in the left sidebar
2. Click "Create a mock server"
3. Select the "API Tests" collection
4. Choose "Users Mock Response" as an example
5. Name your mock server (e.g., "Interview Mock Server")
6. After creation, copy the mock server URL
7. Update the `mock_server_url` environment variable with this URL

## Running the Collection

### Manual Execution

- You can run individual requests in the collection by clicking the "Send" button
- Each request has associated tests that will execute automatically
- View test results in the "Tests" tab of the response panel

### Using Collection Runner

1. Click the three dots (...) next to the collection name
2. Select "Run collection"
3. Configure the run settings:
   - Select all requests
   - Set iterations to 1
   - Make sure " API Tests Environment" is selected
4. Click "Run API Tests"
5. View the results of all tests in the Collection Runner

## Features Demonstrated

1. **API Testing**

   - Basic response validation
   - Schema validation
   - Data verification

2. **Debugging Techniques**

   - Handling 404 errors
   - Console logging
   - Troubleshooting steps

3. **Authentication**

   - OAuth 2.0 setup process and challenges (documented in `OAuth_2.0_Setup_Process.md`)
   - GitHub API authentication using Personal Access Token
   - Token-based security implementation

4. **Advanced Scripting**

   - Pre-request scripts for dynamic data generation
   - Test scripts for response validation
   - Collection-level scripting for test reporting

5. **Mock Server Usage**

   - Example response setup
   - Mock server benefits

6. **Rate Limit Handling**

   - Detecting rate limits
   - Implementing exponential backoff
   - Automatic retries

7. **Test Automation**
   - Collection runner configuration
   - Test reporting

## Notes

- The collection uses public APIs (JSONPlaceholder and GitHub)
- For GitHub API authentication,initially attempted OAuth 2.0 but documented the challenges and ultimately used a simpler Personal Access Token approach for demonstration
