# ============================================================
# GitHub Actions workflow: Snake Animation + Trophy Generator
# Owner: Kartik Saini (ksaini-web)
#
# WHERE TO PUT THIS FILE:
#   In your PROFILE repo (must be named exactly "ksaini-web",
#   same as your username), create this file at:
#   .github/workflows/profile-widgets.yml
#
# ONE-TIME SETUP:
#   1. Go to github.com/ksaini-web/ksaini-web
#   2. Settings → Actions → General → under "Actions permissions"
#      select "Allow all actions and reusable workflows" → Save.
#   3. Settings → Actions → General → under "Workflow permissions"
#      select "Read and write permissions" → Save.
#      (Required so the Action can push the generated SVGs back.)
#   4. Create the file .github/workflows/profile-widgets.yml in
#      that repo and paste this entire content into it.
#   5. Commit directly to the "main" branch.
#   6. Go to the "Actions" tab → you should see this workflow
#      listed → click "Run workflow" to trigger it manually the
#      first time (don't wait for the schedule).
#   7. After it finishes (~1 min), two new files will appear:
#        - dist/github-contribution-grid-snake-dark.svg
#        - dist/trophy.svg
#      on a branch called "output" (auto-created by the snake action)
#      and directly on "main" for the trophy file.
#   8. In your README.md, point the <img> tags to:
#        Snake:
#        https://raw.githubusercontent.com/ksaini-web/ksaini-web/output/github-contribution-grid-snake-dark.svg
#        Trophy:
#        https://raw.githubusercontent.com/ksaini-web/ksaini-web/main/dist/trophy.svg
#      (These are the same paths already used in your README.md —
#      no changes needed there once this workflow has run once.)
#
# NOTE: Replace "ksaini-web" everywhere below if you ever rename
# your GitHub account.
# ============================================================

name: Generate Profile Widgets

on:
  schedule:
    - cron: "0 0 * * *"   # runs once a day at midnight UTC
  workflow_dispatch: {}     # lets you trigger it manually from the Actions tab
  push:
    branches:
      - main

permissions:
  contents: write

jobs:
  generate-snake:
    runs-on: ubuntu-latest
    steps:
      - name: Generate contribution snake SVG
        uses: Platane/snk@v3
        with:
          github_user_name: ksaini-web
          outputs: |
            dist/github-contribution-grid-snake.svg
            dist/github-contribution-grid-snake-dark.svg?palette=github-dark

      - name: Push snake SVG to "output" branch
        uses: crazy-max/ghaction-github-pages@v4
        with:
          target_branch: output
          build_dir: dist
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}

  generate-trophy:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout repo
        uses: actions/checkout@v4

      - name: Generate trophy SVG
        uses: Erik-Donath/github-profile-trophy@feature/generate-svg
        with:
          username: ksaini-web
          output_path: dist/trophy.svg
          theme: algolia
          no-frame: true
          row: 1
          column: 7
          token: ${{ secrets.GITHUB_TOKEN }}

      - name: Commit trophy SVG to main
        run: |
          git config user.name "github-actions[bot]"
          git config user.email "github-actions[bot]@users.noreply.github.com"
          git add dist/trophy.svg
          git diff --staged --quiet || git commit -m "chore: update trophy svg [skip ci]"
          git push
