# About

Repo to store useful reusable workflows for github actions.


# Azure Function Sample

To use this repo you must configure an environment on your repo.</br>
On the environments that you create, don't forget about changing inputs and jobs sections, you must create an environment secret which content is the publish profile that you can get from Azure.

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
    uses: pncolvr/dwemerware/workflows/deploy/azure-function.yml
    with:
      environment: ${{ github.event_name == 'push' && (github.ref_name == 'staging' && 'pp' || 'dev') || github.event.inputs.environment }}
      function_app_name: <function app name>
      project_path: <function app path inside your repo>
      dotnet_version: 8.0.x
      github_nuget_source: <URL to nuget package index json>
    secrets:
      publish_profile: ${{ secrets.PUBLISH_PROFILE }}
      package_feed_token: ${{ secrets.GITHUB_TOKEN }}

```