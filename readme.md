# About

Repo to store useful reusable workflows for github actions.


# Azure Function Sample

To use this repo you must configure an environment on your repo.</br>
On the environments that you create, don't forget about changing inputs and jobs sections on the yml below, you must create an environment secret which content is the publish profile that you can get from Azure.

```yml
name: Deploy Azure Function

on:
  push:
    branches:
      - develop
      - staging
    paths:
      - <function app path inside your repo>/**
  workflow_dispatch:
    inputs:
      environment:
        description: Deployment Environment
        required: true
        default: dev
        type: choice
        options:
          - dev
          - pp
          - prd

jobs:
  deploy:
    uses: pncolvr/dwemerware/.github/workflows/azure-function.yml@master
    with:
      # this is to solve the case when code is pushed and environment is not set,
      # we assume that if branch name is staging we should deploy to pp environment
      # and every other branch name we should deploy to dev environment
      # when we use workflow dispatch, the environment input is set, we deploy to that environment
      environment: ${{ github.event_name == 'push' && (github.ref_name == 'staging' && 'pp' || 'dev') || github.event.inputs.environment }}
      function_app_name: <function app name>
      project_path: <function app path inside your repo>
      dotnet_version: 8.0.x
      github_nuget_source: <URL to nuget package index json>
    secrets:
      publish_profile: ${{ secrets.PUBLISH_PROFILE }}
      package_feed_token: ${{ secrets.GITHUB_TOKEN }}

```