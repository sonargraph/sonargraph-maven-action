# Sonargraph Github Action for Maven-Based Java Projects

## Usage
This action will run  Sonargraph on your project to gather 100+ different software metrics 
on system and module levels. The metrics will then be uploaded to an instance of
[Sonargraph-Enterprise](https://www.hello2morrow.com/products/sonargraph/enterprise). By default
the [public instance](https://enterprise.hello2morrow.com) is used, i.e. everybody
will be able to see the metrics of your project. The cool thing about Sonargraph-Enterprise is
that is keeps the history of your metrics, so you will be able to track changes and visualize
them in charts.

Another positive is that it will allow you to display cool badges with metric values
in your README.md file (see [sonargraph-integration-access](https://github.com/sonargraph/sonargraph-integration-access)
as an example). Since Sonargraph-Enterprise store the metrics of your project in
a database it can make them accessible for badge creation.

Here is an example .yml file that makes use of this action:

```
name: CI

on:
  # Triggers the workflow on push or pull request events but only for the master branch
  push:
    branches: [ develop ]
  pull_request:
    branches: [ develop ]

  # Allows you to run this workflow manually from the Actions tab
  workflow_dispatch:

jobs:
  # This workflow contains a single job called "build"
  build:
    # The type of runner that the job will run on
    runs-on: ubuntu-latest

    # Steps represent a sequence of tasks that will be executed as part of the job
    steps:
      # Checks-out your repository under $GITHUB_WORKSPACE, so your job can access it
      - name: Checkout
        uses: actions/checkout@v3
      # Build and test
      - name: Build
        run: mvn clean package
      # Create Sonargraph XML report
      - name: Sonargraph Report and Upload
        uses: sonargraph/sonargraph-maven-action@v1.0
        with:
          org: hello2morrow GmbH

```


The action has three potential parameters, which are all optional:
- `host`: if you have your own instance of Sonargraph-Enterprise running you can specify the base URL of that instance here, e.g. ```https://enterprise.company.com```
- `org`: the name of your organization, reduces the chance of a project name clash.
- `activation_code`: specify only if you want to use your own license of Sonargraph-Build. The default license runs the free version of Sonargraph-Build.

Before you call that action make sure that your project has been built, i.e. class files are available.

## Creating Badges

Creating badges is very simple once your first set of metrics has been uploaded to Sonargraph-Enterprise.
Here is an example for the two metric badges you can see on [sonargraph-integration-access](https://github.com/sonargraph/sonargraph-integration-access):

```
![badge](https://img.shields.io/endpoint?url=https://enterprise.hello2morrow.com/badges/sonargraph-integration-access/Core:CoreMaintainabilityLevel?org=hello2morrow%20GmbH)
![badge](https://img.shields.io/endpoint?url=https://enterprise.hello2morrow.com/badges/sonargraph-integration-access/Core:CoreLinesOfCode)
```
The URL after ```/badges/``` contains the name of your project and then the key for the metric. Passing
the ```org``` parameter is optional, but recommended to avoid name clashes. The initial part for the badge 
URL is constant.

Here is an incomplete list of available metric keys. The documentation for all
available Sonargraph metrics can be found [here](https://eclipse.hello2morrow.com/doc/standalone/content/metric_definitions.html):

| Key | Description                                                                                                                                                  |
|---|--------------------------------------------------------------------------------------------------------------------------------------------------------------|
|Core:CoreLinesOfCode| Lines of Code                                                                                                                                                |
|Core:CoreRelativeCyclicityComponents| Relative Cyclicity Components (Java Files)                                                                                                                   |
|Core:CoreStatementsComplexMethodsPercent| Percentage of statements in complex methods                                                                                                                  |
|Core:CoreRelativeEntanglement| Relative entanglement                                                                                                                                        |
|Core:CoreMaintainabilityLevel| [Maintainability level](https://blog.hello2morrow.com/2018/12/a-promising-new-metric-to-track-maintainability/)                                              |
|Core:CoreEntangledLinesOfCodePercent| Percentage of statements in classes contributing to circular dependencies                                                                                    |
|Core:CorePropagationCost| [Propagation cost](https://www.researchgate.net/publication/261417217_Variations_on_Using_Propagation_Cost_to_Measure_Architecture_Modifiability_Properties) |
|JavaLanguageProvider:JavaBiggestPackageCycleGroup| Number of packages in biggest package cycle group                                                                                                            |
|Core:CoreRedundancy| Percentage of statements in duplicated code blocks (copy & paste programming)                                                                                |
|Core:CoreBiggestComponentCycleGroup| Number of Java files in biggest component cycle                                                                                                              |
|Core:CoreLinesOfCodeInLargeFilesPercent| Percentage of statements that are in large files (> 1000 LoC)                                                                                                |

Here is the complete list of available keys extracted from a JSON map. Check the [Sonargraph documentation](https://eclipse.hello2morrow.com/doc/standalone/content/metric_definitions.html) for the meaning of metrics.

```
  "Core:CoreLinesOfCodeInLargeFilesToFixPercent" : 0.2,
  "Core:CoreCriticalEntangledLinesOfCodeToFix" : 0,
  "Core:CoreCyclicComponents" : 64,
  "Core:CoreCriticalComponentCycleGroups" : 15,
  "Core:CoreLinesOfCodeIssueIgnoring" : 87418,
  "JavaLanguageProvider:JavaPackagesFullyAnalyzed" : 469,
  "Core:CoreAcd" : 213.02,
  "Core:CoreRelativeCyclicityComponents" : 0.35,
  "Core:CoreNumberOfAuthors90" : 4,
  "Core:CoreChanges30" : 577,
  "Core:CoreStatementsComplexMethodsToFix" : 0,
  "Core:CoreLinesOfCodeInLargeFiles" : 44122,
  "Core:CoreCriticalEntangledLinesOfCodeIgnored" : 7160,
  "Core:CoreComponentsIgnoringIssues" : 1159,
  "Core:CoreCriticalEntangledLinesOfCodePercentIgnored" : 1.21,
  "Core:CoreBiggestCycleLoc" : 1787,
  "Core:CoreStatementsComplexMethodsPercent" : 13.53,
  "Core:CoreSourceElementCount" : 378625,
  "Core:CoreAverageComplexity" : 6.03,
  "Core:CoreAverageNesting" : 1.77,
  "Core:CoreDuplicatesFixed" : 7,
  "Core:CoreSourcefilesTest" : 613,
  "Core:CoreRelativeEntanglement" : 0.18,
  "Core:CoreCodeChurnRate30" : 1.53,
  "Core:CoreLinesOfCodeInLargeFilesToFix" : 1157,
  "JavaLanguageProvider:JavaRelativeCyclicityPackages" : 0.0,
  "Core:CoreMaintainabilityLevel" : 89.23,
  "Core:CoreEntangledLinesOfCodePercent" : 1.21,
  "Core:CorePropagationCost" : 3.69,
  "Core:CoreEntangledLinesOfCodePercentIgnored" : 1.21,
  "Core:CoreDuplicates" : 92,
  "Core:CoreViolatingLinesOfCodePercent" : 0.0,
  "Core:CoreNccd" : 18.53,
  "JavaLanguageProvider:JavaStructuralDebtIndexPackages" : 0,
  "JavaLanguageProvider:JavaBiggestPackageCycleGroup" : 0,
  "Core:CoreLinesOfCodeFullyAnalyzed" : 592454,
  "Core:CoreCriticalEntangledLinesOfCode" : 7160,
  "JavaLanguageProvider:JavaIgnoredCyclicPackages" : 0,
  "Core:CoreEntangledLinesOfCodePercentToFix" : 0.0,
  "Core:CoreSourcefiles" : 6934,
  "Core:CoreSourcefilesFullyAnalyzed" : 5775,
  "Core:CoreComponents" : 6934,
  "Core:CoreComponentsInDeprecatedArtifacts" : 0,
  "Core:CoreCodeChurn30" : 12812,
  "Core:CoreChanges90" : 1252,
  "Core:CoreIgnoredCyclicComponents" : 64,
  "Core:CoreSourcefilesExcluded" : 0,
  "JavaLanguageProvider:JavaByteCodeInstructions" : 2096274,
  "Core:CoreIgnoredDuplicates" : 85,
  "Core:CoreElementsInDeprecatedArtifacts" : 0,
  "Core:CoreCriticalEntangledLinesOfCodePercentToFix" : 0.0,
  "Core:CoreRedundancy" : 1.83,
  "JavaLanguageProvider:JavaCriticalPackageCycleGroups" : 0,
  "Core:CoreCodeChurn365" : 202729,
  "Core:CoreStatementsComplexMethodsIgnoredPercent" : 0.0,
  "Core:CoreViolatingComponents" : 0,
  "JavaLanguageProvider:JavaPackages" : 478,
  "Core:CoreStructuralDebtIndexComponents" : 548,
  "Core:CoreStatementsComplexMethods" : 33547,
  "JavaLanguageProvider:JavaParserDependenciesToRemovePackages" : 0,
  "Core:CoreComponentDependenciesToRemoveComponents" : 40,
  "Core:CoreViolationsComponentDependencies" : 0,
  "Core:CoreMaxAcd" : 114.47,
  "Core:CoreChanges365" : 6713,
  "Core:CoreRedundancyFixed" : 0.28,
  "Core:CoreEntangledLinesOfCodeIgnored" : 7160,
  "Core:CoreEntangledLinesOfCode" : 7160,
  "Core:CoreCcd" : 1230211,
  "Core:CoreLinesOfCodeInLargeFilesPercent" : 7.45,
  "Core:CoreCommentLines" : 38438,
  "Core:CoreUnassignedLinesOfCodePercent" : 0.0,
  "Core:CoreStatementsFullyAnalyzed" : 247878,
  "Core:CoreStatementsComplexMethodsIgnored" : 0,
  "JavaLanguageProvider:JavaCyclicityPackages" : 0,
  "Core:CoreIssueDensity" : 0.24,
  "JavaLanguageProvider:JavaComponentDependenciesToRemovePackages" : 0,
  "Core:CoreComponentsFullyAnalyzed" : 5775,
  "Core:CoreIgnoredViolationsParserDependencies" : 0,
  "Core:CoreCyclicityComponents" : 418,
  "Core:CoreCodeChurn90" : 33827,
  "Core:CoreCodeChurnRate365" : 24.2,
  "Core:CoreDuplicatedLines" : 8248,
  "Core:CoreStatementsComplexMethodsToFixPercent" : 0.0,
  "JavaLanguageProvider:JavaCyclicPackages" : 0,
  "Core:CoreNumberOfAuthors30" : 2,
  "Core:CoreComponentCycleGroups" : 15,
  "Core:CoreUnassignedLogicalElements" : 0,
  "Core:CoreArtifactCount" : 138,
  "Core:CoreLinesOfCode" : 679872,
  "Core:CoreViolationDensityLinesOfCode" : 0.0,
  "Core:CoreNumberOfAuthors" : 4,
  "JavaLanguageProvider:JavaPackageCycleGroups" : 0,
  "Core:CoreCodeChurnRate90" : 4.04,
  "Core:CoreBiggestComponentCycleGroup" : 14,
  "Core:CoreUnassignedComponents" : 0,
  "Core:CoreCyclicModules" : 0,
  "Core:CoreEntangledLinesOfCodeToFix" : 0,
  "Core:CoreModules" : 40,
  "Core:CoreSourcefilesIssueIgnore" : 1159,
  "Core:CoreTypes" : 10759,
  "Core:CoreLinesOfCodeInLargeFilesIgnored" : 37792,
  "Core:CoreParserDependenciesToRemoveComponents" : 148,
  "Core:CoreStatements" : 284747,
  "Core:CoreTotalLines" : 837752,
  "Core:CoreEmptyArtifactCount" : 0,
  "Core:CoreCriticalEntangledLinesOfCodePercent" : 1.21,
  "Core:CoreCodeCommentLines" : 32620,
  "Core:CoreViolationsParserDependencies" : 0,
  "Core:CoreLinesOfCodeInLargeFilesIgnoredPercent" : 6.38,
  "Core:CoreViolationDensitySourceElements" : 0.0,
  "Core:CoreRedundancyIgnored" : 1.59
```

## Potential Issues

This action uses the action ```whelk-io/maven-settings-xml-action@v20``` to create 
a ```settings.xml``` file in the Maven repository. We add ```https://maven.hello2morrow.com/repository``` as a plugin
repository. If you are using that action too you might have to modify this action.