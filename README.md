name: Generate Contribution Snake

on:
  # Refresh twice a day
  schedule:
    - cron: "0 */12 * * *"
  # Allow manual runs from the Actions tab
  workflow_dispatch:
  # Regenerate when the profile README repo is updated
  push:
    branches:
      - main

# Needed so GITHUB_TOKEN can push the `output` branch
permissions:
  contents: write

concurrency:
  group: snake
  cancel-in-progress: true

jobs:
  generate:
    runs-on: ubuntu-latest
    timeout-minutes: 10
    steps:
      # 1) Build the SVGs into ./dist
      - name: Generate snake SVGs
        uses: Platane/snk/svg-only@v3
        with:
          github_user_name: git-user-007
          outputs: |
            dist/github-contribution-grid-snake.svg
            dist/github-contribution-grid-snake-dark.svg?color_snake=#00D9FF&color_dots=#161b22,#0e2f5c,#1457a8,#2f81f7,#58a6ff

      # 2) Publish ./dist to the `output` branch
      - name: Publish to output branch
        uses: crazy-max/ghaction-github-pages@v4
        with:
          target_branch: output
          build_dir: dist
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}

