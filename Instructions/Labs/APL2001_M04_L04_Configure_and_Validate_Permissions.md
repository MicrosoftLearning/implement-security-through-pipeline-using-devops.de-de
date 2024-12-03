---
lab:
  title: Konfigurieren und Überprüfen von Berechtigungen
  module: 'Module 4: Configure and validate permissions'
---

# Konfigurieren und Überprüfen von Berechtigungen

In diesem Lab richten Sie eine sichere Umgebung ein, die dem Prinzip der geringsten Rechte entspricht, um sicherzustellen, dass die Mitglieder nur auf die Ressourcen zugreifen können, die sie für die Ausführung ihrer Aufgaben benötigen, und um potenzielle Sicherheitsrisiken zu minimieren. Dies umfasst das Konfigurieren und Überprüfen von Benutzer- und Pipelineberechtigungen sowie das Einrichten von Genehmigungen und Branchüberprüfungen in Azure DevOps.

Diese Übung dauert ca. **20** Minuten.

## Vorbereitung

Sie benötigen ein Azure-Abonnement, eine Azure DevOps-Organisation und die eShopOnWeb-Anwendung, um den Labs zu folgen.

- Folgen Sie den Schritten, um Ihre [Lab-Umgebung zu überprüfen](APL2001_M00_Validate_Lab_Environment.md).
- Installieren Sie einen selbstgehosteten Agent, indem Sie das Lab [Konfigurieren von Agents und Agentpools für sichere Pipelines](APL2001_M02_L02_Configure_Agents_And_Agent_Pools_for_Secure_Pipelines.md) oder die Schritte in [Installieren eines selbst gehosteten Agents](https://learn.microsoft.com/azure/devops/pipelines/agents/windows-agent) befolgen.

## Anweisungen

### Übung 0: (Überspringen, wenn bereits erledigt) Importieren und Ausführen von CI/CD-Pipelines

In dieser Übung werden Sie die CI/CD-Pipelines in das Azure-DevOps-Projekt importieren und ausführen.

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

#### Aufgabe 2: (überspringen, wenn erledigt) Importieren und Ausführen der CD-Pipeline

> **Hinweis**: In dieser Aufgabe werden Sie die CD-Pipeline [eshoponweb-cd-webapp-code.yml](https://github.com/MicrosoftLearning/eShopOnWeb/blob/main/.ado/eshoponweb-cd-webapp-code.yml) importieren und ausführen.

1. Navigieren Sie zu **Pipelines > Pipelines**.

1. Wählen Sie die Schaltfläche **Neue Pipeline** aus.

1. Wählen Sie **Azure Repos Git** (YAML) aus.

1. Wählen Sie das Repository **eShopOnWeb** aus.

1. Wählen Sie die Option **Vorhandene Azure Pipelines-YAML-Datei** aus.

1. Wählen Sie zunächst die Datei **/.ado/eshoponweb-cd-webapp-code.yml** und dann **Weiter** aus.

1. Legen Sie in der YAML-Pipelinedefinition im Abschnitt „Variablen“ Folgendes fest:

   ```yaml
   variables:
     resource-group: 'YOUR-RESOURCE-GROUP-NAME'
     location: 'centralus'
     templateFile: 'infra/webapp.bicep'
     subscriptionid: 'YOUR-SUBSCRIPTION-ID'
     azureserviceconnection: 'YOUR-AZURE-SERVICE-CONNECTION-NAME'
     webappname: 'YOUR-WEB-APP-NAME'
   ```

1. Ersetzen Sie die Werte der Variablen jeweils durch die Werte für Ihre Umgebung:

   - Ersetzen Sie **YOUR-RESOURCE-GROUP-NAME** durch den Namen der Ressourcengruppe, die Sie in diesem Lab verwenden möchten, wie **rg-eshoponweb-secure**.
   - Legen Sie den Namen der Variablen **Standort** auf den Namen der Azure-Region fest, in der Sie Ihre Ressourcen bereitstellen möchten, wie **centralus**.
   - Ersetzen Sie **YOUR-SUBSCRIPTION-ID** durch Ihre Azure-Abonnement-ID.
   - Ersetzen Sie **YOUR-AZURE-SERVICE-CONNECTION-NAME** mit **azure subs**.
   - Ersetzen Sie **YOUR-WEB-APP-NAME** durch einen global eindeutigen Namen der Web-App, die bereitgestellt werden soll, z.B. die Zeichenfolge **eshoponweb-lab-multi-123456** gefolgt von einer zufälligen sechsstelligen Zahl.

1. Wählen Sie zunächst **Speichern und ausführen** und dann den direkten Commit zu Mainbranch aus.

1. Wählen Sie erneut **Speichern und ausführen** aus.

1. Öffnen Sie die Pipelineausführung. Wenn Sie Ihnen die Meldung „This pipeline needs permission to access a resource before this run can continue to Deploy to WebApp“ angezeigt wird, wählen Sie erneut **View**, **Permit** und **Permit** aus. Diese Berechtigung ist erforderlich, damit die Pipeline die Azure App Service-Ressource erstellen kann.

   ![Screenshot der Genehmigung des Zugriffs über die YAML-Pipeline](media/pipeline-deploy-permit-resource.png)

1. Die Bereitstellung kann einige Minuten dauern. Warten Sie, bis die Pipeline ausgeführt wird. Die Pipeline wird nach Abschluss der CI-Pipeline ausgelöst und umfasst die folgenden Aufgaben:

   - **AzureResourceManagerTemplateDeployment**: Stellt die Azure App Service-Web-App mithilfe der Bicep-Vorlage bereit.
   - **AzureRmWebAppDeployment**: Veröffentlicht die Website in der Azure App Service-Web-App.

   > **Hinweis**: Falls die Bereitstellung fehlschlägt, navigieren Sie zur Seite „Pipelineausführung“ und wählen Sie **Fehlerhafte Aufträge erneut ausführen** aus, um eine weitere Pipelineausführung aufzurufen.

   > **Hinweis**: Ihre Pipeline bekommt einen Namen basierend auf dem Projektnamen. Wir **benennen sie um**, damit wir die Pipeline besser identifizieren können.

1. Wechseln Sie zu **Pipelines > Pipelines** und wählen Sie die zuletzt erstellte Pipeline aus. Wählen Sie die Auslassungspunkte und dann die Option **Umbenennen/verschieben** aus.

1. Nennen Sie es **eshoponweb-cd-webapp-code** und klicken Sie auf **Speichern**.

### Übung 1: Konfigurieren und Überprüfen von Genehmigungs- und Branchüberprüfungen

In dieser Übung konfigurieren und überprüfen Sie die Genehmigungen und Branchüberprüfungen für die CD-Pipeline.

#### Aufgabe 1: Umgebung erstellen und Genehmigungen und Überprüfungen hinzufügen

1. Wählen Sie im Azure DevOps-Portal auf der **eShopOnWeb**-Projektseite **Pipelines > Umgebungen** aus.

1. Klicken Sie auf **Umgebung erstellen**.

1. Benennen Sie die Umgebung **Test**, wählen Sie zunächst **Keine** als Ressource und dann **Erstellen** aus.

1. Öffnen Sie die **Test**-Umgebung, wählen Sie die Registerkarte **Genehmigungen und Überprüfungen** aus.

1. Wählen Sie **Genehmigungen** aus.

1. Geben Sie im Textfeld **Genehmigende Person** Ihren Benutzernamen ein.

1. Falls nicht aktiviert, markieren Sie das Kästchen „Genehmigenden erlauben, ihre eigenen Läufe zu genehmigen“.

1. Geben Sie die Anweisungen **Genehmigen der Bereitstellung für Test**, und wählen Sie **Erstellen** aus.

   ![Screenshot der Umgebungsgenehmigungen mit Anweisungen.](media/add-environment-approvals.png)

1. Klicken Sie auf die Schaltfläche **+ Neu hinzufügen**, wählen Sie **Branchenüberprüfung**, und wählen Sie dann **Weiter**.

1. Behalten Sie im Feld **Zulässige Branches** die Standardeinstellung bei, und wählen Sie **Erstellen** aus. Sie können bei Bedarf weitere Branches hinzufügen.

   ![Screenshot der Steuerung von Umgebungsbranches mit dem Mainbranch.](media/add-environment-branch-control.png)

1. Erstellen Sie eine weitere Umgebung mit dem Namen **Production**, und führen Sie die gleichen Schritte aus, um Genehmigungen und die Steuerung von Branches hinzuzufügen. Um die Umgebungen zu unterscheiden, fügen Sie die Anweisungen **Genehmigen der Bereitstellung für Production** hinzu und legen Sie die zulässigen Branches auf **refs/heads/main** fest.

> **Anmerkung**: Sie können weitere Umgebungen hinzufügen und Genehmigungen und Branchenüberprüfungen für sie konfigurieren. Darüber hinaus können Sie **Sicherheit** so konfigurieren, dass Benutzer*innen oder Gruppen zur Umgebung mit Rollen wie *Benutzer*in*, *Ersteller*in* oder *Leser*in* hinzugefügt werden.

#### Aufgabe 2: CD-Pipeline für die Verwendung der neuen Umgebung konfigurieren

1. Wählen Sie im Azure DevOps-Portal auf der **eShopOnWeb**-Projektseite **Pipelines > Pipelines** aus.

1. Öffnen Sie die **eshoponweb-cd-webapp-code**-Pipeline.

1. Wählen Sie **Bearbeiten** aus.

1. Wählen Sie die Zeile oberhalb des Kommentars **#download artifacts** bis zur Zeile **stages:** in der Pipeline-YAML-Datei aus und ersetzen Sie den Inhalt durch den folgenden Code:

   ```yaml
   stages:
   - stage: Test
     displayName: Testing WebApp
     jobs:
     - deployment: Test
       pool: eShopOnWebSelfPool
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
       pool: eShopOnWebSelfPool
       environment: Production
       strategy:
         runOnce:
           deploy:
             steps:
             - checkout: self
   ```

   > **Hinweis**: Sie müssen alle Zeilen, die dem obigen Code folgen, um sechs Leerzeichen nach rechts verschieben, damit die YAML-Einrückungsregeln eingehalten werden.

   Ihre Pipeline sollte wie folgt aussehen:

   ![Screenshot der Pipeline mit der neuen Bereitstellung.](media/pipeline-add-yaml-deployment.png)

   > [!IMPORTANT]
   > Vergewissern Sie sich, dass der Name des **Pools** derselbe ist wie der, den Sie in der vorherigen Übung erstellt haben.

1. Klicken Sie auf "**Überprüfen und speichern**", wählen Sie den Commit direkt im Mainbranch aus, und klicken Sie dann auf **Speichern**.

1. Ihre Pipeline wird automatisch ausgelöst. Öffnen Sie die Pipelineausführung.

   > **Hinweis**: Wenn Sie die Meldung „Diese Pipeline benötigt eine Berechtigung für den Zugriff auf eine Ressource, bevor dieser Lauf mit dem Testen der WebApp fortgesetzt werden kann“ erhalten, wählen Sie erneut **Ansicht**, **Berechtigung** und **Berechtigung**.

1. Öffnen Sie die Phase **Testen von WebApp** der Pipeline, und beachten Sie die Meldung **1 Genehmigung muss von Ihnen überprüft werden, bevor diese Ausführung mit dem Testen von WebApp fortfahren kann**. Wählen Sie zunächst **Überprüfen** und dann **Genehmigen** aus.

   ![Screenshot der Pipeline mit der zu genehmigenden Testphase“.](media/pipeline-test-environment-approve.png)

1. Warten Sie, bis die Ausführung der Pipeline abgeschlossen ist, öffnen Sie das Pipelineprotokoll, und überprüfen Sie, ob die Phase **Testen von WebApp** erfolgreich ausgeführt wurde.

   ![Screenshot des Pipelineprotokolls mit der erfolgreichen Ausführung der Phase „Testen von WebApp“.](media/pipeline-test-environment-success.png)

1. Zurück zur Pipeline, und Sie sehen, dass die Phase **Bereitstellen für WebApp** auf die Genehmigung wartet. Wählen Sie **Überprüfen** und **Genehmigen** aus, wie Sie es zuvor für die Phase **Testen von WebApp** getan haben.

   > **Hinweis**: Wenn Sie die Meldung „Diese Pipeline benötigt eine Berechtigung für den Zugriff auf eine Ressource, bevor diese Ausführung mit der Bereitstellung in der WebApp fortfahren kann“ erhalten, wählen Sie **Ansicht**, **Zulassen** und noch einmal **Zulassen** aus.

1. Warten Sie, bis die Ausführung der Pipeline abgeschlossen ist, und überprüfen Sie, ob die Phase **Bereitstellen für WebApp** erfolgreich ausgeführt wurde.

   ![Screenshot der Pipeline mit der zu genehmigenden ‚Bereitstellen für WebApp‘-Phase“.](media/pipeline-deploy-environment-success.png)

> **Hinweis**: Sie sollten in der Lage sein, die Pipeline mit den Genehmigungen und Verzweigungsprüfungen in beiden Umgebungen, Test und Produktion, erfolgreich auszuführen.

> [!IMPORTANT]
> Denken Sie daran, die im Azure-Portal erstellten Ressourcen zu löschen, um unnötige Gebühren zu vermeiden.

## Überprüfung

In diesem Lab haben Sie gelernt, wie Sie eine sichere Umgebung einrichten, die dem Prinzip der geringsten Rechte entspricht, um sicherzustellen, dass die Mitglieder nur auf die Ressourcen zugreifen können, die sie für die Ausführung ihrer Aufgaben benötigen, und um potenzielle Sicherheitsrisiken zu minimieren. Sie haben Benutzer- und Pipelineberechtigungen konfiguriert und überprüft und Genehmigungen und Branchüberprüfungen in Azure DevOps eingerichtet.
