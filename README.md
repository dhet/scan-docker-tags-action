# Scan Docker Tags Action

Scan a Docker repository for recently-updated tags. This allows you to trigger builds whenever a given Docker image is updated. 


## Example

This example workflow scans a Docker repository every 15 minutes and prints all tags that were updated since the last run.

```yml
name: Scan Docker Repo
on:
  schedule:
    - cron: '*/15 * * * *'

jobs:
  scan:
    name: List Recent Updates
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v2
      - name: Scan Docker Repository
        uses: dhet/scan-docker-tags-action@v1.0.1
        with:
          image: mhart/alpine-node
          max-age-minutes: 15
          username: ${{ secrets.DOCKER_HUB_USER }}
          password: ${{ secrets.DOCKER_HUB_PASSWORD }}
        id: scan
      - name: Print
        run: "echo 'Recent updates: ${{ steps.scan.outputs.tags }}'"
```
**Output:**
```
Recent updates: slim-14.4,slim-14,slim,latest
```

## Required Inputs

|Parameter|Description
|---|---
|`image`|The image repository to scan (sans tag), e.g. "mhart/alpine-node"
|`max-age-minutes`|The maximum age of tags to consider. Older tags are discarded. E.g. `30` for 30 minutes
|`username`|The username used to authenticate against the Docker repository
|`password`|The password used to authenticate against the Docker repository


## Optional Inputs
|Parameter|Description|Default
|---|---|---
|`tag-regex`|A regular expression for filtering tags. Backslashes need to be escaped. E.g. `\\d+\\.\\d+$` matches `0.10`, `1.2`, `1.12.123`, etc.; `latest` matches only the "latest" tag.|`.*`
|`repo-url`|The URL of the Docker registry. Defaults to Dockerhub.|`https://hub.docker.com`


## Outputs
The only output this action exposes is the `tags` output. `tags` is a comma-separated list of all image tags that have 
changed in the specified time frame. The list is sorted by date in ascending order. E.g. `v1.0.7,v1.0.8,latest`.
