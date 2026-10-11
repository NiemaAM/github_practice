# Practice for Github Session 2

This project is a simple web calculator, use it to practice Github collaboration, testing and actions.

- front-end: [view.html](src/view.html)
- controler: [calculator.js](src/calculator.js)
- unit tests: [claculator.test.js](test/calculator.test.js)
- actions template: [CI_CD_STARTED.txt](CI_CD_STARTED.txt)

# Practice 1: GitHub Collaboration
## Part 1: Add Collaborators

1. Create a **GitHub repository** for the calculator project.
2. **Invite your teammates** as repository collaborators.
3. Ask each collaborator to **clone the repository**.
4. **Verify** that everyone can **access** the project.

## Part 2: Protect Main Branch

1. Open your repository's **Settings**.
2. Navigate to **Rules** → **Rulesets**
3. Create a rule for the **main branch**.
4. **Require pull requests** before merging changes.
5. **Require approvals** and passing status checks before merging.

## Part 3: Open a Pull Requests

1. Create a **new branch**:
```bash
git checkout -b feature
```
2. Make a **change**: add you name in the [view.html](src/view.html) file.
3. **Stage** and **commit** your changes:
```bash
   git add .
   git commit -m "UI: added name to view"
```
4. **Push** your branch:
```bash
   git push -u origin feature
```
5. Open a **pull request** on GitHub: Describe the change and explain how you tested it.

## Part 4: Review the Pull Request
1. Assign another student to **review your pull request**.
2. **Review** the code changes.
3. Leave a **comment** or suggest an improvement.
4. **Approve** the pull request.
5. **Merge the pull request** after the required checks pass.

## Part 6: Resolve Conflicts
1. Modify the **same line** in separate branches.
2. **Merge the first** branch.
```bash
git checkout main
git pull origin main
git checkout feature/my-change
git merge main
```
3. **Update the second branch** with the latest changes from main.
4. **Resolve the conflict** in your editor.
5. **Commit** the resolution and complete the pull request.
```bash
git add .
git commit -m "fix: resolve merge conflict"
git push
```

# Practice 2: GitHub Automation
## Part 1: Unit testing
### 1. create a package.json file:

```bash
npm init -y
```

you should see:
```bash
Wrote to github_practice\package.json:

{
  "name": "github_practice",
  "version": "1.0.0",
  "description": "",
  "main": "calculator.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "keywords": [],
  "author": "",
  "license": "ISC",
  "type": "commonjs"
}
```

### 2. Update the package.json file:

change to **"scripts": {"test": "jest"},**
```bash
{
  "name": "github_practice",
  "version": "1.0.0",
  "description": "",
  "main": "calculator.js",
  "scripts": {
    "test": "jest"
  },
  "keywords": [],
  "author": "",
  "license": "ISC",
  "type": "commonjs"
}
```

### 3. install jest:

```bash
npm install --save-dev jest
```

you should see:
```bash
added 278 packages, and audited 279 packages in 25s
```

### 4. run unit tests:
```bash
npm test
```

you should see:
```bash
> github_practice@1.0.0 test
> jest

 PASS  ./calculator.test.js
  Calculator - Addition
    √ adds two positive numbers (2 ms)
    √ adds a negative number (1 ms)
    √ adds decimal numbers (1 ms)
  Calculator - Subtraction
    √ subtracts two numbers (1 ms)
    √ returns a negative result (1 ms)
  Calculator - Multiplication
    √ multiplies two numbers (1 ms)
    √ multiplying by zero returns zero (1 ms)
    √ multiplies decimal numbers (1 ms)
  Calculator - Division
    √ divides two numbers (1 ms)
    √ divides decimal numbers (1 ms)
    √ throws an error when dividing by zero (5 ms)

Test Suites: 1 passed, 1 total
Tests:       11 passed, 11 total
Snapshots:   0 total
Time:        0.793 s
Ran all test suites.
```
## Part 2: Github Actions
1. Create a CI_CD.yml file:
``` text
github_practice/
├── .github/
    └── workflows/
        └── CI_CD.yml
```
your repo should be looking like this:
``` text
github_practice/
├── .github/
│   └── workflows/
│       └── CI_CD.yml
├── src/
│   ├── view.html
│   └── calculator.js
├── test/
│   └── calculator.test.js
├── package.json
├── package-lock.json
└── README.md
```

2. use the [CI_CD_STARTED.txt](CI_CD_STARTED.txt) as a template

## Part 3: Continus Integration

1. create a [CI_CD.yaml](.github/wordkflow/CI_CD.yml) file:
``` yaml
on:
  # Run tests on pushes and pull requests
  push:
    branches:
      - main

  pull_request:
    branches:
      - main

  # Allow manual execution
  workflow_dispatch:

permissions:
  contents: read

jobs:
  # Continuous Integration
  test:
    name: Run Unit Tests
    runs-on: ubuntu-latest

    steps:
      - name: Checkout repository
        uses: actions/checkout@v4

      - name: Set up Node.js
        uses: actions/setup-node@v4
        with:
          node-version: 22
          cache: npm

      - name: Install dependencies
        run: npm ci

      - name: Run Jest tests
        run: npm test
```
2. Make sure the calculator works locally.
3. commit and push you code
4. observe the CI running on Github
5. Confirm that the CI workflow passes.

## Part 4: Continus Deployement
1. in you [CI_CD.yaml](.github/wordkflow/CI_CD.yml) file add at the end of the file:
```yml
  # Continuous Deployment
  deploy:
    name: Deploy to GitHub Pages
    needs: test

    # Deploy only after a push to main or manual run.
    if: github.event_name == 'push' || github.event_name == 'workflow_dispatch'

    runs-on: ubuntu-latest

    permissions:
      contents: read
      pages: write
      id-token: write

    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}

    steps:
      - name: Checkout repository
        uses: actions/checkout@v4

      - name: Configure GitHub Pages
        uses: actions/configure-pages@v5

      - name: Prepare website files
        run: |
          mkdir public
          cp src/view.html public/index.html
          cp src/calculator.js public/calculator.js

      - name: Upload website artifact
        uses: actions/upload-pages-artifact@v3
        with:
          path: public

      - name: Deploy to GitHub Pages
        id: deployment
        uses: actions/deploy-pages@v4
```
2. Enable GitHub Pages in the repository settings.
3. commit and push you code
4. observe the CD running on Github
5. Deploy the HTML, CSS, and browser-side JavaScript files (vercel).
6. Open the published website (vercel) and test the calculator.