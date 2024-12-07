---
lab:
  title: Konfigurieren einer Projekt- und Repositorystruktur zur Unterstützung sicherer Pipelines
  module: 'Module 1: Configure a project and repository structure to support secure pipelines'
---

# Konfigurieren einer Projekt- und Repositorystruktur zur Unterstützung sicherer Pipelines

In diesem Lab erfahren Sie, wie Sie eine Projekt- und Repositorystruktur zur Unterstützung sicherer Pipelines in Azure DevOps konfigurieren. In diesem Lab werden Best Practices zum Organisieren von Projekten und Repositorys, zum Zuweisen von Berechtigungen und zum Verwalten sicherer Dateien behandelt.

Diese Übungen nehmen ungefähr **30** Minuten in Anspruch.

## Vorbereitung

Sie benötigen ein Azure-Abonnement, eine Azure DevOps-Organisation und die eShopOnWeb-Anwendung, um den Labs zu folgen.

- Folgen Sie den Schritten, um Ihre [Lab-Umgebung zu überprüfen](APL2001_M00_Validate_Lab_Environment.md).

## Anweisungen

### Übung 1: Konfigurieren einer sicheren Projektstruktur

In dieser Übung konfigurieren Sie eine sichere Projektstruktur, indem Sie ein neues Projekt erstellen und ihm Projektberechtigungen zuweisen. Das Trennen von Zuständigkeiten und Ressourcen in verschiedene Projekte oder Repositorys mit bestimmten Berechtigungen erhöht die Sicherheit.

#### Aufgabe 1: Erstellen eines neuen Teamprojekts

1. Navigieren Sie zum Azure DevOps-Portal unter `https://aex.dev.azure.com` und öffnen Sie Ihre Organisation.

1. Öffnen Sie in der unteren linken Ecke des Portals Ihre **Organisationseinstellungen** und anschließend im Abschnitt „Allgemein“ den Eintrag **Projekte**.

1. Wählen Sie die Option **Neues Projekt** aus und verwenden Sie die folgenden Einstellungen:

   - Name: **eShopSecurity**
   - Sichtbarkeit: **Privat**
   - Erweitert: Versionskontrolle: **Git**
   - Erweitert: Work Item Process: **Scrum**

   ![Screenshot des Dialogfelds „Neues Projekt“ mit den angegebenen Einstellungen](media/new-team-project.png)

1. Wählen Sie **Erstellen** aus, um das neue Projekt zu erstellen.

1. Sie können jetzt zwischen den verschiedenen Projekten wechseln, indem Sie in der oberen linken Ecke des Azure DevOps-Portals auf das Azure DevOps-Symbol klicken.

   ![Screenshot der Azure DevOps-Teamprojekte eShopOnWeb und eShopSecurity](media/azure-devops-projects.png)

Sie können Berechtigungen und Einstellungen für jedes Projekt separat verwalten, indem Sie zum Menü „Projekteinstellungen“ wechseln und das entsprechende Teamprojekt auswählen. Wenn Sie über mehrere Benutzer*innen oder Teams verfügen, die an verschiedenen Projekten arbeiten, können Sie jedem Projekt auch separat Berechtigungen zuweisen.

#### Aufgabe 2: Erstellen eines neuen Repositorys und Zuweisen von Projektberechtigungen

1. Wählen Sie den Namen der Organisation in der oberen linken Ecke des Azure DevOps-Portals aus, und wählen Sie dann das neue Projekt **eShopSecurity** aus.

1. Wählen Sie das Menü **Repositorys** aus.

1. Wählen Sie die Schaltfläche **Initialisieren** aus, um das neue Repository zu initialisieren, indem Sie die Datei README.md hinzufügen.

1. Öffnen Sie das Menü **Projekteinstellungen** in der unteren linken Ecke des Portals, und wählen Sie im Abschnitts „Repos“ **Repositorys** aus.

1. Wählen Sie das neue Repository **eShopSecurity** und anschließend die Registerkarte **Sicherheit** aus.

   > **Hinweis**: Stellen Sie sicher, dass Sie die Registerkarte „Sicherheit“ nur für das jeweilige Repository auswählen und nicht für alle Repositories im Projekt. Wenn Sie alle Repositorys auswählen, verlieren Sie möglicherweise den Zugriff auf andere Repositorys im Projekt.

1. Entfernen Sie die Berechtigungen „Erben“ vom übergeordneten Element, indem Sie die Umschaltfläche **Vererbung** deaktivieren.

1. Wählen Sie die Gruppe **Beitragende** und wählen Sie das Dropdown-Menü **Ablehnen** für alle Berechtigungen außer **Berechtigungen verwalten** und **Lesen**. Dadurch wird verhindert, dass alle Benutzer*innen der Gruppe „Mitwirkende“ auf das Repository zugreifen können.

   > **Hinweis**: In einem realen Szenario verweigern Sie auch die Verwaltungsberechtigungen für die Gruppe "Mitwirkende". Für diese Übung erlauben wir der Gruppe "Mitwirkende", Berechtigungen zu verwalten, damit Sie die Übung abschließen können.

1. Wählen Sie unter „Benutzer“ Ihren Benutzernamen aus, und wählen Sie die Schaltfläche **Zulassen** aus, um alle Berechtigungen zuzulassen.

   > **Hinweis**: Wenn Sie Ihren Namen nicht im Abschnitt **Benutzende** sehen, geben Sie Ihren Namen in das Textfeld **Suche nach Benutzenden oder Gruppen** ein und wählen Sie ihn in der Ergebnisliste aus.

   ![Screenshot der Repositorysicherheitseinstellungen mit der Auswahl von „Zulassen“ für die Leseberechtigung und „Verweigern“ für alle anderen Berechtigungen](media/repository-security.png)

1. Die von Ihnen vorgenommenen Änderungen werden automatisch gespeichert.

Jetzt können nur der Benutzer bzw. die Benutzerin, dem bzw der Sie Berechtigungen zugewiesen haben, und die Administratoren auf das Repository zugreifen. Dies ist nützlich, wenn Sie bestimmten Benutzer*innen die Berechtigung erteilen möchten, auf Repository zuzugreifen und Pipelines aus dem eShopOnWeb-Projekt auszuführen.

### Übung 2: Konfigurieren einer Pipeline- und Vorlagenstruktur zur Unterstützung sicherer Pipelines

#### Aufgabe 1: Importieren und Ausführen der CI-Pipeline

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

#### Übung 2: Importieren und Ausführen der CD-Pipeline

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

Jetzt sollten zwei Pipelines in Ihrem eShopOnWeb-Projekt ausgeführt werden.

![Screenshot der erfolgreich ausgeführten CI/CD-Pipelines](media/pipeline-successful-executed.png)

#### Aufgabe 3: Verschieben der CD-Pipelinevariablen in eine YAML-Vorlage

In dieser Aufgabe erstellen Sie eine YAML-Vorlage zum Speichern der Variablen, die in der CD-Pipeline verwendet werden. Auf diese Weise können Sie die Vorlage in anderen Pipelines wiederverwenden.

1. Wechseln Sie zu **Repos** und zu **Dateien**.

1. Erweitern Sie den Ordner **.ado**, und wählen Sie **Neue Datei** aus.

1. Geben Sie der Datei den Namen **eshoponweb-secure-variables.yml**, und wählen Sie **Erstellen** aus.

1. Fügen Sie der neuen Datei den Variablenabschnitt hinzu, der in der CD-Pipeline verwendet wird. Die Datei sollte folgendermaßen aussehen:

   ```yaml
   variables:
     resource-group: 'rg-eshoponweb-secure'
     location: 'southcentralus' #the name of the Azure region you want to deploy your resources
     templateFile: 'infra/webapp.bicep'
     subscriptionid: 'YOUR-SUBSCRIPTION-ID'
     azureserviceconnection: 'azure subs' #the name of the service connection to your Azure subscription
     webappname: 'eshoponweb-lab-secure-XXXXXX' #the globally unique name of the web app
   ```

   > **Wichtig**: Ersetzen Sie die Werte der Variablen durch die Werte Ihrer Umgebung (Ressourcengruppe, Standort, Abonnement-ID, Azure-Dienstverbindung und Web-App-Name).

1. Wählen Sie **Commit** aus, geben Sie im Textfeld „Commitkommentar“ `[skip ci]` ein, und wählen Sie dann **Commit** aus.

   > **Hinweis**: Indem Sie den Kommentar `[skip ci]` zur Übergabe hinzufügen, verhindern Sie die automatische Ausführung der Pipeline, die zu diesem Zeitpunkt standardmäßig nach jeder Änderung an der Repository ausgeführt wird.

1. Öffnen Sie in der Liste der im Repository enthaltenen Dateien die Pipelinedefinition **eshoponweb-cd-webapp-code.yml**, und ersetzen Sie den Variablenabschnitt durch Folgendes:

   ```yaml
   variables:
     - template: eshoponweb-secure-variables.yml
   ```

1. Wählen Sie **Commit** aus, übernehmen Sie den Standardkommentar, und wählen Sie dann **Commit ausführen** aus, um die Pipeline erneut auszuführen.

1. Vergewissern Sie sich, dass die Pipelineausführung erfolgreich abgeschlossen wird.

Jetzt verfügen Sie über eine YAML-Vorlage mit den Variablen, die in der CD-Pipeline verwendet werden. Sie können diese Vorlage in anderen Pipelines in Szenarien wiederverwenden, in denen Sie dieselben Ressourcen bereitstellen müssen. Außerdem kann Ihr Betriebsteam die Ressourcengruppe und den Standort, an dem die Ressourcen bereitgestellt werden, sowie andere Informationen in Ihren Vorlagenwerten steuern, ohne dass Sie Änderungen an Ihrer Pipeline-Definition vornehmen müssen.

#### Aufgabe 4: Verschieben der YAML-Vorlagen in ein separates Repository und Projekt

In dieser Aufgabe verschieben Sie die YAML-Vorlagen in ein separates Repository und Projekt.

1. Wechseln Sie in Ihrem eShopSecurity-Projekt zu **Repos > Dateien**.

1. Erstellen Sie eine neue Datei namens **eshoponweb-secure-variables.yml**.

1. Kopieren Sie den Inhalt der Datei **.ado/eshoponweb-secure-variables.yml** aus dem eShopOnWeb-Repository in die neue Datei.

1. Führen Sie für die Änderungen einen Commit aus.

1. Öffnen Sie die Pipelinedefinition **eshoponweb-cd-webapp-code.yml** im eShopOnWeb-Repository.

1. Fügen Sie dem Ressourcenabschnitt vor dem Variablenabschnitt in der Pipelinedefinition Folgendes hinzu:

   ```yaml
     repositories:
       - repository: eShopSecurity
         type: git
         name: eShopSecurity/eShopSecurity #name of the project and repository
   ```

1. Ersetzen Sie den Variablenabschnitt durch den folgenden Inhalt:

   ```yaml
   variables:
     - template: eshoponweb-secure-variables.yml@eShopSecurity #name of the template and repository
   ```

   ![Screenshot der Pipelinedefinition mit den neuen Variablen und Ressourcenabschnitten](media/pipeline-variables-resource-section.png)

1. Wählen Sie **Commit** aus, übernehmen Sie den Standardkommentar, und wählen Sie dann **Commit ausführen** aus, um die Pipeline erneut auszuführen.

1. Navigieren Sie zur Pipelineausführung, und überprüfen Sie, ob die Pipeline die YAML-Datei aus dem eShopSecurity-Repository verwendet.

   ![Screenshot der Pipelineausführung unter Verwendung der YAML-Vorlage aus dem eShopSecurity-Repository](media/pipeline-execution-using-template.png)

Nun befindet sich die YAML-Vorlage in einem separaten Repository und Projekt. Sie können diese Datei in anderen Pipelines in Szenarien wiederverwenden, in denen Sie dieselben Ressourcen bereitstellen müssen. Außerdem kann Ihr Betriebsteam die Ressourcengruppe, den Speicherort, die Sicherheit und den Standort, an dem die Ressourcen bereitgestellt werden, sowie andere Informationen durch eine Änderung der Werte in der YAML-Datei steuern, ohne dass Sie Änderungen an Ihrer Pipeline-Definition vornehmen müssen.

> [!IMPORTANT]
> Denken Sie daran, die im Azure-Portal erstellten Ressourcen zu löschen, um unnötige Gebühren zu vermeiden.

## Überprüfung

In diesem Lab haben Sie gelernt, wie Sie eine sichere Projekt- und Repositorystruktur in Azure DevOps konfigurieren und organisieren. Durch die effektive Verwaltung von Berechtigungen können Sie sicherstellen, dass die richtigen Benutzer*innen Zugriff auf die benötigten Ressourcen haben und gleichzeitig die Sicherheit und Integrität Ihrer DevOps-Pipelines und -Prozesse wahren.
