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

#### Aufgabe 1: (überspringen, wenn erledigt) Importieren und Ausführen der CI-Pipeline

Beginnen wir mit dem Importieren der CI-Pipeline mit dem Namen [eshoponweb-ci.yml](https://github.com/MicrosoftLearning/eShopOnWeb/blob/main/.ado/eshoponweb-ci.yml).

1. Navigieren Sie zum Azure DevOps-Portal unter `https://aex.dev.azure.com` und öffnen Sie Ihre Organisation.

1. Öffnen Sie das Projekt **eShopOnWeb** in Azure DevOps.

1. Navigieren Sie zu **Pipelines > Pipelines**.

1. Wählen Sie die Schaltfläche **Pipeline erstellen** aus.

1. Wählen Sie **Azure Repos Git (Yaml)**.

1. Wählen Sie das Repository **eShopOnWeb** aus.

1. Wählen Sie die Option **Vorhandene Azure Pipelines-YAML-Datei** aus.

1. Wählen Sie die Datei **/.ado/eshoponweb-ci.yml** aus und klicken Sie dann auf **Weiter**.

1. Klicken Sie auf die Schaltfläche **Ausführen**, um die Pipeline auszuführen.

   > **Hinweis**: Ihre Pipeline bekommt einen Namen basierend auf dem Projektnamen. Sie benennen die Pipeline um, damit leichter zu identifizieren ist.

1. Wechseln Sie zu **Pipelines > Pipelines** und wählen Sie die zuletzt erstellte Pipeline aus. Wählen Sie die Auslassungspunkte und dann die Option **Umbenennen/verschieben** aus.

1. Geben Sie ihr den Namen **eshoponweb-ci**, und wählen Sie **Speichern** aus.

#### Aufgabe 2: Sicherstellen von Parametertypen für YAML-Pipelines

In dieser Aufgabe legen Sie Parameter- und Parametertypen für die Pipeline fest.

1. Wechseln Sie zu **Pipelines > Pipelines**, und wählen Sie die **eshoponweb-ci**-Pipeline aus.

1. Wählen Sie **Bearbeiten** aus.

1. Fügen Sie die folgenden Parameter oberhalb der Abschnitte für die Jobs am Anfang der YAML-Datei hinzu:

   ```yaml
   parameters:
   - name: dotNetProjects
     type: string
     default: '**/*.sln'
   - name: testProjects
     type: string
     default: 'tests/UnitTests/*.csproj'

   jobs:
   - job: Build
     pool: eShopOnWebSelfPool
     steps:

   ```

1. Ersetzen Sie die fest einprogrammierten Pfade in den Aufgaben „Wiederherstellen“, „Erstellen“ und „Testen“ durch die soeben erstellten Parameter.

   - **Projekte ersetzen**: `**/*.sln` mit Projekten: `${{ parameters.dotNetProjects }}` in den Aufgaben `Restore` und `Build`;
   - **Projekte ersetzen**: `tests/UnitTests/*.csproj` mit Projekten: `${{ parameters.testProjects }}` in der Aufgabe `Test`;

   Die Aufgaben „Wiederherstellen“, „Erstellen“ und „Testen“ im Schritte-Abschnitt der YAML-Datei sollten wie folgt aussehen:

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

1. Klicken Sie auf **Überprüfen und speichern**, um die Änderungen zu speichern, und klicken Sie dann auf **Speichern**.

1. Navigieren Sie zu **Pipelines > Pipelines** und öffnen Sie die **eshoponweb-ci** Pipeline, die automatisch ausgelöst wird.

1. Stellen Sie sicher, dass die Pipelineausführung erfolgreich abgeschlossen wird.

   ![Screenshot der Pipelineausführung mit Parametern.](media/pipeline-parameters-run.png)

#### Aufgabe 3: Variablen und Parameter sichern

In dieser Aufgabe sichern Sie die Variablen und Parameter aus Ihrer Pipeline mithilfe von Variablengruppen.

1. Wechseln Sie zu **Pipelines > Library**.

1. Wählen Sie die Schaltfläche **+ Variablengruppe**, um eine neue Variablengruppe mit dem Namen `BuildConfigurations` zu erstellen.

1. Fügen Sie eine Variable namens `buildConfiguration` hinzu und setzen Sie ihren Wert auf `Release`.

1. Speichern Sie die Variablengruppe.

   ![Screenshot der Variablengruppe mit BuildConfigurations.](media/eshop-variable-group.png)

1. Wählen Sie die Schaltfläche **Pipelineberechtigungen** und dann die Schaltfläche **+** aus, um eine neue Pipeline hinzuzufügen.

1. Wählen Sie die **eshoponweb-ci**-Pipeline, damit die Pipeline die Variablengruppe verwenden kann.

   ![Screenshot der Pipelineberechtigungen.](media/pipeline-permissions.png)

   > **Hinweis**: Sie können auch bestimmten Benutzern oder Gruppen die Möglichkeit geben, die Variablengruppe zu bearbeiten, indem Sie auf die Schaltfläche **Sicherheit** klicken.

1. Navigieren Sie zu **Pipelines > Pipelines**.

1. Öffnen Sie die Pipeline **eshoponweb-ci**, und wählen Sie **Bearbeiten** aus.

1. Verweisen Sie oben in der YML-Datei direkt unter den Parametern auf die Variablengruppe, indem Sie Folgendes hinzufügen:

   ```yaml
   variables:
     - group: BuildConfigurations
   ```

1. Fügen Sie in der Aufgabe „Erstellen" den Konfigurationsparameter zur Aufgabe hinzu, um die Buildkonfiguration aus der Variablengruppe zu verwenden.

    ```yaml
            command: 'build'
            projects: ${{ parameters.dotNetProjects }}
            configuration: $(buildConfiguration)
    ```

1. Klicken Sie auf **Überprüfen und speichern**, um die Änderungen zu speichern, und klicken Sie dann auf **Speichern**.

1. Öffnen Sie den **Eshoponweb-ci** Pipelinelauf. Es sollte erfolgreich laufen, wenn die Build-Konfiguration auf „Freigabe“ eingestellt ist. Sie können dies überprüfen, indem Sie sich die Protokolle der Aufgabe „Erstellen“ ansehen.

> **Hinweis**: Wenn die Buildkonfiguration in den Protokollen nicht auf "Freigabe" festgelegt ist, aktivieren Sie die Systemdiagnose, und führen Sie die Pipeline erneut aus, um den Konfigurationswert anzuzeigen.

> **Anmerkung**: Mit diesem Ansatz können Sie Ihre Variablen und Parameter durch die Verwendung von Variablengruppen absichern, ohne sie in YAML-Dateien hardcodieren zu müssen.

#### Aufgabe 4: Obligatorische Variablen und Parameter überprüfen

In dieser Aufgabe überprüfen Sie die obligatorischen Variablen, bevor die Pipeline ausgeführt wird.

1. Navigieren Sie zu **Pipelines > Pipelines**.

1. Öffnen Sie die Pipeline **eshoponweb-ci**, und wählen Sie **Bearbeiten** aus.

1. Fügen Sie im Abschnitt „Schritte“ ganz am Anfang (nach der Zeile **Schritte:**) eine neue Skriptaufgabe hinzu, um die obligatorischen Variablen zu validieren, bevor die Pipeline ausgeführt wird.

    ```yaml
    - script: |
        IF NOT DEFINED buildConfiguration (
          ECHO Error: buildConfiguration variable is not set
          EXIT /B 1
        )
      displayName: 'Validate Variables'
     ```

    > **Hinweis**: Dies ist eine einfache Validierung, um zu überprüfen, ob die Variable festgelegt ist. Wenn die Variablen nicht festgelegt sind, schlägt das Skript fehl, und die Pipeline wird beendet. Sie können eine komplexere Überprüfung hinzufügen, um den Wert der Variablen zu überprüfen oder wenn sie auf einen bestimmten Wert festgelegt ist.

1. Klicken Sie auf **Überprüfen und speichern**, um die Änderungen zu speichern, und klicken Sie dann auf **Speichern**.

1. Öffnen Sie den **Eshoponweb-ci** Pipelinelauf. Sie wird erfolgreich ausgeführt, da die buildConfiguration-Variable in der Variablengruppe festgelegt ist.

1. Um die Validierung zu testen, entfernen Sie die buildConfiguration-Variable aus der Variablengruppe, oder benennen Sie die Variable um, und führen Sie die Pipeline erneut aus. Bei folgendem Fehler sollte es fehlschlagen:

    ```yaml
    Error: buildConfiguration variable is not set   
    ```

    ![Screenshot der Pipelineausführung mit fehlerhafter Überprüfung.](media/pipeline-validation-fail.png)

1. Fügen Sie die Variable buildConfiguration wieder zur Variablengruppe hinzu und führen Sie die Pipeline erneut aus. Es sollte erfolgreich ausgeführt werden.

> [!IMPORTANT]
> Denken Sie daran, die im Azure-Portal erstellten Ressourcen zu löschen, um unnötige Gebühren zu vermeiden.

## Überprüfung

In diesem Lab haben Sie gelernt, wie Sie Pipelines für die sichere Verwendung von Variablen und Parametern konfigurieren und wie Sie obligatorische Variablen und Parameter überprüfen.
