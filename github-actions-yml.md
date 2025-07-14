* * *

# GitHub Actions and CI/CD Course Project - YAML

Welcome to this engaging and practical course on GitHub Actions and Continuous Integration/Continuous Deployment (CI/CD). In this course, you'll embark on a journey to master the art of automating your software development processes using one of the most powerful tools available on GitHub. Whether you're a seasoned developer or a beginner, this course is designed to equip you with the essential skills needed to streamline your development workflow, enhance the quality of your code, and significantly reduce the time to deploy new features and fixes.

* * *

## Why This Course is Relevant for Learners

Imagine you are a conductor of an orchestra. Each musician (developer) plays a different instrument (code) and must synchronize perfectly to create harmonious music (software). In this scenario, GitHub Actions and CI/CD processes are like your conductor's baton, helping you to orchestrate the diverse elements of software development.

Just as a conductor ensures that each musician enters at the right time and the music flows smoothly, CI/CD coordinates the various stages of development, testing, and deployment, ensuring that the final product is delivered seamlessly and efficiently. This course, therefore, is not just about learning the technicalities of GitHub Actions; it's about learning how to conduct your software development orchestra with skill and precision, leading to a symphony of streamlined processes and high-quality outcomes.

* * *

## Lesson 3: Workflow Syntax and Structure

### Objectives

*   Understand YAML syntax for workflows.
    
*   Learn the structure and components of a workflow.
    

### Pre-requisites

To get the most out of this lesson, ensure you have the following:

*   **GitHub Account:**
    
    *   Necessary for repository management and GitHub Actions.
        
    *   Sign up at [GitHub](https://github.com/join).
        
*   **Git Installed:**
    
    *   Required for version control and managing code changes.
        
    *   Installation guide: [Git Installation](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git).
        
*   **Basic Knowledge of Git:**
    
    *   Understanding of basic Git commands (`clone`, `commit`, `push`, `pull`).
        
    *   Tutorial: [Git Basics](https://git-scm.com/book/en/v2/Git-Basics-Getting-a-Git-Repository).
        
*   **Node.js and npm Installed:**
    
    *   Node.js is the runtime for the project, and npm is the package manager.
        
    *   Download and installation: [Node.js Downloads](https://nodejs.org/en/download/).
        
    *   Verify installation with `node -v` and `npm -v` in the terminal.
        
*   **Familiarity with JavaScript:**
    
    *   Basic understanding of JavaScript programming.
        
    *   Tutorial: [JavaScript Guide (MDN Web Docs)](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide).
        
*   **Text Editor or IDE:**
    
    *   A code editor such as Visual Studio Code, Atom, or Sublime Text.
        
    *   Visual Studio Code: [Download VS Code](https://code.visualstudio.com/download).
        
*   **Access to a Command Line Interface (CLI):**
    
    *   Terminal on macOS/Linux or Command Prompt/PowerShell on Windows.
        
    *   Guide: [The Command Line Interface (CLI) (Codecademy)](https://www.google.com/search?q=https://www.codecademy.com/articles/what-is-the-command-line).
        
*   **Basic Understanding of YAML:**
    
    *   YAML is used for writing GitHub Actions workflows.
        
    *   Tutorial: [Learn YAML in Y Minutes](https://learnxinyminutes.com/docs/yaml/).
        
*   **Internet Connection:**
    
    *   Required for accessing GitHub, documentation, and online resources.
        
*   **Willingness to Learn and Experiment:**
    
    *   Openness to exploring new tools and troubleshooting.
        

### Lesson Details

#### YAML Syntax for Workflows

YAML (YAML Ain't Markup Language) is a human-readable data serialization standard commonly used for configuration files. GitHub Actions workflows are defined using YAML.

**Key concepts:**

*   **Indentation:** YAML uses indentation (spaces, _not_ tabs) to define structure and hierarchy. Consistent indentation is critical.
    
*   **Key-Value Pairs:** Data is represented as `key: value`.
    
*   **Lists (Sequences):** Items in a list are denoted by a hyphen (`-`).
    

**Example snippet:**

YAML

    name: Example Workflow # Key-value pair
    on: [push]            # List with one item 'push'
    jobs:                # Key indicating a block of jobs
      build:             # Key for a job
        runs-on: ubuntu-latest # Key-value pair for the runner
        steps:           # Key for a list of steps
        - name: Hello World # List item (step), with a key-value pair 'name'
          run: echo "Hello!"  # Key-value pair for a command
    

#### Workflow Structure and Components

A GitHub Actions workflow file, typically located in the `.github/workflows` directory, is structured around several core components:

*   **Workflow File (`.github/workflows/your-workflow-name.yml`):**
    
    *   This is the file where you define your entire CI/CD process.
        
    *   The file name can be anything you choose, but it must end with `.yml` or `.yaml`.
        
*   **`name` (Optional, but Recommended):**
    
    *   A human-readable name for your workflow. This name appears in the GitHub UI when the workflow runs.
        
*   **`on` (Event):**
    
    *   Defines the event(s) that trigger the workflow. This could be a `push`, `pull_request`, `schedule`, `workflow_dispatch` (manual trigger), etc.
        
*   **`jobs`:**
    
    *   A collection of named jobs that run as part of the workflow.
        
    *   Jobs run in parallel by default, but you can configure them to run sequentially using the `needs` keyword.
        
*   **`job_id` (e.g., `build`, `test`, `deploy`):**
    
    *   A unique identifier for each job.
        
*   **`runs-on` (Runner):**
    
    *   Specifies the type of virtual environment (runner) where the job will execute. GitHub-hosted runners include `ubuntu-latest`, `windows-latest`, `macos-latest`. You can also use self-hosted runners.
        
*   **`steps`:**
    
    *   A sequence of individual tasks that make up a job.
        
    *   Steps are executed in the order they are defined.
        
*   **`name` (Optional, for steps):**
    
    *   A descriptive name for a step, appearing in the GitHub UI.
        
*   **`uses` (Action):**
    
    *   Specifies an action to run as part of a step. Actions are reusable units of code, often from the GitHub Marketplace (e.g., `actions/checkout@v4`, `actions/setup-node@v4`).
        
*   **`run` (Command):**
    
    *   Executes shell commands directly on the runner (e.g., `npm install`, `echo "Hello"`).
        
*   **`with`:**
    
    *   Used to pass input parameters to an action.
        
*   **`env` (Environment Variables):**
    
    *   Allows you to define environment variables that can be accessed by steps within a job or workflow.
        
*   **`secrets`:**
    
    *   Used to access encrypted environment variables (secrets) stored in your GitHub repository.
        

* * *

## Module 3: Implementing Continuous Integration

### Lesson 1: Building and Testing Code

#### Objectives

*   Set up build steps in GitHub Actions.
    
*   Run tests as part of the CI process.
    

#### Setting Up Build Steps

In your GitHub Actions workflow file (e.g., `.github/workflows/main.yml`), you'll define a job responsible for building and testing your code.

**Defining the Build Job:**

Start by defining a job named `build` within the `jobs` section of your YAML file.

YAML

    # .github/workflows/main.yml
    name: CI Pipeline
    
    on: [push, pull_request] # Trigger on push and pull request events
    
    jobs:
      build:
        runs-on: ubuntu-latest # Run this job on the latest Ubuntu runner
        steps:
          # Steps will be defined next
    

**Adding Build Steps:**

Each step in the job performs a specific task. Here, we add steps for checking out the code, installing dependencies, and running the build script (if your project has one).

YAML

    # ... inside the 'build' job's 'steps' section
        steps:
        - uses: actions/checkout@v4 # Using v4 for the latest stable version
          # 'actions/checkout@v4' is a pre-made action that checks out your repository
          # under $GITHUB_WORKSPACE, so your workflow can access the code.
    
        - name: Set up Node.js environment # Added for Node.js projects
          uses: actions/setup-node@v4
          with:
            node-version: '20.x' # Specify a Node.js version, e.g., 20.x
    
        - name: Install dependencies
          run: npm ci # 'npm ci' is preferred for CI/CD for consistent builds
          # 'npm ci' installs the dependencies defined in your project's 'package.json'
          # and ensures that 'package-lock.json' is used for exact versions.
    
        - name: Build application
          run: npm run build --if-present
          # 'npm run build --if-present' runs the build script defined in your
          # 'package.json'. This is typically used for compiling, transpiling,
          # or preparing your code for deployment. The '--if-present' flag prevents
          # the step from failing if no build script exists.
    

#### Running Tests in the Workflow

After the build steps, it's crucial to include steps to execute your automated test scripts. This ensures that your code is not only built but also passes all the tests, maintaining code quality.

YAML

    # ... inside the 'build' job's 'steps' section, after the build step
        - name: Run tests
          run: npm test
          # 'npm test' runs the test script defined in your 'package.json'.
          # It's crucial for ensuring that your code works as expected before any
          # further deployment or integration.
    

**Complete Build and Test Workflow Example:**

YAML

    # .github/workflows/main.yml
    name: Node.js CI Build & Test
    
    on:
      push:
        branches: [ main ]
      pull_request:
        branches: [ main ]
    
    jobs:
      build:
        runs-on: ubuntu-latest
    
        strategy:
          matrix:
            node-version: [16.x, 18.x, 20.x] # Test across multiple Node.js versions
    
        steps:
        - uses: actions/checkout@v4
          name: Checkout repository
    
        - name: Set up Node.js ${{ matrix.node-version }}
          uses: actions/setup-node@v4
          with:
            node-version: ${{ matrix.node-version }}
            cache: 'npm' # Cache npm dependencies for faster builds
    
        - name: Install dependencies
          run: npm ci
    
        - name: Build application
          run: npm run build --if-present
    
        - name: Run tests
          run: npm test
    

#### Learner Notes:

*   The `build` job combines steps to check out the code, set up the Node.js environment, install dependencies, build the code, and run tests.
    
*   The `runs-on: ubuntu-latest` line specifies that the job should run on the latest version of Ubuntu provided by GitHub Actions.
    
*   Using actions like `actions/checkout@v4` and `actions/setup-node@v4` helps in leveraging community-maintained actions to simplify common tasks. Always try to use the latest stable version of actions (e.g., `@v4`).
    
*   Commands like `npm ci`, `npm run build`, and `npm test` are standard Node.js commands used for managing dependencies, building, and testing Node.js applications. `npm ci` is generally preferred over `npm install` in CI environments for more consistent and reliable builds.
    
*   The `strategy.matrix` allows you to efficiently test your application against multiple versions of Node.js, ensuring broader compatibility.
    

* * *

## Additional YAML Concepts in GitHub Actions

### Objectives

*   Deepen understanding of advanced YAML features used in GitHub Actions.
    
*   Explore the use of environment variables and secrets in workflows.
    

### Detailed Steps and Code Explanation:

#### Using Environment Variables

Environment variables allow you to dynamically pass configuration and settings to your workflow, jobs, or individual steps. They can be defined at different scopes:

*   **Workflow Level:** Available to all jobs and steps in the workflow.
    
*   **Job Level:** Available to all steps within that specific job.
    
*   **Step Level:** Available only to that particular step.
    

YAML

    # .github/workflows/environment-variables.yml
    name: Environment Variable Example
    
    on: [push]
    
    env: # Workflow-level environment variable
      GLOBAL_CUSTOM_VAR: "Hello from Workflow!"
    
    jobs:
      example-job:
        runs-on: ubuntu-latest
        env: # Job-level environment variable
          JOB_CUSTOM_VAR: "Hello from Job!"
        steps:
        - name: Use environment variables
          env: # Step-level environment variable
            STEP_CUSTOM_VAR: "Hello from Step!"
          run: |
            echo "Global variable: $GLOBAL_CUSTOM_VAR"
            echo "Job variable: $JOB_CUSTOM_VAR"
            echo "Step variable: $STEP_CUSTOM_VAR"
            echo "Using path variable: $PATH" # Access built-in env variables
    

#### Working with Secrets

Secrets are encrypted variables that you set in your GitHub repository settings. They are ideal for storing sensitive data like API keys, access tokens, passwords, or cloud credentials. Secrets are not exposed in logs or publicly visible.

To use secrets:

1.  Go to your GitHub repository.
    
2.  Navigate to **Settings > Secrets and variables > Actions**.
    
3.  Click "New repository secret" and add your secret (e.g., `MY_API_KEY`, `HEROKU_API_KEY`).
    

YAML

    # .github/workflows/secrets-example.yml
    name: Secrets Example
    
    on: [push]
    
    jobs:
      deploy:
        runs-on: ubuntu-latest
        steps:
        - name: Access sensitive data
          run: |
            echo "API Key: ${{ secrets.MY_API_KEY }}"
            # Do not echo real secrets in production workflows! This is for demonstration.
            # In a real scenario, you would pass this secret to a deployment tool.
          env:
            # You can also pass secrets as environment variables to specific tools
            DATABASE_PASSWORD: ${{ secrets.DB_PASSWORD }}
    

**Security Best Practice:** Never log secrets directly. The example above is purely for demonstration. In a real deployment, you would pass the secret directly to the tool or command that requires it without printing it to the console.

#### Conditional Execution

You can control when jobs, steps, or even entire workflows run based on specific conditions using the `if` keyword. Conditions use expressions that evaluate to `true` or `false`.

YAML

    # .github/workflows/conditional-execution.yml
    name: Conditional Execution Example
    
    on: [push, pull_request]
    
    jobs:
      build:
        runs-on: ubuntu-latest
        steps:
        - uses: actions/checkout@v4
        - name: Run tests (only on push)
          if: github.event_name == 'push'
          run: echo "Running tests only because this is a push event."
    
      deploy:
        runs-on: ubuntu-latest
        needs: build # This job runs only if the 'build' job succeeds
        if: github.event_name == 'push' && github.ref == 'refs/heads/main' # Only deploy for pushes to main branch
        steps:
        - name: Deploy application
          run: echo "Deploying to production!"
    

Common `github` context properties for conditions:

*   `github.event_name`: The name of the event that triggered the workflow (e.g., `push`, `pull_request`).
    
*   `github.ref`: The branch or tag that triggered the workflow (e.g., `refs/heads/main`).
    
*   `github.actor`: The username of the user who initiated the workflow.
    

#### Using Outputs and Inputs between Steps

You can share data between steps within the same job using outputs. One step can set an output, and a subsequent step can retrieve that output.

YAML

    # .github/workflows/outputs-inputs.yml
    name: Outputs and Inputs Example
    
    on: [push]
    
    jobs:
      data-sharing-job:
        runs-on: ubuntu-latest
        steps:
        - id: generate-data # Assign an ID to the step
          name: Generate a random number
          run: |
            RANDOM_NUMBER=$(shuf -i 1-100 -n 1) # Generate a random number
            echo "::set-output name=my_random_value::$RANDOM_NUMBER" # Set an output named 'my_random_value'
            echo "Generated: $RANDOM_NUMBER"
    
        - name: Use generated data
          run: |
            echo "The random number generated in the previous step was: ${{ steps.generate-data.outputs.my_random_value }}"
    

*   `id`: A unique identifier for a step. This is crucial for referencing its outputs.
    
*   `::set-output name=OUTPUT_NAME::OUTPUT_VALUE`: This special syntax in a `run` command sets an output for the current step.
    
*   `steps.STEP_ID.outputs.OUTPUT_NAME`: This syntax is used by subsequent steps to access the output from a previous step.
    

#### Learner Notes:

*   **Environment variables** and **secrets** are crucial for managing configurations and sensitive data in your CI/CD pipelines, ensuring flexibility and security.
    
*   **Conditional execution** helps tailor the workflow based on specific criteria (e.g., running deploy only on `main` branch pushes), making your CI/CD process more efficient and controlled.
    
*   **Sharing data between steps** using outputs allows for more complex workflows where the outcome or data of one step can influence or provide input to subsequent steps within the same job.
    
*   These advanced features enhance the flexibility and security of your GitHub Actions workflows, enabling a more robust CI/CD process.
    

* * *

## Module 3: Implementing Continuous Integration (Continued)

### Lesson 2: Configuring Build Matrices

#### Objectives

*   Implement parallel and matrix builds.
    
*   Manage dependencies across different environments.
    

#### Implementing Parallel and Matrix Builds

The `strategy.matrix` feature in GitHub Actions allows you to run multiple jobs in parallel, based on a matrix of different variables. This is incredibly powerful for:

*   **Testing across multiple environments:** E.g., different operating systems (Ubuntu, Windows, macOS) or different versions of a runtime (Node.js 16, 18, 20; Python 3.8, 3.9, 3.10).
    
*   **Ensuring compatibility:** Verifying that your application works correctly across various configurations.
    
*   **Speeding up CI:** By running tests in parallel, you can get feedback much faster.
    

**Example: Testing across multiple Node.js versions and operating systems:**

YAML

    # .github/workflows/matrix-build.yml
    name: Matrix Build Example
    
    on: [push, pull_request]
    
    jobs:
      test-matrix:
        runs-on: ${{ matrix.os }} # Runner OS from the matrix
    
        strategy:
          matrix:
            node-version: [16.x, 18.x, 20.x]
            os: [ubuntu-latest, windows-latest] # Test on Ubuntu and Windows
    
        steps:
        - uses: actions/checkout@v4
          name: Checkout code
    
        - name: Set up Node.js ${{ matrix.node-version }} on ${{ matrix.os }}
          uses: actions/setup-node@v4
          with:
            node-version: ${{ matrix.node-version }}
            cache: 'npm'
    
        - name: Install dependencies
          run: npm ci
    
        - name: Run tests
          run: npm test
    
        - name: Display environment info (Optional)
          if: runner.os == 'Linux' # Conditional step based on OS from matrix
          run: |
            echo "Running on Linux"
            node -v
            npm -v
    

**Explanation:**

*   **`strategy.matrix`**: Defines the variables for the matrix.
    
    *   `node-version`: A list of Node.js versions to test.
        
    *   `os`: A list of operating systems to test on.
        
*   **`runs-on: ${{ matrix.os }}`**: The runner dynamically changes based on the current `os` value from the matrix.
    
*   **`${{ matrix.node-version }}`**: This syntax accesses the current value of `node-version` from the matrix for the specific job instance.
    
*   When this workflow runs, GitHub Actions will create `node-version * os` (3 \* 2 = 6) separate jobs, each running in parallel with a different combination of Node.js version and operating system.
    

#### Managing Dependencies Across Different Environments

When using matrix builds, it's essential to ensure that your dependency management strategy is robust across all environments defined in your matrix.

*   **`npm ci` vs. `npm install`**: As mentioned, `npm ci` is generally preferred in CI environments. It strictly installs dependencies based on `package-lock.json` (or `npm-shrinkwrap.json`), which helps ensure consistent builds across different runners and Node.js versions. `npm install` might update `package.json` and `package-lock.json`, which is not desirable in CI.
    
*   **Caching Dependencies**: The `actions/setup-node@v4` action includes a `cache: 'npm'` option (or `cache: 'yarn'`, `cache: 'pnpm'`) which intelligently caches your `node_modules` directory. This significantly speeds up subsequent workflow runs by avoiding re-downloading and re-installing dependencies every time. The cache key is typically based on your `package-lock.json`, so it only invalidates and recreates the cache when your dependencies actually change.
    

**Benefits of Matrix Builds:**

*   **Comprehensive Testing:** Ensures your application works across all target environments.
    
*   **Faster Feedback:** Parallel execution reduces the overall time to run your CI pipeline.
    
*   **Reduced Risk:** Catches environment-specific bugs early in the development cycle.
    

**Excluding and Including Configurations:**

You can fine-tune your matrix with `exclude` and `include` directives.

*   **`exclude`**: Prevents certain combinations from running.
    
    YAML
    
        strategy:
          matrix:
            node-version: [16.x, 18.x, 20.x]
            os: [ubuntu-latest, windows-latest]
            exclude: # Don't run Node.js 16 on Windows
              - node-version: 16.x
                os: windows-latest
        
    
*   **`include`**: Adds additional configurations outside the main matrix or overrides existing ones.
    
    YAML
    
        strategy:
          matrix:
            node-version: [18.x, 20.x]
            os: [ubuntu-latest]
          include: # Also run Node.js 16 on macOS
            - node-version: 16.x
              os: macos-latest

## Screenshots

<img width="1375" height="695" alt="Snipaste_2025-07-14_16-36-59" src="https://github.com/user-attachments/assets/2d401f17-cae3-4edd-863b-cf51138ab9d4" />


<img width="1384" height="716" alt="Snipaste_2025-07-14_16-36-12" src="https://github.com/user-attachments/assets/441ed7b4-03fb-4e76-879a-f32281e6ad93" />

        
    

Matrix builds are a cornerstone of advanced CI/CD, allowing you to create highly robust and efficient testing pipelines.

* * *
