---
lab:
  title: Erweitern einer Pipeline für die Verwendung mehrerer Vorlagen
  module: 'Module 5: Extend a pipeline to use multiple templates'
---

# Erweitern einer Pipeline für die Verwendung mehrerer Vorlagen

In diesem Lab lernen Sie, wie wichtig es ist, eine Pipeline auf mehrere Vorlagen zu erweitern und wie Sie Azure DevOps dafür verwenden. Dieses Lab behandelt grundlegende Konzepte und Best Practices für das Erstellen einer mehrstufigen Pipeline, einer Variablenvorlage, einer Auftragsvorlage und einer Phasenvorlage.

Diese Übung dauert ca. **20** Minuten.

## Vorbereitung

Sie benötigen ein Azure-Abonnement, eine Azure DevOps-Organisation und die eShopOnWeb-Anwendung, um den Labs zu folgen.

- Folgen Sie den Schritten, um Ihre [Lab-Umgebung zu überprüfen](APL2001_M00_Validate_Lab_Environment.md).

## Anweisungen

### Übung 1: Erstellen einer mehrstufigen YAML-Pipeline

In dieser Übung erstellen Sie eine mehrstufige YAML-Pipeline in Azure DevOps.

#### Aufgabe 1: Erstellen einer mehrstufigen YAML-Hauptpipeline

1. Navigieren Sie zum Azure DevOps-Portal unter `https://aex.dev.azure.com` und öffnen Sie Ihre Organisation.

1. Öffnen Sie das Projekt **eShopOnWeb**.

1. Navigieren Sie zu **Pipelines > Pipelines**.

1. Klicken Sie auf die Schaltfläche **Neue Pipeline**.

1. Wählen Sie **Azure Repos Git (Yaml)**.

1. Wählen Sie das Repository **eShopOnWeb** aus.

1. Wählen Sie **Starterpipeline** aus.

1. Ersetzen Sie den Inhalt der Datei **azure-pipelines.yml** durch folgenden Code:

   ```yaml
   trigger:
   - main

   pool:
     vmImage: 'windows-latest'

   stages:
   - stage: Dev
     jobs:
     - job: Build
       steps:
       - script: echo Build
   - stage: Test
     jobs:
     - job: Test
       steps:
       - script: echo Test
   - stage: Production
     jobs:
     - job: Deploy
       steps:
       - script: echo Deploy
   ```

1. Klicken Sie auf **Speichern und ausführen**. Wählen Sie zunächst den direkten Commit zu Mainbranch und dann **Speichern und ausführen** aus.

1. Die Pipeline wird mit den drei Phasen (Entwicklung, Test und Produktion) und den entsprechenden Aufträgen ausgeführt. Warten Sie, bis die Pipeline abgeschlossen ist, und navigieren Sie zurück zur Seite **Pipelines**.

   ![Screenshot der Pipeline, die mit den drei Phasen und den entsprechenden Aufträgen ausgeführt wird](media/eshoponweb-pipeline-multi-stage.png)

1. Wählen Sie **...** (Weitere Optionen) auf der rechten Seite der soeben erstellten Pipeline und anschließend **Umbenennen/Verschieben** aus.

1. Benennen Sie die Pipeline in **eShopOnWeb-MultiStage-Main** um, und wählen Sie **Speichern** aus.

#### Aufgabe 2: Erstellen einer Variablenvorlage

1. Wechseln Sie zu **Repos > Dateien**.

1. Erweitern Sie den Ordner **.ado**, und klicken Sie auf **Neue Datei**.

1. Geben Sie der Datei den Namen **eshoponweb-variables.yml**, und klicken Sie auf **Erstellen**.

1. Fügen Sie der Datei den folgenden Code hinzu:

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

1. Wählen Sie **Commit** aus, geben Sie im Textfeld „Commitkommentar“ `[skip ci]` ein, und wählen Sie dann **Commit** aus.

   > **Hinweis**: Indem Sie den Kommentar `[skip ci]` zur Übergabe hinzufügen, verhindern Sie die automatische Ausführung der Pipeline, die zu diesem Zeitpunkt standardmäßig nach jeder Änderung an der Repository ausgeführt wird. 

#### Aufgabe 3: Vorbereiten der Pipeline für die Verwendung von Vorlagen

1. Wechseln Sie im Azure DevOps-Portal auf der Projektseite **eShopOnWeb** zu **Pipelines > Pipelines**.

1. Wählen Sie im Stammverzeichnis des Repositorys die Datei **azure-pipelines.yml** aus, die die Definition der Pipeline **eShopOnWeb-MultiStage-Main** enthält.

1. Klicken Sie auf die Schaltfläche **Bearbeiten**.

1. Ersetzen Sie den Inhalt der Datei **azure-pipelines.yml** durch folgenden Code:

   ```yaml
   trigger:
   - main
   variables:
   - template: .ado/eshoponweb-variables.yml
   
   stages:
   - stage: Dev
     jobs:
     - template: .ado/eshoponweb-ci.yml
   - stage: Test
     jobs:
     - template: .ado/eshoponweb-cd-webapp-code.yml
   - stage: Production
     jobs:
     - job: Deploy
       steps:
       - script: echo Deploy to Production or Swap
   ```

1. Wählen Sie **Commit** aus, geben Sie im Textfeld „Commitkommentar“ `[skip ci]` ein, und wählen Sie dann **Commit** aus.

#### Aufgabe 4: Aktualisieren von CI/CD-Vorlagen

1. Wählen Sie im Abschnitt **Repos** des Projekts **eShopOnWeb** das Verzeichnis **.ado** und anschließend die Datei **eshoponweb-ci.yml** aus.

1. Klicken Sie auf die Schaltfläche **Bearbeiten**.

1. Entfernen Sie den gesamten Text über dem Abschnitt **Aufträge**.

   ```yaml
   #NAME THE PIPELINE SAME AS FILE (WITHOUT ".yml")
   # trigger:
   # - main
   
   resources:
     repositories:
       - repository: self
         trigger: none
   
   stages:
   - stage: Build
     displayName: Build .Net Core Solution
   ```

1. Wählen Sie **Abschließen** aus, geben Sie in das Textfeld für den Abschließenden Kommentar `[skip ci]` ein, und wählen Sie dann **Abschließen** aus.

1. Wählen Sie im Abschnitt **Repos** des Projekts **eShopOnWeb** das Verzeichnis **.ado** und anschließend die Datei **eshoponweb-cd-webapp-code.yml** aus.

1. Klicken Sie auf die Schaltfläche **Bearbeiten**.

1. Entfernen Sie den gesamten Text über dem Abschnitt **Aufträge**.

   ```yaml
    # NAME THE PIPELINE SAME AS FILE (WITHOUT ".yml") #
    # Trigger CD when CI executed successfully
    
    resources:
      pipelines:
        - pipeline: eshoponweb-ci
          source: eshoponweb-ci # given pipeline name
          trigger: true
    
    repositories:
      - repository: eShopSecurity
        type: git
        name: eShopSecurity/eShopSecurity # name of the project and repository
    
    variables:
      - template: eshoponweb-secure-variables.yml@eShopSecurity # name of the template and repository
    
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
   ```

1. Ersetzen Sie den vorhandenen Inhalt des Schritts **#download artefacts** durch folgenden Inhalt:

   ```yaml
       - download: current
         artifact: Website
       - download: current
         artifact: Bicep
   ```

1. Wählen Sie **Abschließen** aus, geben Sie in das Textfeld für den Abschließenden Kommentar `[skip ci]` ein, und wählen Sie dann **Abschließen** aus.

#### Aufgabe 5: Ausführen der Hauptpipeline

1. Navigieren Sie zu **Pipelines > Pipelines**.

1. Öffnen Sie die Pipeline **eShopOnWeb-MultiStage-Main**.

1. Klicken Sie auf **Pipeline ausführen**.

   > **Hinweis**: Wenn Sie eine Meldung erhalten, dass die Pipeline eine Zugriffsberechtigung für eine Ressource benötigt, bevor dieser Lauf fortgesetzt werden kann, wählen Sie **Ansicht** und dann **Erlaubnis** und **Erlaubnis** erneut aus, um die Pipeline laufen zu lassen.

   > **Anmerkung**: Wenn Aufträge in der Bereitstellungsphase fehlschlagen, navigieren Sie zur Seite mit der Pipelineausführung und wählen Sie **Fehlerhafte Aufträge erneut ausführen*** aus.

1. Warten Sie, bis die Pipeline abgeschlossen ist, und überprüfen Sie die Ergebnisse.

   ![Screenshot der Pipeline, die mit den drei Phasen und den entsprechenden Aufträgen ausgeführt wird](media/multi-stage-completed.png)

> [!IMPORTANT]
> Denken Sie daran, die im Azure-Portal erstellten Ressourcen zu löschen, um unnötige Gebühren zu vermeiden.

## Überprüfung

In diesem Lab haben Sie gelernt, wie Sie eine Pipeline mithilfe von Azure DevOps auf mehrere Vorlagen erweitern. Dieses Lab wurden grundlegende Konzepte und Best Practices für das Erstellen einer mehrstufigen Pipeline, einer Variablenvorlage, einer Auftragsvorlage und einer Phasenvorlage behandelt.
