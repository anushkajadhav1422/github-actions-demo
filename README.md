# W3 L1 - CI/CD Fundamentals & GitHub Actions Workflow

## 📌 Task Overview

The objective of this task is to understand CI/CD fundamentals and create a GitHub Actions workflow that automatically builds and tests the application whenever code is pushed to the `main` branch.

### Subtasks

1. Understand CI/CD concepts
2. Create a GitHub Actions workflow
3. Trigger workflow on push to `main`
4. Checkout source code
5. Install dependencies
6. Run unit tests
7. Understand workflow, job, step, runner and artifact
8. Optional: Build and push Docker image to GHCR

---

# 1. Create Project Directory

Go to home directory:

```bash
cd ~
```

Create project directory:

```bash
mkdir devops-task-W3L1
```

Enter the directory:

```bash
cd devops-task-W3L1
```

Check current directory:

```bash
pwd
```

Expected:

```text
/home/<username>/devops-task-W3L1
```

---

# 2. Initialize Git Repository

Initialize Git:

```bash
git init
```

Check status:

```bash
git status
```

---

# 3. Create Node.js Application

Create `package.json`:

```bash
nano package.json
```

Add:

```json
{
  "name": "github-actions-demo",
  "version": "1.0.0",
  "description": "GitHub Actions CI demo",
  "main": "app.js",
  "scripts": {
    "test": "jest"
  },
  "devDependencies": {
    "jest": "^30.0.0"
  }
}
```

Save the file.

---

# 4. Create Application File

Create:

```bash
nano app.js
```

Add:

```javascript
function add(a, b) {
  return a + b;
}

module.exports = add;
```

This simple function will be tested by Jest.

---

# 5. Create Test Directory

Create the test directory:

```bash
mkdir test
```

Create test file:

```bash
nano test/app.test.js
```

Add:

```javascript
const add = require("../app");

test("2 + 3 should equal 5", () => {
  expect(add(2, 3)).toBe(5);
});
```

---

# 6. Install Dependencies

Run:

```bash
npm install
```

This creates:

```text
node_modules/
package-lock.json
```

---

# 7. Run Test Locally

Run:

```bash
npm test
```

Expected result:

```text
PASS  test/app.test.js

✓ 2 + 3 should equal 5
```

This confirms that the application test is working before using GitHub Actions.

---

# 8. Create .gitignore

Create:

```bash
nano .gitignore
```

Add:

```text
node_modules/
.env
```

We do not push `node_modules` to GitHub because dependencies can be installed using `npm install`.

---

# 9. Create GitHub Actions Workflow Directory

Create the required directories:

```bash
mkdir -p .github/workflows
```

Check:

```bash
ls -la .github/workflows
```

---

# 10. Create CI Workflow

Create:

```bash
nano .github/workflows/ci.yml
```

Add:

```yaml
name: CI Pipeline

on:
  push:
    branches:
      - main

jobs:
  test:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: 20

      - name: Install dependencies
        run: npm install

      - name: Run tests
        run: npm test
```

---

# 11. Understand the Workflow

The workflow performs:

```text
Developer pushes code
        ↓
GitHub Actions starts
        ↓
Checkout code
        ↓
Setup Node.js
        ↓
Install dependencies
        ↓
Run tests
        ↓
Success / Failure
```

---

# 12. GitHub Actions Concepts

## Workflow

A YAML file that defines an automated process.

Example:

```text
.github/workflows/ci.yml
```

## Job

A collection of steps that run together.

Example:

```yaml
jobs:
  test:
```

## Step

An individual task inside a job.

Examples:

```text
Checkout code
Setup Node.js
Install dependencies
Run tests
```

## Runner

The virtual machine where the GitHub Actions job executes.

Example:

```yaml
runs-on: ubuntu-latest
```

## Artifact

A file generated during a workflow that can be stored and downloaded from GitHub Actions.

Artifacts are useful for build output, reports, logs, etc.

---

# 13. Add Files to Git

Check status:

```bash
git status
```

Add files:

```bash
git add .
```

Commit:

```bash
git commit -m "Add GitHub Actions CI pipeline"
```

---

# 14. Connect GitHub Repository

Create a new repository on GitHub.

Example repository name:

```text
github-actions-demo
```

Add remote:

```bash
git remote add origin https://github.com/<github-username>/github-actions-demo.git
```

Check:

```bash
git remote -v
```

---

# 15. Push Code to Main

Rename branch:

```bash
git branch -M main
```

Push:

```bash
git push -u origin main
```

---

# 16. Check GitHub Actions

Open your GitHub repository.

Go to:

```text
Actions
```

You should see:

```text
CI Pipeline
```

Open the workflow.

You should see:

```text
✓ Checkout code
✓ Setup Node.js
✓ Install dependencies
✓ Run tests
```

A green check mark means the CI pipeline completed successfully.

---

# 17. Test CI Automatically

Make a small change:

```bash
echo "// CI test" >> app.js
```

Then:

```bash
git add .
git commit -m "Test CI pipeline"
git push
```

GitHub Actions will automatically start again because the workflow is configured for:

```yaml
on:
  push:
    branches:
      - main
```

---

# 18. Optional - Docker Image for GHCR

> Docker/GHCR is not a core requirement of W3 L1. This section is included as an additional demonstration.

Create a Dockerfile:

```bash
nano Dockerfile
```

Add:

```dockerfile
FROM node:20-alpine

WORKDIR /app

COPY package*.json ./

RUN npm install

COPY . .

CMD ["npm", "test"]
```

Build the image:

```bash
sudo docker build -t github-actions-demo:1.0 .
```

Check:

```bash
sudo docker images | grep github-actions-demo
```

---

# 19. Login to GHCR

Login:

```bash
sudo docker login ghcr.io
```

Use:

```text
Username: <github-username>
Password: <github-personal-access-token>
```

Expected:

```text
Login Succeeded
```

Never commit your GitHub token to the repository.

---

# 20. Tag Docker Image for GHCR

Format:

```bash
sudo docker tag github-actions-demo:1.0 ghcr.io/<github-username>/github-actions-demo:1.0
```

Example:

```bash
sudo docker tag github-actions-demo:1.0 ghcr.io/anushkajadhav/github-actions-demo:1.0
```

---

# 21. Push Docker Image to GHCR

```bash
sudo docker push ghcr.io/<github-username>/github-actions-demo:1.0
```

Example:

```bash
sudo docker push ghcr.io/anushkajadhav/github-actions-demo:1.0
```

After successful push, the image will be available under your GitHub Packages.

---

# 22. Complete W3 L1 CI/CD Flow

```text
Create Node.js Project
        ↓
Create Unit Test
        ↓
Run Test Locally
        ↓
Initialize Git
        ↓
Create GitHub Repository
        ↓
Push Code to main
        ↓
GitHub Actions Triggered
        ↓
Checkout Code
        ↓
Setup Node.js
        ↓
Install Dependencies
        ↓
Run Unit Tests
        ↓
✓ Pipeline Successful
```

Optional Docker flow:

```text
Application
     ↓
Docker Build
     ↓
Docker Image
     ↓
Tag Image
     ↓
Login to GHCR
     ↓
Push Image
     ↓
GitHub Container Registry
```

---

# 23. Important Commands

### Create directory

```bash
mkdir devops-task-W3L1
```

### Enter directory

```bash
cd devops-task-W3L1
```

### Initialize Git

```bash
git init
```

### Install dependencies

```bash
npm install
```

### Run tests

```bash
npm test
```

### Create GitHub Actions directory

```bash
mkdir -p .github/workflows
```

### Git commands

```bash
git add .
git commit -m "Add CI pipeline"
git push
```

### Docker build

```bash
sudo docker build -t github-actions-demo:1.0 .
```

### GHCR login

```bash
sudo docker login ghcr.io
```

### GHCR tag

```bash
sudo docker tag github-actions-demo:1.0 ghcr.io/<github-username>/github-actions-demo:1.0
```

### GHCR push

```bash
sudo docker push ghcr.io/<github-username>/github-actions-demo:1.0
```

---

# 24. What I Learned

### CI

Continuous Integration automatically validates code whenever changes are pushed.

### CD

Continuous Delivery/Deployment automates the process of delivering or deploying validated code.

### GitHub Actions

GitHub Actions provides automation for building, testing and deploying applications.

### Workflow

Defines the complete automation process using a YAML file.

### Job

A group of related steps.

### Step

An individual action performed by a job.

### Runner

The machine that executes the workflow.

### Artifact

A file produced by a workflow that can be stored for later use.

---


created a Node.js project with a simple unit test and configured GitHub Actions for Continuous Integration.

The workflow is triggered whenever code is pushed to the `main` branch.

It checks out the source code, sets up Node.js, installs dependencies and runs the unit tests.

The workflow runs on an Ubuntu runner.

I also understand the concepts of workflow, job, step, runner and artifact.

As an additional Docker demonstration, I built the application into a Docker image and pushed it to GitHub Container Registry.

---


## Demo 1

Show:

```text
.github/workflows/ci.yml
```

Explain:

```text
push → checkout → install → test
```

## Demo 2

Open:

```text
GitHub → Actions
```

Show the green successful workflow.

## Demo 3

Open the workflow and show:

```text
ubuntu-latest
checkout
Node.js
npm install
npm test
```

## Optional Demo 4

Show GHCR:

```text
GitHub → Packages
```

and show the Docker image.

---

# ⭐ Final Result

The W3 L1 task demonstrates a basic CI pipeline where GitHub automatically validates the application after code is pushed to the `main` branch.

```text
Developer
   ↓
Git Push
   ↓
GitHub
   ↓
GitHub Actions
   ↓
Build / Install
   ↓
Unit Tests
   ↓
✓ Success
```
