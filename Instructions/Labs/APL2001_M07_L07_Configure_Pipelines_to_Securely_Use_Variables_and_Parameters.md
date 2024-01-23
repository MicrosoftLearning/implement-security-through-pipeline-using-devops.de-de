---
lab:
  title: Konfigurieren von Pipelines für die sichere Verwendung von Variablen und Parametern
  module: 'Module 7: Configure pipelines to securely use variables and parameters'
---

# Konfigurieren von Pipelines für die sichere Verwendung von Variablen und Parametern

In diesem Lab lernen Sie, wie Sie Pipelines für die sichere Verwendung von Variablen und Parametern konfigurieren.

Diese Übung dauert ca. **20** Minuten.

## Vorbereitung

Sie benötigen ein Azure-Abonnement, eine Azure DevOps-Organisation und die eShopOnWeb-Anwendung, um den Labs zu folgen.

- Folgen Sie den Schritten, um Ihre [Lab-Umgebung zu überprüfen](APL2001_M00_Validate_Lab_Environment.md).

## Anweisungen

### Übung 1: Sicherstellen von Parameter- und Variablentypen

#### Übung 1: Importieren und Ausführen der CI-Pipeline

Importieren Sie zunächst die CI-Pipeline mit dem Namen [eshoponweb-ci.ymll](https://github.com/MicrosoftLearning/eShopOnWeb/blob/main/.ado/eshoponweb-ci.yml).

1. Navigieren Sie zum Azure DevOps-Portal unter `https://dev.azure.com` und öffnen Sie Ihre Organisation.

1. Öffnen Sie das eShopOnWeb-Projekt.

1. Navigieren Sie zu **Pipelines > Pipelines**.

1. Wählen Sie **Pipeline erstellen** aus.

1. Wählen Sie **Azure Repos Git (Yaml)**.

1. Wählen Sie das Repository **eShopOnWeb** aus.

1. Wählen Sie die Option **Vorhandene Azure Pipelines-YAML-Datei** aus.

1. Wählen Sie die Datei **/.ado/eshoponweb-ci.yml** und dann **Weiter** aus.

1. Klicken Sie auf die Schaltfläche **Ausführen**, um die Pipeline auszuführen.

   > [!NOTE]
   > Ihre Pipeline nimmt einen Namen auf der Grundlage des Projektnamens an. Benennen Sie sie um, um die Pipeline besser zu identifizieren.

1. Wechseln Sie zu **Pipelines > Pipelines** und wählen Sie die zuletzt erstellte Pipeline aus. Wählen Sie die Auslassungspunkte und die Option **Umbenennen/Verschieben** aus.

1. Nennen Sie sie **eshoponweb-ci-parameters**, und wählen Sie **Speichern** aus.

#### Aufgabe 2: Sicherstellen von Parametertypen für YAML-Pipelines

In dieser Aufgabe legen Sie Parameter- und Parametertypen für die Pipeline fest.

1. Wechseln Sie zu **Pipelines > Pipelines**, und wählen Sie die **eshoponweb-ci-parameters**-Pipeline aus.

1. Wählen Sie **Bearbeiten** aus.

1. Fügen Sie die folgenden Abschnitte „Parameter“ und „Ressourcen“ am Anfang der YAML-Datei hinzu:

   ```yaml
   parameters:
   - name: dotNetProjects
     type: string
     default: '**/*.sln'
   - name: testProjects
     type: string
     default: 'tests/UnitTests/*.csproj'

   resources:
     repositories:
     - repository: self
       trigger: none

   ```

1. Ersetzen Sie die hartcodierten Pfade in den Aufgaben `Restore`, `Build` und `Test` durch die soeben erstellten Parameter.

   - **Projekte ersetzen**: `**/*.sln` durch Projekte: ${{ parameters.dotNetProjects }} in den Aufgaben `Restore` und `Build`.
   - **Projekte ersetzen**: `tests/UnitTests/*.csproj` durch Projekte: ${{ parameters.testProjects }} in der Aufgabe `Test`.

   Die Aufgaben `Restore`, `Build` und `Test` im Abschnitt „Schritte“ der YAML-Datei sollten wie folgt aussehen:

   ```yaml
       steps:
       - task: DotNetCoreCLI@2
         displayName: Restore
         inputs:
           command: 'restore'
           projects: ${{ parameters.dotNetProjects }}
           feedsToUse: 'select'
   
       - task: DotNetCoreCLI@2
         displayName: Build
         inputs:
           command: 'build'
           projects: ${{ parameters.dotNetProjects }}
   
       - task: DotNetCoreCLI@2
         displayName: Test
         inputs:
           command: 'test'
           projects: ${{ parameters.testProjects }}

   ```

1. Speichern Sie die Pipeline, und führen Sie sie aus. Stellen Sie sicher, dass die Pipelineausführung erfolgreich abgeschlossen wird.

   ![Screenshot der Pipelineausführung mit Parametern.](media/pipeline-parameters-run.png)

#### Aufgabe 3: Variablen und Parameter sichern

In dieser Aufgabe sichern Sie die Variablen und Parameter aus Ihrer Pipeline mithilfe von Variablengruppen.

1. Wechseln Sie zu **Pipelines > Library**.

1. Wählen Sie die Schaltfläche **+ Variablengruppe** aus, um eine neue Variablengruppe mit dem Namen **BuildConfigurations** zu erstellen.

1. Fügen Sie eine Variable mit dem Namen **buildConfiguration** hinzu, und legen Sie ihren Wert fest auf `Release`.

1. Speichern Sie die Variablengruppe.

   ![Screenshot der Variablengruppe mit BuildConfigurations.](media/eshop-variable-group.png)

1. Wählen Sie die Schaltfläche **Pipelineberechtigungen** und dann die Schaltfläche **+** aus, um eine neue Pipeline hinzuzufügen.

1. Wählen Sie die Pipeline **eshoponweb-ci-parameters** aus, damit die Pipeline die Variablengruppe verwenden kann.

   ![Screenshot der Pipelineberechtigungen.](media/pipeline-permissions.png)

   > [!NOTE]
   > Sie können auch festlegen, dass bestimmte Benutzer*innen oder Gruppen die Variablengruppe bearbeiten können, indem Sie auf die Schaltfläche **Sicherheit** klicken.

1. Navigieren Sie zu **Pipelines > Pipelines**.

1. Öffnen Sie die Pipeline **eshoponweb-ci-parameters**, und wählen Sie **Bearbeiten** aus.

1. Verweisen Sie oben in der YML-Datei direkt unter den Parametern auf die Variablengruppe, indem Sie Folgendes hinzufügen:

   ```yaml
   variables:
     - group: BuildConfigurations
   ```

1. Ersetzen Sie in der Aufgabe „Erstellen“ den Befehl „Erstellen“ durch die folgenden Zeilen, um die Buildkonfiguration aus der Variablengruppe zu verwenden.

   ```yaml
           command: 'build'
           projects: ${{ parameters.dotNetProjects }}
           configuration: $(buildConfiguration)
   ```

1. Speichern Sie die Pipeline, und führen Sie sie aus. Mit folgender Buildkonfiguration sollte sie erfolgreich ausgeführt werden`Release`. Sie können dies überprüfen, indem Sie sich die Protokolle der Aufgabe „Erstellen“ ansehen.

> [!NOTE]
> Auf diese Weise können Sie Ihre Variablen und Parameter mithilfe von Variablengruppen sichern, ohne sie in YAML-Dateien hartcodieren zu müssen.

#### Aufgabe 4: Obligatorische Variablen und Parameter überprüfen

In dieser Aufgabe überprüfen Sie die obligatorischen Variablen, bevor die Pipeline ausgeführt wird.

1. Navigieren Sie zu **Pipelines > Pipelines**.

1. Öffnen Sie die Pipeline **eshoponweb-ci-parameters**, und wählen Sie **Bearbeiten** aus.

1. Fügen Sie im Abschnitt „Phasen“ am Anfang (nach der Zeile `stage:`) eine neue Phase mit dem Namen **Validate** hinzu, um die obligatorischen Variablen zu überprüfen, bevor die Pipeline ausgeführt wird.

   ```yaml
   - stage: Validate
     displayName: Validate mandatory variables
     jobs:
     - job: ValidateVariables
       pool:
         vmImage: ubuntu-latest
       steps:
       - script: |
           if [ -z "$(buildConfiguration)" ]; then
             echo "Error: buildConfiguration variable is not set"
             exit 1
           fi
         displayName: 'Validate Variables'
    ```

   > [!NOTE]
   > In dieser Phase wird ein Skript ausgeführt, um die buildConfiguration-Variable zu überprüfen. Wenn die Variable nicht festgelegt ist, schlägt das Skript fehl, und die Pipeline wird beendet.

1. Stellen Sie sicher, dass die **Build**-Phase von der **Validate**-Phase abhängig ist, indem Sie `dependsOn: Validate` am Anfang der **Build**-Phase hinzufügen:

   ```yaml
   - stage: Build
     displayName: Build .Net Core Solution
     dependsOn: Validate
      ```

1. Speichern Sie die Pipeline, und führen Sie sie aus. Sie wird erfolgreich ausgeführt, da die buildConfiguration-Variable in der Variablengruppe festgelegt ist.

1. Um die Überprüfung zu testen, entfernen Sie die buildConfiguration-Variable aus der Variablengruppe, oder löschen Sie die Variablengruppe, und führen Sie die Pipeline erneut aus. Bei folgendem Fehler sollte es fehlschlagen:

   ```yaml
   Error: buildConfiguration variable is not set   
   ```

   ![Screenshot der Pipelineausführung mit fehlerhafter Überprüfung.](media/pipeline-validation-fail.png)

1. Fügen Sie die Variablengruppe und die buildConfiguration-Variable wieder zur Variablengruppe hinzu, und führen Sie die Pipeline erneut aus. Es sollte erfolgreich ausgeführt werden.

## Überprüfung

In diesem Lab haben Sie gelernt, wie Sie Pipelines für die sichere Verwendung von Variablen und Parametern konfigurieren und wie Sie obligatorische Variablen und Parameter überprüfen.
