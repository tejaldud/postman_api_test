# OAuth 2.0 Authentication Setup in Postman

This document addresses the requirement to **"Set up OAuth 2.0 authentication in Postman for a given API and walk through the configuration process and any challenges encountered."**

## Initial OAuth 2.0 Setup Process

I initially attempted to configure OAuth 2.0 authentication with GitHub's API using the standard authorization code flow in Postman. Here's the process we followed:

### 1. Creating a GitHub OAuth Application

1. Navigated to GitHub account settings
2. Selected Developer Settings > OAuth Apps > New OAuth App
3. Configured the application with:
   - Application name: "Postman API Testing"
   - Homepage URL: https://www.postman.com
   - Application description: "API testing with Postman"
   - Authorization callback URL: https://oauth.pstmn.io/v1/callback

### 2. Configuring OAuth 2.0 in Postman

In our Postman collection, we configured the GitHub User Info request with the following OAuth 2.0 settings:

```json
"auth": {
  "type": "oauth2",
  "oauth2": {
    "addTokenTo": "header",
    "tokenName": "github_token",
    "grant_type": "authorization_code",
    "callback_url": "https://oauth.pstmn.io/v1/callback",
    "authUrl": "https://github.com/login/oauth/authorize",
    "accessTokenUrl": "https://github.com/login/oauth/access_token",
    "clientId": "{{github_client_id}}",
    "clientSecret": "{{github_client_secret}}",
    "scope": "user",
    "clientAuthentication": "body"
  }
}
```

### 3. Setting Up Environment Variables

We created environment variables to store the OAuth credentials:

- `github_client_id`: The Client ID from the GitHub OAuth App
- `github_client_secret`: The Client Secret from the GitHub OAuth App

## Challenges Encountered

During implementation, we encountered several challenges with the OAuth 2.0 setup:

1. **Protocol Error**: Initially received an error message:

   ```
   TypeError: Cannot read properties of undefined (reading 'length')
   Error: Invalid protocol for auth URL. Only HTTP and HTTPS protocol are allowed.
   ```

   This suggested issues with the URL format in the OAuth configuration.

2. **Authentication Errors**: After resolving the protocol issue, we still received "Bad credentials" errors when attempting to authenticate.

## Alternative Solution Implemented

Due to the challenges with the OAuth 2.0 flow, implemented an alternative authentication approach using GitHub's Personal Access Tokens (PAT):

1. **Token Generation**: Created a Personal Access Token in GitHub with the "user" scope
2. **Custom Header**: Used a pre-request script to manually set the Authorization header:
   ```javascript
   pm.request.headers.upsert({
     key: "Authorization",
     value: "token " + pm.environment.get("github_access_token"),
   });
   ```

This approach successfully authenticated our requests while simplifying the authentication process for testing purposes.


## Complete OAuth 2.0 Setup Instructions

For a complete OAuth 2.0 setup with GitHub API in Postman, follow these steps:

1. Create GitHub OAuth App:

   - Go to GitHub > Settings > Developer settings > OAuth Apps > New OAuth App
   - Set Homepage URL to your application's URL (https://www.postman.com for testing)
   - Set Authorization callback URL to: https://oauth.pstmn.io/v1/callback
   - Register application and note the Client ID and Client Secret

2. Configure Postman:

   - Set up environment variables for github_client_id and github_client_secret
   - Configure the request's Authorization tab:
     - Type: OAuth 2.0
     - Grant Type: Authorization Code
     - Callback URL: https://oauth.pstmn.io/v1/callback
     - Auth URL: https://github.com/login/oauth/authorize
     - Access Token URL: https://github.com/login/oauth/access_token
     - Client ID: {{github_client_id}}
     - Client Secret: {{github_client_secret}}
     - Scope: user
     - Client Authentication: Send as Basic Auth header

3. Run the Authentication:

   - Click "Get New Access Token"
   - Sign in to GitHub when prompted
   - Authorize the application
   - Postman will receive the token and you can use it by clicking "Use Token"
