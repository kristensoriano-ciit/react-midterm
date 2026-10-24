# React CI/CD Midterm Project

## Project Overview

This project is a React application created for the Midterm Assessment about Continuous Integration and Continuous Delivery using Jenkins and Docker.

The application is a simple calculator that supports addition and subtraction. Jenkins is used to automatically test and build the application, while Docker is used to package and deploy it to different environments.

The project uses two Jenkins jobs:

- `React-CI` - automatically runs tests, builds the React application, and creates a release artifact.
- `React-Deploy` - manually deploys a selected successful CI artifact to a chosen environment.

## Technologies Used

- React
- Vite
- Vitest
- Node.js
- Docker
- Nginx
- Jenkins
- GitHub

## Application Features

The calculator includes:

- Addition
- Subtraction
- Numeric input handling
- Automated tests
- Application version display

Final application version:

```text
2.0
```

## Setup Instructions

### 1. Clone the Repository

```bash
git clone https://github.com/kristensoriano-ciit/react-midterm.git
cd react-midterm
```

### 2. Install Dependencies

Make sure Node.js and npm are installed.

Run:

```bash
npm install
```

### 3. Run the Automated Tests

```bash
npm test
```

The project should show at least five passing tests.

### 4. Run the Application Locally

```bash
npm run dev
```

Open the local Vite URL shown in the terminal.

### 5. Build the Production Version

```bash
npm run build
```

This creates the `dist` folder containing the production build.

### 6. Build the Docker Image

Make sure Docker Desktop is running.

Run:

```bash
docker build -t react-midterm .
```

## Project Structure

```text
react-midterm/
├── src/
│   ├── App.jsx
│   ├── calculator.js
│   └── calculator.test.js
├── Dockerfile
├── Dockerfile.deploy
├── .dockerignore
├── package.json
├── package-lock.json
└── README.md
```

## Automated Tests

The project uses Vitest for testing.

The calculator has at least five tests covering addition and subtraction.

Run:

```bash
npm test
```

A successful test run should show:

```text
5 passed
```

## Docker Setup

The main `Dockerfile` uses a multi-stage build.

The first stage uses Node.js to install dependencies, run tests, and build the React application.

The second stage uses Nginx to serve the production files.

```dockerfile
FROM node:lts-alpine AS builder

WORKDIR /app

COPY package*.json ./
RUN npm ci

COPY . .

RUN npm test
RUN npm run build

FROM nginx:stable-alpine AS production

COPY --from=builder /app/dist/ /usr/share/nginx/html/

EXPOSE 80

CMD ["nginx", "-g", "daemon off;"]
```

## Deployment Dockerfile

The deployment job uses `Dockerfile.deploy`.

This Dockerfile does not rebuild the React application. It only serves the production files that were already created by the CI job.

```dockerfile
FROM nginx:stable-alpine

COPY site/ /usr/share/nginx/html/

EXPOSE 80

CMD ["nginx", "-g", "daemon off;"]
```

## Jenkins CI Setup

Jenkins job name:

```text
React-CI
```

The CI job is connected to the GitHub repository and monitors the `main` branch.

Branch:

```text
*/main
```

Poll SCM schedule:

```text
* * * * *
```

The CI job does the following:

1. Detects repository changes.
2. Gets the latest source code.
3. Builds the Docker image.
4. Installs dependencies.
5. Runs automated tests.
6. Builds the React application.
7. Creates `release.json`.
8. Creates a release artifact.
9. Archives the artifact in Jenkins.

Artifact format:

```text
react-app-BUILD_NUMBER.tar.gz
```

Example:

```text
react-app-7.tar.gz
```

## Release Metadata

Each successful CI build includes a `release.json` file.

Example:

```json
{
  "ci_build": "7",
  "git_commit": "938e6589b85f09277e0ab79880bfb7384e0658da"
}
```

This shows which CI build and Git commit are being used by the deployed application.

## Jenkins Deployment Setup

Jenkins job name:

```text
React-Deploy
```

The deployment job uses two parameters.

### TARGET_ENVIRONMENT

Available choices:

```text
dev
test
acceptance
production
```

### CI_BUILD_NUMBER

This is the successful `React-CI` build number that will be deployed.

Example:

```text
7
```

The deployment job retrieves the existing CI artifact and deploys it without rebuilding React.

## Deployment Environments

| Environment | Port | Final CI Build | App Version |
|---|---:|---:|---:|
| dev | 8081 | 7 | 2.0 |
| test | 8082 | 7 | 2.0 |
| acceptance | 8083 | 7 | 2.0 |
| production | 8084 | 7 | 2.0 |

Application URLs:

```text
Dev:        http://localhost:8081
Test:       http://localhost:8082
Acceptance: http://localhost:8083
Production: http://localhost:8084
```

Release metadata URLs:

```text
http://localhost:8081/release.json
http://localhost:8082/release.json
http://localhost:8083/release.json
http://localhost:8084/release.json
```

## Checking Running Containers

Run:

```bash
docker ps
```

The following containers should be running:

```text
react-dev
react-test
react-acceptance
react-production
```

## CI Failure Testing

The project also demonstrates CI failure handling.

The addition function was temporarily changed to an incorrect implementation.

Jenkins automatically detected the Git change and ran the CI job.

Because the tests failed:

- The CI build failed.
- A release artifact was not created.
- The failed CI build could not be deployed.
- Previously deployed applications continued running.

After fixing the function, Jenkins automatically ran the CI job again and created a successful release.

## Final Release Process

The final release is version `2.0`.

The final process was:

1. Add the subtraction function.
2. Add at least two subtraction tests.
3. Verify that at least five tests pass.
4. Update the application version to `2.0`.
5. Commit and push the changes.
6. Jenkins automatically runs the CI job.
7. Jenkins creates the version 2.0 artifact.
8. Deploy the new artifact to `dev`.
9. Verify that `dev` is on version `2.0` while the other environments still use the earlier version.
10. Promote the same CI artifact to `test`, `acceptance`, and `production`.

The same final CI build was used for all four environments.

## Author

Kristen Soriano
Masangkay Christian