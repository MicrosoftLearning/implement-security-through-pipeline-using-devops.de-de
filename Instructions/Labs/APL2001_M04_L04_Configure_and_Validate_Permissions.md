---
lab:
  title: Konfigurieren und Überprüfen von Berechtigungen
  module: 'Module 4: Configure and validate permissions'
---

# Konfigurieren und Überprüfen von Berechtigungen

Richten Sie in diesem Lab eine sichere Umgebung ein, die dem Prinzip der geringsten Rechte entspricht, um sicherzustellen, dass die Mitglieder nur auf die Ressourcen zugreifen können, die sie für die Ausführung ihrer Aufgaben benötigen, und um potenzielle Sicherheitsrisiken zu minimieren. Dazu gehören das Konfigurieren und Überprüfen von Benutzer- und Pipelineberechtigungen sowie das Einrichten von Genehmigungs- und Branchüberprüfungen in Azure DevOps.

Diese Übung dauert ca. **30** Minuten.

## Vorbereitung

Sie benötigen ein Azure-Abonnement, eine Azure DevOps-Organisation und die eShopOnWeb-Anwendung, um den Laboren zu folgen.

- Führen Sie die Schritte aus, um Ihre Lab-Umgebung[ zu ](APL2001_M00_Validate_Lab_Environment.md)überprüfen.
- Installieren Sie einen selbst gehosteten Agent nach dem Lab [Konfigurieren von Agents und Agentpools für sichere Pipelines oder die Schritte unter [Installieren eines selbst gehosteten](/Instructions/Labs/APL2001_M03_L03_Configure_Agents_And_Agent_Pools_for_Secure_Pipelines.md) Agents](https://docs.microsoft.com/azure/devops/pipelines/agents/v2-windows?view=azure-devops#install).

## Anweisungen

### Übung 1: Importieren der CI-Pipeline und Konfigurieren von pipelinespezifischen Berechtigungen

In dieser Übung importieren und ausführen Sie die CI-Pipeline für die eShopOnWeb-Anwendung und konfigurieren pipelinespezifische Berechtigungen.

#### Aufgabe 1: (Wenn erledigt, überspringen) Importieren und Ausführen der CI-Pipeline

> [!NOTE]
> Überspringen Sie den Import, wenn er bereits in einer anderen Übung durchgeführt wurde.

Importieren Sie zunächst die CI-Pipeline mit dem Namen ["eshoponweb-ci.yml](https://github.com/MicrosoftLearning/eShopOnWeb/blob/main/.ado/eshoponweb-ci.yml)".

1. Navigieren Sie zum Azure DevOps-Portal unter `https://dev.azure.com` und öffnen Sie Ihre Organisation.

1. Öffnen Sie das **eShopOnWeb-Projekt** .

1. Navigieren Sie in Azure Pipelines zu PipelinesPipelines.

1. Wählen Sie **die Schaltfläche "Neue Pipeline"** aus.

1. Wählen Sie **Azure Repos Git** (YAML) aus.

1. Wählen Sie das **eShopOnWeb-Repository** aus.

1. Wählen Sie die Option **Vorhandene Azure Pipelines-YAML-Datei** aus.

1. Wählen Sie die **Datei "/.ado/eshoponweb-ci.yml**" und dann "Weiter"** aus**.

1. Klicken Sie auf die Schaltfläche **Ausführen**, um die Pipeline auszuführen.

1. Ihre Pipeline nimmt einen Namen auf der Grundlage des Projektnamens an. Benennen Sie sie um, um die Pipeline besser zu identifizieren.

1. Wechseln Sie zu **Pipelines > Pipelines**, wählen Sie die zuletzt erstellte Pipeline aus, wählen Sie die Auslassungspunkte und dann **die Option "Umbenennen/Verschieben** " aus.

1. Nennen Sie es **eshoponweb-ci**, und wählen Sie "Speichern" aus****.

### Aufgabe 2: Konfigurieren und Ausführen der Pipeline mit bestimmten Berechtigungen

In dieser Aufgabe konfigurieren Sie die CI-Pipeline für die Ausführung mit einem bestimmten Agentpool und überprüfen die Berechtigungen zum Ausführen der Pipeline. Sie müssen über Berechtigungen zum Bearbeiten der Pipeline verfügen und dem Agentpool Berechtigungen hinzufügen.

1. Wechseln Sie zu den Projekteinstellungen, und klicken Sie dann unter „Pipelines“ auf „Agentpools“.

1. Öffnen Sie den **Standard-Agent-Pool** .

1. Wählen Sie die Registerkarte **Security** (Sicherheit) aus.

1. Wenn der Agentpool keine Einschränkung enthält, wählen Sie **die Schaltfläche "Berechtigungen** einschränken" aus.

    ![Screenshot der Registerkarte "Agentpoolsicherheit" ohne Einschränkungen.](media/agent-pool-security-no-restriction.png)

1. Wählen Sie die Schaltfläche "Hinzufügen **" aus, und wählen Sie **die **eshoponweb-ci-Pipeline** aus, um sie der Liste der Pipelines mit Zugriff auf den Agentpool hinzuzufügen.

1. Wählen Sie **Ausführen** aus, um die Pipeline auszuführen.

1. Öffnen Sie die laufende Pipeline. Wenn die Meldung "Diese Pipeline benötigt Die Berechtigung für den Zugriff auf eine Ressource benötigt, bevor diese Ausführung weiterhin ".Net Core Solution erstellen" angezeigt wird, wählen Sie **"Ansicht**", **"Zulassen" und **"Zulassen**"** aus.

Die Pipeline sollte erfolgreich ausgeführt werden können.

#### Aufgabe 3: (Wenn sie fertig sind, überspringen) Konfigurieren der CD-Pipeline und Überprüfen von Berechtigungen

> [!NOTE]
> Überspringen Sie den Import, wenn er bereits in einer anderen Übung durchgeführt wurde.

> [!IMPORTANT]
> Wenn Sie über Berechtigungen verfügen, können **Sie zulassen, dass** die Pipeline direkt von der ausgeführten Pipeline ausgeführt werden kann. Wenn Sie nicht über Berechtigungen verfügen, müssen Sie ein weiteres Konto mit Administratorberechtigungen verwenden, damit Ihre Pipeline mit dem spezifischen Agent ausgeführt werden kann, wie in der vorherigen Aufgabe 2 beschrieben, oder um dem Agentpool Benutzerberechtigungen hinzuzufügen.

1. Navigieren Sie in Azure Pipelines zu PipelinesPipelines.

1. Wählen Sie **die Schaltfläche "Neue Pipeline** " aus.

1. Wählen Sie **Azure Repos Git** (YAML) aus.

1. Wählen Sie das **eShopOnWeb-Repository** aus.

1. Wählen Sie die Option **Vorhandene Azure Pipelines-YAML-Datei** aus.

1. Wählen Sie die **Datei "/.ado/eshoponweb-cd-webapp-code.yml**" und dann "Weiter"** aus**.

1. Passen Sie in der YAML-Pipelinedefinition im Abschnitt "Variablen" Folgendes an:
   - Ersetzen Sie <yourSubscriptionId> durch Ihre Azure-Abonnement-ID.
   - **az400eshop-NAME**, mit einem Web-App-Namen, der mit einem globalen eindeutigen Namen bereitgestellt werden soll, **z. B. eshoponweb-lab-YOURNAME**.
   - **AZ400-EWebShop-NAME** mit dem Namen Ihrer Vorliebe, **z. B. rg-eshoponweb**.

1. Aktualisieren Sie die YAML-Datei, um das **windows-neueste** Image im **Von Microsoft gehosteten Standard-Agentpool** zu verwenden. Um dies zu öffnen, legen Sie den **Poolabschnitt** auf den folgenden Wert fest:

    ```yaml
    pool: 
      vmImage: windows-latest

    ```

1. Klicken Sie auf **Speichern** und dann auf **Test ausführen**.

1. Öffnen Sie die Pipeline, und Sie sehen die Meldung "Diese Pipeline benötigt die Berechtigung für den Zugriff auf eine Ressource, bevor diese Ausführung weiterhin web App bereitstellen kann". Wählen Sie die ausstehende Pipelineausführung und dann Anzeigen aus.

    ![Screenshot der Pipeline mit Genehmigungsschaltflächen".](media/pipeline-permission-permit.png)

### Konfigurieren und Überprüfen von Genehmigungs- und Branchüberprüfungen

In dieser Übung konfigurieren und überprüfen Sie die Genehmigungs- und Verzweigungsprüfungen für die CD-Pipeline.

#### Aufgabe 1: Erstellen einer neuen Umgebung und Hinzufügen von Genehmigungen und Überprüfungen

1. Wechseln Sie zu **Pipelines > Umgebungen**.

1. Wählen Sie die Schaltfläche **Umgebung erstellen** aus.

1. Benennen Sie die Umgebung **"Test"**, wählen Sie **"Keine"** als Ressource aus, und wählen Sie "Erstellen"** aus**.

1. Wählen Sie "Neue Umgebung" aus, erstellen Sie **eine neue Umgebung **"Produktion**", wählen Sie **"Keine**" als Ressource und dann "Erstellen"** aus**.**

1. Öffnen Sie die Testumgebung **, wählen Sie ***...*** und wählen Sie **Genehmigungen und Prüfungen aus**.**

1. Wählen Sie **Genehmigungen** aus.

1. Geben Sie im **Textfeld Genehmiger** Ihren Benutzernamen ein, und fügen Sie ihn hinzu, um den Genehmigungsprozess zu überprüfen.

1. Geben Sie die Anweisungen an **, genehmigen Sie die Bereitstellung zum Testen**, und wählen Sie "Erstellen"** aus**.

    ![Screenshot der Umgebungsgenehmigungen mit Anweisungen.](media/add-environment-approvals.png)

1. Wählen Sie **+** die Schaltfläche " **Verzweigung"** und dann " **Weiter"** aus.

1. Lassen Sie im **Feld "Zulässige Verzweigungen**" die Standardeinstellung, und wählen Sie "Erstellen"** aus**. Wenn Sie möchten, können Sie weitere Aktionen hinzufügen.

    ![Screenshot der Umgebungszweigsteuerung mit dem Standard Branch.](media/add-environment-branch-control.png)

1. Öffnen Sie die **Produktionsumgebung** , und führen Sie die gleichen Schritte aus, um Genehmigungen und Verzweigungssteuerung hinzuzufügen. Um die Umgebungen zu unterscheiden, fügen Sie die Anweisungen **hinzu, genehmigen Sie die Bereitstellung in der Produktion**, und fügen Sie die **Refs/heads/Standard** Branch den zulässigen Verzweigungen hinzu.

1. (Optional) Sie können weitere Umgebungen hinzufügen und Genehmigungen und Verzweigungssteuerungen für sie konfigurieren. Darüber hinaus können Sie die Sicherheit** so konfigurieren**, dass Benutzer oder Gruppen zur Umgebung hinzugefügt werden.
    - Öffnen Sie die **Testumgebung**, wählen Sie ***...*** und wählen Sie "Sicherheit"** aus**.
    - Wählen Sie **"Hinzufügen"** und dann den Benutzer aus, der die Pipeline ausführt, und die Rolle *"Benutzer*", *"Ersteller"* oder *"Leser*".
    - Wählen Sie **Hinzufügen**.
    - Wählen Sie **Speichern** aus.

#### Aufgabe 2: Konfigurieren der CD-Pipeline für die Verwendung der neuen Umgebung

1. Navigieren Sie in Azure Pipelines zu PipelinesPipelines.

1. Öffnen Sie die **Eshoponweb-cd-webapp-code-Pipeline** .

1. Wählen Sie **Bearbeiten** aus.

1. Fügen Sie über dem Kommentar #download **Artefakte** Folgendes hinzu:

    ```yaml
    stages:
    - stage: Test
      displayName: Testing WebApp
      jobs:
      - deployment: Test
        pool:
          vmImage: 'windows-latest'
        environment: Test
        strategy:
          runOnce:
            deploy:
              steps:
              - script: echo Hello world! Testing environments!
    - stage: Deploy
    displayName: Deploy to WebApp
      jobs:
      - deployment: Deploy
        pool: 
          vmImage: windows-latest
        environment: Production
        strategy:
          runOnce:
            deploy:
              steps:
              - checkout: self
    ```

    > [!NOTE]
    > Sie müssen die Zeilen nach dem Code über sechs Leerzeichen nach rechts verschieben, um sicherzustellen, dass YAML-Einzugsregeln erfüllt sind.

    Ihre Pipeline sollte wie folgt aussehen:

    ![Screenshot der Seite „Bereitstellungspipeline“](media/pipeline-add-yaml-deployment.png)

1. Klicken Sie auf Speichern und ausführen.

1. Öffnen Sie die Pipeline, und Sie sehen die Meldung "Diese Pipeline benötigt die Berechtigung für den Zugriff auf eine Ressource, bevor diese Ausführung weiterhin webApp testen kann". Wählen Sie **erneut "Anzeigen**", **"Genehmigung**" und "Genehmigung"** aus**.

    ![Screenshot der Pipeline mit der Schaltfläche "Zulassen", um die Pipelineausführung zu genehmigen".](media/pipeline-environment-permit.png)

1. Öffnen Sie die **Phase "WebApp** testen", und Sie werden sehen, dass die Meldung **1 Ihre Überprüfung erfordert, bevor diese Ausführung mit "WebApp** testen" fortfahren kann. Wählen Sie "**Überprüfen"** und dann "Genehmigen"** aus**.

    ![Screenshot der Pipeline mit der zu genehmigenden Testphase".](media/pipeline-test-environment-approve.png)

1. Warten Sie, bis die Pipeline abgeschlossen ist, öffnen Sie das Pipelineprotokoll, und überprüfen Sie, ob die **TestwebApp-Phase** erfolgreich ausgeführt wurde.

    ![Screenshot des Pipelineprotokolls mit der erfolgreichen Ausführung der TestwebApp-Phase".](media/pipeline-test-environment-success.png)

1. Zurück zur Pipeline, und Sie sehen die Phase "In WebApp bereitstellen", die auf die Genehmigung wartet.Back to the pipeline and you will see the stage **Deploy to WebApp** waiting for approval. Wählen Sie **"Überprüfen**" und **"Genehmigen"** wie zuvor für die WebApp-Phase** "** Testen" aus.

1. Warten Sie, bis die Pipeline abgeschlossen ist, überprüfen Sie, ob die **Bereitstellung in WebApp-Phase** erfolgreich ausgeführt wurde.

    ![Screenshot der Pipeline mit der Zu genehmigenden Phase "Bereitstellen in WebApp".](media/pipeline-deploy-environment-success.png)

Sie sollten in der Lage sein, die Pipeline erfolgreich mit den Genehmigungen und Zweigprüfungen in beiden Umgebungen, Test und Produktion auszuführen.

### Übung 3: Entfernen der Azure-Lab-Ressourcen.

1. Öffnen Sie in der Azure-Portal die erstellte Ressourcengruppe, und klicken Sie auf "**Ressourcengruppe** löschen" für alle erstellten Ressourcen in dieser Übung.

    ![Screenshot: Schaltfläche „Ressourcengruppe löschen“](media/delete-resource-group.png)

    > [!WARNING]
    > Denken Sie daran, alle neu erstellten Azure-Ressourcen zu entfernen, die Sie nicht mehr verwenden. Durch das Entfernen nicht verwendeter Ressourcen wird sichergestellt, dass keine unerwarteten Kosten anfallen.

1. Setzen Sie die spezifischen Berechtigungen zurück, die Sie der Azure DevOps-Organisation und dem Projekt in dieser Übung hinzugefügt haben.

## Überprüfung

Richten Sie in diesem Lab eine sichere Umgebung ein, die dem Prinzip der geringsten Rechte entspricht, um sicherzustellen, dass die Mitglieder nur auf die Ressourcen zugreifen können, die sie für die Ausführung ihrer Aufgaben benötigen, und um potenzielle Sicherheitsrisiken zu minimieren. Dazu gehören das Konfigurieren und Überprüfen von Benutzer- und Pipelineberechtigungen sowie das Einrichten von Genehmigungs- und Branchüberprüfungen in Azure DevOps.
