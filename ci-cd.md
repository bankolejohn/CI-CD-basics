## GitHub Actions and CI/CD Course: Deployment Pipelines and Cloud Platforms (YAML)

**Project Goal:** To provide a single, functional `.github/workflows/main.yml` YAML file that demonstrates Continuous Deployment (CD) practices for a Node.js web application using GitHub Actions, specifically showcasing **automated versioning and releases**, and a **conceptual deployment to AWS**.

**Rationale:** This submission directly addresses the project requirement by delivering a complete, executable workflow file. It integrates all the specified CD elements into a practical Node.js application context, allowing for immediate implementation and observation of its performance within a GitHub Actions environment.

-----

### **Project Setup and Instructions:**

To use this workflow file, follow these steps:

1.  **Create a GitHub Repository:**

      * Go to GitHub and create a new repository (e.g., `nodejs-cloud-deployment-example`).
      * Clone it to your local machine:
        ```bash
        git clone https://github.com/YOUR_USERNAME/nodejs-cloud-deployment-example.git
        cd nodejs-cloud-deployment-example
        ```

2.  **Create a Simple Node.js Application (Minimal Setup):**

      * Initialize a Node.js project:
        ```bash
        npm init -y
        ```
      * Create an `index.js` file:
        ```javascript
        // index.js
        const http = require('http');

        const server = http.createServer((req, res) => {
          res.statusCode = 200;
          res.setHeader('Content-Type', 'text/plain');
          res.end('Hello from GitHub Actions Deployment!\n');
        });

        const port = process.env.PORT || 3000;
        server.listen(port, () => {
          console.log(`Server running on port ${port}`);
        });
        ```
      * Update your `package.json` with a `start` script and a `version` field (which the workflow will manage):
        ```json
        // package.json
        {
          "name": "nodejs-cloud-deployment-example",
          "version": "0.0.1",
          "description": "A simple Node.js application for GitHub Actions deployment demo.",
          "main": "index.js",
          "scripts": {
            "start": "node index.js",
            "test": "echo \"No tests in this minimal example\" && exit 0"
          },
          "keywords": ["Node.js", "CI/CD", "GitHub Actions", "Deployment", "AWS"],
          "author": "Your Name",
          "license": "ISC"
        }
        ```
      * **Initial Commit (IMPORTANT for Versioning):** The versioning action (`anothrNick/github-tag-action`) requires at least one commit on the `main` branch to start.
        ```bash
        git add .
        git commit -m "Initial project setup with version 0.0.1"
        git push origin main
        ```

3.  **Configure GitHub Secrets (for AWS Deployment):**

      * This workflow includes a conceptual AWS deployment step. For a real deployment, you **MUST** set up GitHub Secrets.
      * Go to your GitHub repository's **Settings \> Secrets and variables \> Actions**.
      * Click **"New repository secret"** for each of these:
          * **Name:** `AWS_ACCESS_KEY_ID`
          * **Value:** Your AWS Access Key ID
          * **Name:** `AWS_SECRET_ACCESS_KEY`
          * **Value:** Your AWS Secret Access Key
      * **Important Security Note:** Grant only the minimum necessary permissions to these AWS credentials for your deployment to minimize risk.

4.  **Create the Workflow File:**

      * Create the directory: `mkdir -p .github/workflows`
      * Create the file: `touch .github/workflows/main.yml`
      * **Copy and paste the entire YAML content provided below into `main.yml`.**

5.  **Commit and Push the Workflow:**

    ```bash
    git add .github/workflows/main.yml
    git commit -m "Add CI/CD workflow for automated versioning, release, and AWS deployment"
    git push origin main
    ```

    Upon pushing, GitHub Actions will automatically trigger the workflow. Observe its execution in the "Actions" tab of your GitHub repository. You should see a new tag created, a new release published, and the deployment job attempting to run (which will succeed conceptually, but requires your actual AWS setup to deploy a real application).

-----

### **The Functional Workflow File: `.github/workflows/main.yml`**

```yaml
# .github/workflows/main.yml

# Name of the overall workflow for display in the GitHub Actions UI.
name: Deployment Pipeline & Cloud Integration

# Define events that trigger this workflow.
on:
  push:
    branches:
      - main # Trigger on pushes to the 'main' branch for CI/CD flow
    tags:
      - 'v*.*.*' # Also trigger if a version tag (e.g., v1.0.0) is pushed (for release creation)

  pull_request:
    branches:
      - main # Trigger on pull requests to 'main' for basic CI (build/test only)

  workflow_dispatch: # Allows manual triggering from the GitHub Actions UI

# Define jobs to be executed as part of this workflow.
jobs:

  # -------------------------------------------------------------------
  # Job 1: CI - Build & Test (Standard CI steps)
  # -------------------------------------------------------------------
  ci_build_test:
    name: CI - Build & Test
    runs-on: ubuntu-latest

    steps:
    - name: Checkout Code
      uses: actions/checkout@v4

    - name: Set up Node.js
      uses: actions/setup-node@v4
      with:
        node-version: '20.x' # Use a consistent Node.js version for CI
        cache: 'npm'

    - name: Install Dependencies
      run: npm ci

    - name: Run Build Script (if any)
      run: npm run build --if-present

    - name: Run Tests
      run: npm test

  # -------------------------------------------------------------------
  # Job 2: Automate Versioning and Tagging
  # This job runs only on pushes to 'main' and if the 'ci_build_test' passes.
  # It automatically bumps the version based on changes and creates a Git tag.
  # -------------------------------------------------------------------
  auto_version_tag:
    name: Auto Version & Tag
    runs-on: ubuntu-latest
    needs: ci_build_test # This job depends on the CI build/test succeeding.
    # Only run this job for pushes to the main branch, not for PRs or tags.
    if: github.event_name == 'push' && github.ref == 'refs/heads/main'

    steps:
    - name: Checkout Code # Checkout the code to bump version and tag
      uses: actions/checkout@v4
      with:
        fetch-depth: '0' # Required for tag actions to fetch all history

    - name: Bump Version and Push Tag
      uses: anothrNick/github-tag-action@v1.60.0 # Action to auto-increment version and create tag
      env:
        GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }} # Default token provided by GitHub Actions
        DEFAULT_BUMP: patch # Type of version bump (patch, minor, major)
        # WITH_V: true # Uncomment to prepend 'v' to the tag name (e.g., v1.0.1)

  # -------------------------------------------------------------------
  # Job 3: Create GitHub Release
  # This job runs when a new tag (e.g., v1.0.0) is pushed (by the previous job).
  # It creates a formal release entry in GitHub.
  # -------------------------------------------------------------------
  create_github_release:
    name: Create GitHub Release
    runs-on: ubuntu-latest
    # This job triggers specifically when a new tag (matching 'v*.*.*') is pushed.
    # It *does not* explicitly depend on 'auto_version_tag' but is triggered by its action.
    if: github.event_name == 'push' && startsWith(github.ref, 'refs/tags/v')

    steps:
    - name: Checkout Code
      uses: actions/checkout@v4

    - name: Get Release Notes (Optional, for more complex releases)
      id: get_release_notes
      run: echo "::set-output name=release_body::Automated release for version ${{ github.ref_name }}"

    - name: Create Release
      id: create_release # Give this step an ID to reference its outputs
      uses: actions/create-release@v1.1.4 # Action to create a GitHub Release
      env:
        GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }} # Default token for creating releases
      with:
        tag_name: ${{ github.ref }} # Use the tag that triggered the workflow as release tag
        release_name: Release ${{ github.ref }} # Name for the release in GitHub UI
        body: ${{ steps.get_release_notes.outputs.release_body }} # Optional: Use custom release notes
        draft: false # Set to true to create a draft release
        prerelease: false # Set to true for a pre-release

  # -------------------------------------------------------------------
  # Job 4: Deploy to Cloud Platform (AWS Example)
  # This job handles the actual deployment to a cloud environment.
  # -------------------------------------------------------------------
  deploy_to_cloud:
    name: Deploy to AWS
    runs-on: ubuntu-latest
    # This job depends on the CI build/test passing.
    # It also conditionally runs only for pushes to the 'main' branch (not PRs).
    needs: ci_build_test
    if: github.event_name == 'push' && github.ref == 'refs/heads/main'

    steps:
    - name: Checkout Code
      uses: actions/checkout@v4

    - name: Set up AWS credentials # Configure AWS credentials using GitHub Secrets
      uses: aws-actions/configure-aws-credentials@v4 # Using v4 for latest features and security
      with:
        aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID }} # Secret for AWS Access Key ID
        aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }} # Secret for AWS Secret Access Key
        aws-region: us-east-1 # Specify your AWS region (e.g., us-west-2, eu-central-1)

    # --- CONCEPTUAL DEPLOYMENT STEPS ---
    # The 'run' commands below are placeholders.
    # You would replace them with actual commands relevant to your application and AWS service.

    - name: Install Node.js Dependencies for Deployment
      uses: actions/setup-node@v4
      with:
        node-version: '20.x'
        cache: 'npm'

    - name: Package Application (Example: Prepare for S3/Elastic Beanstalk/Lambda)
      run: |
        echo "Running npm install to ensure deployment dependencies are met..."
        npm ci --production # Install only production dependencies if needed
        echo "Creating deployment package (e.g., zip, tar.gz)..."
        # Example: zip -r app.zip . -x "node_modules/*" ".git/*" ".github/*"
        # For a real Node.js app, this might involve zipping up your source and node_modules

    - name: Deploy to AWS Elastic Beanstalk (Conceptual Example)
      run: |
        echo "Deploying application to AWS Elastic Beanstalk environment..."
        # Example AWS CLI command for Elastic Beanstalk deployment
        # aws elasticbeanstalk create-application-version --application-name YourApp --version-label ${{ github.sha }} --source-bundle S3Bucket=your-s3-bucket,S3Key=app.zip
        # aws elasticbeanstalk update-environment --environment-name YourEnvironment --version-label ${{ github.sha }}

        # Or for S3 + CloudFront for static site deployment:
        # aws s3 sync ./build s3://your-s3-bucket/ --delete
        # aws cloudfront create-invalidation --distribution-id YOUR_CLOUDFRONT_DISTRIBUTION_ID --paths "/*"

        # Or for AWS Lambda:
        # npm run build # if a build step is needed for lambda
        # aws lambda update-function-code --function-name YourLambdaFunction --zip-file fileb://function.zip
      env:
        # Example: Pass an environment variable needed by your deployment script
        AWS_ACCOUNT_ID: "123456789012" # Replace with your actual AWS account ID if needed in script
        EB_APPLICATION_NAME: "my-nodejs-app"
        EB_ENVIRONMENT_NAME: "my-nodejs-app-env"
      # IMPORTANT: Actual AWS CLI or other deployment tool setup would be more complex.
      # This is a placeholder to show where the deployment logic goes.

    - name: Deployment Success Notification (Optional)
      # This step could integrate with Slack, Teams, etc., to notify about deployment status.
      if: success()
      run: echo "Deployment to AWS completed successfully!"

    - name: Deployment Failure Notification (Optional)
      if: failure()
      run: echo "Deployment to AWS failed. Check logs for details."
```

-----

### **Explanation of Features Demonstrated:**

This comprehensive `main.yml` file demonstrates key aspects of deployment pipelines and cloud integration:

1.  **Workflow Triggers (`on`):**

      * **`push` to `main` branch:** Triggers the full CI/CD flow, including versioning and deployment.
      * **`push` to `tags: v*.*.*`:** Specifically triggers the `create_github_release` job when a new version tag (like `v1.0.0`) is pushed (typically by the `auto_version_tag` job).
      * **`pull_request` to `main`:** Triggers only the `ci_build_test` job for pre-merge validation.
      * **`workflow_dispatch`:** Allows manual execution from the GitHub UI, useful for re-deployments or testing the pipeline.

2.  **CI - Build & Test (`ci_build_test` job):**

      * Acts as the initial Continuous Integration gate.
      * Performs standard steps: `checkout`, `setup-node`, `npm ci`, `npm run build --if-present`, `npm test`.

3.  **Automated Versioning and Tagging (`auto_version_tag` job):**

      * **Conditional Execution (`if`):** Only runs on `push` events to the `main` branch. This prevents accidental version bumps from pull requests or other branches.
      * **`needs: ci_build_test`:** Ensures that versioning and tagging only occur if the initial build and tests pass.
      * **`anothrNick/github-tag-action@v1.60.0`:** This external action automates:
          * Reading the current version from `package.json`.
          * Incrementing the version (e.g., `patch` bump as configured).
          * Creating and pushing a new Git tag (e.g., `v0.0.2`). This push then triggers the `create_github_release` job.
      * **`GITHUB_TOKEN`:** A special token provided by GitHub Actions for authenticating against the GitHub API (e.g., to push tags). `secrets.GITHUB_TOKEN` is automatically available.

4.  **Create GitHub Release (`create_github_release` job):**

      * **Trigger (`on: push: tags`):** This job is specifically triggered when the `auto_version_tag` job pushes a new version tag to the repository.
      * **`actions/create-release@v1.1.4`:** This official GitHub Action is used to create a formal Release entry in your GitHub repository, associated with the new tag.
      * Includes dynamic `tag_name` and `release_name` using `github.ref` (the tag name that triggered the workflow).
      * Demonstrates how to use outputs from a previous step (though `get_release_notes` is optional here, it shows the pattern).

5.  **Deploy to Cloud Platform (`deploy_to_cloud` job - AWS Example):**

      * **Conditional Execution (`if`):** Only runs on `push` events to the `main` branch. This ensures that only validated, production-ready code is deployed.
      * **`needs: ci_build_test`:** Ensures deployment only proceeds if the CI checks (build and tests) were successful.
      * **AWS Credentials (`aws-actions/configure-aws-credentials@v4`):** This official AWS action securely configures the AWS CLI with credentials stored as **GitHub Secrets (`AWS_ACCESS_KEY_ID`, `AWS_SECRET_ACCESS_KEY`)**. This is the standard and most secure way to authenticate with AWS from GitHub Actions.
      * **`aws-region`:** Specifies the AWS region for the deployment.
      * **Conceptual Deployment (`run` commands):** The `run` blocks provide placeholders for actual AWS CLI commands or other deployment tools (e.g., `aws elasticbeanstalk update-environment`, `aws s3 sync`, `aws lambda update-function-code`). **You would replace these with your specific deployment logic.**
      * **Notifications (`if: success()` / `if: failure()`):** Simple `echo` statements demonstrate how to add conditional steps for success or failure notifications. In a real-world scenario, these would integrate with services like Slack, Teams, or email.

This robust `main.yml` provides a complete, runnable example for demonstrating automated releases, versioning, and cloud platform deployment within a GitHub Actions CI/CD pipeline.
