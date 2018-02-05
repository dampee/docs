---
title: Deploy Release Step 
description: The Deploy Release Step allows you to trigger the deployment of a Release of a Project from another Project 
version: 2018.2 
---

The _Deploy Release_ step allows you to trigger the deployment of a release of a project from another project.  This is useful when you are [coordinating multiple Projects](index.md).

![Deploy Release Step Card](deploy-release-card.png "width=500")

When you add a _Deploy Release_ step to your deployment process, you are then able to select the project which will be deployed.

![Deploy Release Select Project](deploy-release-step-select-project.png "width=500")

You can add many _Deploy Release_ steps to your process, if you wish to deploy releases of many projects.

When creating a release of a project containing _Deploy Release_ steps you are able to select the release version of each project, similar to the way versions of packages are selected.  

## Conditional Deployment 

A _Deploy Release_ step can be configured to:

- Deploy Always (default)
- If the selected release is not the current release in the environment
- If the selected release has a higher version than the current release in the environment

## Variables

Variables can be passed to the deployment triggered by the _Deploy Release_ step. These will be made available to steps within the child deployment's process, just like regular [project variables](/docs/deployment-process/variables/index.md).  Variables passed in will override existing variables in the child project if the names collide.

![Deploy Release Variables](deploy-release-step-variables.png "width=500")

### Output Variables

You may wish to capture information from a deployment triggered by a _Deploy Release_ step, either to be used in the parent process or to be passed to another deployment via another _Deploy Release_ step.

Any [output variables](/docs/deployment-process/variables/output-variables.md) generated by a deployment will be captured as output variables on the _Deploy Release_ step which triggered the deployment.  These can then be used by subsequent steps in the process. 

These output variables are captured as variables with the following name pattern:

```
Octopus.Action[Deploy Release Step Name].Output.Deployment[Child Step Name].VariableName
```

and for [machine-specific output variables](/docs/deployment-process/variables/output-variables.md#Outputvariables-Outputfrommultiplemachines):

```
Octopus.Action[Deploy Release Step Name].Output.Deployment[Child Step Name][Machine Name].VariableName
```

Where:

*Deploy Release Step Name:* The name of the _Deploy Release_ step in the parent process.    
*Child Step Name:* The name of the step in the child deployment process which set the output variable.   
*VariableName:* The original name of the output variable. e.g. for `Set-OctopusVariable -Name "Foo" -Value "Bar"` this would be `Foo`.   
*Machine Name:* The machine the child process was targetting when the output variable was set.   

:::hint
For example, you have a project _Project Voltron_ which contains a _Deploy Release_ step named _Deploy Red Lion_ which triggers a deployment of another project _Project Red Lion_.  
_Project Red Lion_ contains a step _Echo Paladin_ which sets an output variable. e.g. 

```
Set-OctopusVariable -Name "Paladin" -Value "Lance"
```

This variable will be available in subsequent steps of the _Project Voltron_ process via the variable `Octopus.Action[Deploy Red Lion].Output.Deployment[Echo Paladin].Paladin`. 
:::

## Lifecycles

The Lifecycles of project's being deployed by a _Deploy Release_ step must be compatible with the coordinating project.

For example, if you have two projects, `Project A` and `Project B` which are referenced by _Deploy Release_ steps in another project `Project Alphabet`. When deploying `Project Alphabet` to the `Test` environment, the release versions chosen for `Project A` and `Project B` must be eligible to be deployed to the `Test` environment according to the lifecycles of those projects. 

## Multi-Tenant Deployments

When it is a [tenanted](/docs/deployment-patterns/multi-tenant-deployments/multi-tenant-deployment-guide/index.md) project being deployed by _Deploy Release_ step, then the parent project should also be created as tenanted.

When triggering a tenanted deployment of the parent project, the tenant will be used to trigger the child deployment.

If the child project is untenanted, and the parent project is deployed with a tenant selected, then the untenanted child project will simply be deployed, ignoring the tenant.

### Deploying a Combination of Tenanted and Untenanted Projects

A project can contain multiple _Deploy Release_ steps which deploy a combination of tenanted and untenanted projects. There are a number of approaches which can be used to control which _Deploy Release_ steps will be executed.   

- Scope the _Deploy Release_  step to one or more tenants.  This is useful if the child project should only be deployed for particular tenants.  
- If the child project is untenanted, and should only be deployed _once_ for all tenants, then the [Deployment Conditions](#conditional-deployment) can be used to specify that is should only be deployed if the version does not match.  This will prevent it from being deployed multiple times if multiple tenanted-deployments of the parent project are created. 