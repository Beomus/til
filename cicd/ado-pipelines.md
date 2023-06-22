# ADO pipelines

Taking from [ADO key concepts](https://learn.microsoft.com/en-us/azure/devops/pipelines/get-started/key-pipelines-concepts?view=azure-devops)

> - A trigger tells a Pipeline to run.
> - A pipeline is made up of one or more stages. A pipeline can deploy to one or
>   more environments.
> - A stage is a way of organizing jobs in a pipeline and each stage can have
>   one or more jobs.
> - Each job runs on one agent. A job can also be agentless.
> - Each agent runs a job that contains one or more steps.
> - A step can be a task or script and is the smallest building block of a
>   pipeline.
> - A task is a pre-packaged script that performs an action, such as invoking a
>   REST API or publishing a build artifact.
> - An artifact is a collection of files or packages published by a run.

Example:

```yaml
# https://learn.microsoft.com/en-us/azure/devops/pipelines/repos/azure-repos-git?view=azure-devops&tabs=yaml#opting-out-of-ci
# trigger is set to none here because the script is bundled together with other
# scripts and we don't want it to be triggered by unrelated merges
trigger: none


schedules:
# https://crontab.guru/
# https://learn.microsoft.com/en-us/azure/devops/pipelines/process/scheduled-triggers?view=azure-devops&tabs=yaml#scheduled-triggers
- cron: '0 0 1 * *'
  displayName: At 00:00 UTC on day-of-month 1.
  branches:
    include:
    - master
  always: true

variables: # setting high level variables to be referenced later
  # this is reference to a variable group name which needs to be matching
  # this group can be created/found under Pipelines/Library
  - group: PATs 
  - name: vmImageName
    value: ubuntu-latest

# https://learn.microsoft.com/en-us/azure/devops/pipelines/process/stages?
stages: # not necessarily required for simple cases
- stage: A # reference stage name
  displayName: Stage A # nicely displayed name
  jobs:
  - job: A1 # reference job name
    displayName: Job A1 # nicely displayed name
    
    # https://learn.microsoft.com/en-us/azure/devops/pipelines/agents/agents?
    # here since it's a simple script, we're using ootb pool
    pool: # this pool is defined at a job level -> all jobs here will use it
      vmImage: $(vmImageName)
    steps:
      # tasks should be referenced from a list of available tasks:
      # https://learn.microsoft.com/en-us/azure/devops/pipelines/process/tasks?


      # https://learn.microsoft.com/en-us/azure/devops/pipelines/tasks/reference/cmd-line-v2?view=azure-pipelines
      - task: CmdLine@2
        env:
          # this needs to match whatever variable defined in the var group
          AZURE_DEVOPS_PAT: $(place.holder.value)
        displayName: Install dependencies and run script
        inputs:
          script: |
            python -m pip install --upgrade pip setuptools wheel
            pip install -r requirements.txt
            python my_script.py

      # https://learn.microsoft.com/en-us/azure/devops/pipelines/tasks/reference/publish-pipeline-artifact-v1?view=azure-pipelines
      - task: PublishPipelineArtifact@1
        inputs:
          targetPath: $(Build.SourcesDirectory)/file.extension
          artifactName: give-it-a-nice-name
          publishLocation: pipeline
```
