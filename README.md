# sample-repository
name: Step 1, Create a branch

# This step listens for the learner to create branch `my-first-branch`
# This step sets STEP to 2
# This step closes <details id=1> and opens <details id=2>

# This will run every time we create a branch or tag
# Reference https://docs.github.com/en/actions/learn-github-actions/events-that-trigger-workflows
on:
  workflow_dispatch:
  create:

# Reference https://docs.github.com/en/actions/security-guides/automatic-token-authentication
permissions:
  # Need `contents: read` to checkout the repository
  # Need `contents: write` to update the step metadata
  contents: write

jobs:
  # Get the current step from .github/script/STEP so we can
  # limit running the main job when the learner is on the same step.
  get_current_step:
    name: Check current step number
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v3
      - id: get_step
        run: |
          echo "current_step=$(cat ./.github/script/STEP)" >> $GITHUB_OUTPUT
    outputs:
      current_step: ${{ steps.get_step.outputs.current_step }}

  on_create_a_branch:
    name: On create a branch
    needs: get_current_step

    # We will only run this action when:
    # 1. This repository isn't the template repository
    # 2. The STEP is currently 1
    # 3. The event is a branch
    # 4. The branch name is `my-first-branch`
    # Reference https://docs.github.com/en/actions/learn-github-actions/contexts
    # Reference https://docs.github.com/en/actions/learn-github-actions/expressions
    if: >-
      ${{ !github.event.repository.is_template
          && needs.get_current_step.outputs.current_step == 1
          && github.ref_type == 'branch'
          && github.ref_name == 'my-first-branch' }}

    # We'll run Ubuntu for performance instead of Mac or Windows
    runs-on: ubuntu-latest

    steps:
      # We'll need to check out the repository so that we can edit the README
      - name: Checkout
        uses: actions/checkout@v3
        with:
          fetch-depth: 0 # Let's get all the branches
name: Step 2, Commit a file

# This step listens for the learner to commit a file to branch `my-first-branch`
# This step sets STEP to 3
# This step closes <details id=2> and opens <details id=3>

# This action will run every time there's a push to `my-first-branch`
# Reference https://docs.github.com/en/actions/learn-github-actions/events-that-trigger-workflows
on:
  workflow_dispatch:
  push:
    branches:
      - my-first-branch

# Reference https://docs.github.com/en/actions/security-guides/automatic-token-authentication
permissions:
  # Need `contents: read` to checkout the repository
  # Need `contents: write` to update the step metadata
  contents: write

jobs:
  # Get the current step from .github/script/STEP so we can
  # limit running the main job when the learner is on the same step.
  get_current_step:
    name: Check current step number
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v3
      - id: get_step
        run: |
          echo "current_step=$(cat ./.github/script/STEP)" >> $GITHUB_OUTPUT
    outputs:
      current_step: ${{ steps.get_step.outputs.current_step }}

  on_commit_a_file:
    name: On commit a file
    needs: get_current_step

    # We will only run this action when:
    # 1. This repository isn't the template repository
    # 2. The STEP is currently 2
    # Reference https://docs.github.com/en/actions/learn-github-actions/contexts
    # Reference https://docs.github.com/en/actions/learn-github-actions/expressions
    if: >-
      ${{ !github.event.repository.is_template
          && needs.get_current_step.outputs.current_step == 2 }}

    # We'll run Ubuntu for performance instead of Mac or Windows
    runs-on: ubuntu-latest

    steps:
      # We'll need to check out the repository so that we can edit the README
      - name: Checkout
        uses: actions/checkout@v3
        with:
          fetch-depth: 0 # Let's get all the branches

      # Update README to close <details id=2> and open <details id=3>
      # and set STEP to '3'
      - name: Update to step 3
        uses: skills/action-update-step@v1
        with:
          token: ${{ secrets.GITHUB_TOKEN }}
          from_step: 2
          to_step: 3
          branch_name: my-first-branch
      # Update README to close <details id=1> and open <details id=2>
      # and set STEP to '2'
      - name: Update to step 2
        uses: skills/action-update-step@v1
        with:
          token: ${{ secrets.GITHUB_TOKEN }}
          from_step: 1
          to_step: 2
          branch_name: my-first-branch
