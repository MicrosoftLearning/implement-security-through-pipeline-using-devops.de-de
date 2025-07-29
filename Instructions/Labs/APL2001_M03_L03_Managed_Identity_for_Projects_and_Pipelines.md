---
lab:
  title: Verwaltete Identitäten für Projekte und Pipelines
  module: 'Module 3: Manage identity for projects, pipelines, and agents'
---

# Verwaltete Identitäten für Projekte und Pipelines

Verwaltete Identitäten bieten eine sichere Methode zum Steuern des Zugriffs auf Azure-Ressourcen. Azure verarbeitet diese Identitäten automatisch, sodass Sie den Zugriff auf Dienste überprüfen können, die mit der Azure AD-Authentifizierung kompatibel sind. Dies bedeutet, dass Sie keine Anmeldeinformationen in Ihren Code einbetten müssen, wodurch die Sicherheit erhöht wird. In Azure DevOps können verwaltete Identitäten Azure-Ressourcen innerhalb Ihrer selbstgehosteten Agents authentifizieren, wodurch die Zugriffssteuerung vereinfacht wird, ohne die Sicherheit zu beeinträchtigen.

In diese Lab erstellen Sie eine verwaltete Identität und verwenden diese in Azure DevOps YAML-Pipelines, die auf selbst gehosteten Agenten ausgeführt werden, um Azure-Ressourcen bereitzustellen.

Dieses Lab nimmt etwa **30** Minuten in Anspruch.

## Vor der Installation

Sie benötigen ein Azure-Abonnement, eine Azure DevOps-Organisation und die eShopOnWeb-Anwendung, um den Labs zu folgen.

- Vergewissern Sie sich, dass Sie über ein Microsoft- oder ein Microsoft Entra-Konto mit der Rolle „Mitwirkender“ oder „Besitzer“ im Azure-Abonnement verfügen. Ausführliche Informationen finden Sie in den Artikeln zum [Auflisten von Azure-Rollenzuweisungen mithilfe des Azure-Portals](https://learn.microsoft.com/azure/role-based-access-control/role-assignments-list-portal) und [Anzeigen und Zuweisen von Administratorrollen in Azure Active Directory](https://learn.microsoft.com/azure/active-directory/roles/manage-roles-portal).

## Voraussetzungen

Abschluss des Labs

- Folgen Sie den Schritten, um Ihre [Lab-Umgebung zu überprüfen](APL2001_M00_Validate_Lab_Environment.md).
- [Konfigurieren einer Projekt- und Repositorystruktur zur Unterstützung sicherer Pipelines](APL2001_M01_L01_Configure_a_Project_and_Repository_Structure_to_Support_Secure_Pipelines.md)
- [Konfigurieren von Agents und Agentpools für sichere Pipelines](APL2001_M02_L02_Configure_Agents_And_Agent_Pools_for_Secure_Pipelines.md)

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

### Übung 1: Konfigurieren der verwalteten Identität in Azure Pipelines

In dieser Übung verwenden Sie eine verwaltete Identität, um eine neue Dienstverbindung zu konfigurieren und sie in die CI/CD-Pipelines einzubinden.

#### Aufgabe 1: Einrichten der verwalteten Identität im Azure-Abonnement

1. Öffnen Sie in Ihrem Browser das Azure-Portal unter `https://portal.azure.com`.

1. Navigieren Sie im Azure-Portal zu der Seite, auf der der virtuelle Azure-Computer **eshoponweb-vm** angezeigt wird, den Sie im [vorherigen Lab](APL2001_M02_L02_Configure_Agents_And_Agent_Pools_for_Secure_Pipelines.md) bereitgestellt haben.

1. Wählen Sie auf der Seite „Azure VM“ von **eshoponweb-vm** auf der Symbolleiste **Start** aus, um sie zu starten, falls sie angehalten wurde.

1. Wählen Sie auf der Seite **eshoponweb-vm** der Azure VM im vertikalen Menü auf der linken Seite im Abschnitt **Sicherheit** die Option **Identität**aus.

1. Überprüfen Sie auf der Seite **Identity**, ob der **Status** auf **On** gesetzt ist, und wählen Sie die Option **Azure role assignments** aus.

1. Wählen Sie die Schaltfläche **Rollenzuweisung hinzufügen** aus, und führen Sie die folgenden Aktionen aus:

   | Einstellung | Aktion |
   | -- | -- |
   | Dropdownliste **Bereich** | Wählen Sie **Abonnement** aus. |
   | Dropdownliste **Abonnement** | Wählen Sie Ihr Azure-Abonnement. |
   | Dropdownliste **Rolle** | Wählen Sie die Rolle **Mitwirkender** aus. |

   > **Hinweis**: Der Abonnementbereich ist für die Bereitstellungen in den nachfolgenden Labs erforderlich.

1. Wählen Sie die Schaltfläche **Speichern** aus.

    ![Screenshot des Bereichs „Rollenzuweisung hinzufügen“](media/add-role-assignment.png)

#### Aufgabe 2: Erstellen einer auf einer verwalteten Identität basierenden Dienstverbindung

1. Wechseln Sie zum Webbrowser, in dem das Projekt **eShopOnWeb** im Azure DevOps-Portal auf `https://aex.dev.azure.com`angezeigt wird.

1. Navigieren Sie im Projekt **eShopOnWeb** zu **Project-Einstellungen > Dienstverbindungen**.

1. Wählen Sie die Schaltfläche **Neue Dienstverbindung** und dann **Azure Resource Manager** aus.

1. Wählen Sie **Verwaltete Identität (Agent-seitig zugewiesen)** für die **Authentifizierungsmethode** aus.

1. Stellen Sie die Bereichsebene auf **Abonnement** ein und geben Sie die Informationen aus dem Azure-Portal an, einschließlich der **Abonnement-ID**, des **Abonnementnamens** und der **Mandanten-ID**.

   > **Hinweis**: Sie finden die **Subscription Id** im Azure-Portal, indem Sie zum **Subscriptions**-Blade navigieren und die von Ihnen verwendete Subscription auswählen. Die **Mandanten-ID** ist im **Microsoft Entra ID**-Blade zu finden.

1. Geben Sie in **Dienstverbindungsname** den Namen **azure subs managed** ein. Auf diesen Namen wird in YAML-Pipelines beim Zugriff auf Ihre Azure-Abonnement verwiesen.

1. Wählen Sie **Speichern**.

#### Aufgabe 3: Aktualisieren der CI-Pipeline mit dem selbstgehosteten Agentpool

In dieser Aufgabe aktualisieren Sie die CI-Pipeline, um den selbst gehosteten Agentpool zu verwenden.

1. Wechseln Sie zu dem Bowserfenster, in dem das Projekt **eShopOnWeb** im Azure DevOps-Portal angezeigt wird.

1. Navigieren Sie auf der Projektseite **eShopOnWeb** zu **Pipelines > Pipelines**.

1. Wählen Sie zunächst die **eshoponweb-ci**-Pipeline und dann **Bearbeiten** aus.

1. Aktualisieren Sie im Unterabschnitt **Aufträge** des Abschnitts **Phasen** den Wert der **Pool**-Eigenschaft, um auf den selbstgehosteten Agentpool **eShopOnWebSelfPool** zu verweisen, den Sie in dieser Aufgabe konfiguriert haben, sodass er das folgende Format aufweist:

   ```yaml
     jobs:
     - job: Build
       pool: eShopOnWebSelfPool
       steps:
       - task: DotNetCoreCLI@2
   ```

1. Wählen Sie zunächst **Speichern und ausführen** und dann den direkten Commit zum Mainbranch aus.

1. Wählen Sie erneut **Speichern** aus.

1. Wählen Sie die Option zum **Ausführen** der Pipeline aus, und klicken Sie dann erneut auf die Option **Ausführen**.

1. Stellen Sie sicher, dass der Buildauftrag auf dem **eShopOnWebSelfAgent**-Agent ausgeführt und erfolgreich abgeschlossen wird.

    > **Hinweis**: Wenn die Meldung **Die Agent-Anforderung wird nicht ausgeführt, da alle potenziellen Agents andere Anforderungen ausführen. Aktuelle Position in der Warteschlange: 1** angezeigt wird, können Sie warten, bis der Agent verfügbar ist, oder Sie können den Agentauftrag anhalten, der ausgeführt wird. Möglicherweise handelt es sich um die CD-Pipeline, die automatisch ausgeführt wird.

    > **Hinweis**: Wenn auf der Seite zur Pipelineausführung die Meldung „Diese Pipeline benötigt die Berechtigung zum Zugriff auf eine Ressource, bevor diese Ausführung mit dem Build .Net Core Solution fortfahren kann“ angezeigt wird, wählen Sie **Anzeigen**, **Zulassen** und erneut **Zulassen** aus. Dies ist erforderlich, damit die Pipeline den selbst gehosteten Agentpool verwenden kann.

#### Aufgabe 4: Aktualisieren der CD-Pipeline zur Nutzung des selbstgehosteten Agentpools und der verwalteten identitätsbasierten Dienstverbindung

In dieser Aufgabe aktualisieren Sie die CD-Pipeline, um die verwaltete identitätsbasierte Dienstverbindung und den selbst gehosteten Agentpool zu verwenden.

1. Wechseln Sie zu dem Browserfenster, in dem das Projekt **eShopSecurity** im Azure DevOps-Portal angezeigt wird.

   > **Hinweis**: **eShopSecurity** ist der Name des Projekts, das Sie im [ersten Lab](APL2001_M01_L01_Configure_a_Project_and_Repository_Structure_to_Support_Secure_Pipelines.md) erstellt haben.

1. Navigieren Sie auf der **eShopSecurity**-Projektseite zu **Repos > Dateien**.

1. Wählen Sie die **eshoponweb-secure-variables.yml**-Datei aus, und klicken Sie auf die Schaltfläche **Bearbeiten**.

1. Aktualisieren Sie im Abschnitt „Variablen“ die Variable **azureserviceconnection**, sodass der Name der Dienstverbindung verwendet wird, die Sie in der vorherigen Aufgabe erstellt haben – **azure subs managed**.

   ```yaml
     azureserviceconnection: 'azure subs managed'
   ```

1. Klicken Sie auf die Schaltfläche **Commit**, und wählen Sie den direkten Commit zum Mainbranch aus.

1. Klicken Sie erneut auf die Schaltfläche **Commit**.

1. Wechseln Sie zum Projekt **eShopOnWeb**.

1. Navigieren Sie auf der Projektseite **eShopOnWeb** zu **Pipelines > Pipelines**.

1. Wählen Sie zunächst die Pipeline **eshoponweb-cd-webapp-code** und dann **Bearbeiten** aus.

1. Aktualisieren Sie im Unterabschnitt **Aufträge** des Abschnitts **Phasen** den Wert der Eigenschaft **Pool**, um auf den selbstgehosteten Agentpool **eShopOnWebSelfPool** zu verweisen, den Sie im vorigen Lab erstellt haben, sodass er das folgende Format aufweist:

   ```yaml
     jobs:
     - job: Deploy
       pool: eShopOnWebSelfPool
       steps:
       #download artifacts
       - download: eshoponweb-ci
   ```

1. Klicken Sie auf die Schaltfläche **Überprüfen und speichern**, und wählen Sie den direkten Commit zum Mainbranch aus.

1. Klicken Sie erneut auf **Speichern**.

1. Navigieren Sie zu **Pipelines > Pipelines**, und wählen Sie die **eshoponweb-cd-webapp-code**-Pipeline aus, die bereits aus der vorherigen Aufgabe ausgeführt wird.

1. Klicken Sie auf die Pipelineausführung und dann auf **Abbrechen**. Klicken Sie zur Bestätigung auf die Schaltfläche **Ja**.

   > **Hinweis**: Sie führen die Systemdiagnose zur Pipelineaktivierung aus, um die Protokolle der Pipeline anzuzeigen.

1. Klicken Sie auf **Neue Pipeline ausführen**, aktivieren Sie das Kontrollkästchen „Systemdiagnose aktivieren“, und klicken Sie auf die Schaltfläche **Ausführen**.

1. Öffnen Sie die Pipelineausführung.

   > **Hinweis**: Wenn die Meldung „Diese Pipeline benötigt die Berechtigung zum Zugriff auf zwei Ressourcen, bevor diese Ausführung mit der Bereitstellung an WebApp fortgesetzt werden kann“ angezeigt wird, wählen Sie für alle Ressourcen **Anzeigen**, **Zulassen** und erneut **Zulassen** aus. Dies ist erforderlich, damit die Pipeline die Dienstverbindung und den selbst gehosteten Agentpool verwenden kann.

1. Die Bereitstellung kann einige Minuten dauern. Warten Sie, bis die Pipeline ausgeführt wird.

   > [!IMPORTANT]
   > Wenn die Pipeline aufgrund des AZ CLI-Fehlers fehlschlägt, müssen Sie Ihren selbst gehosteten Agent möglicherweise neu starten und die Pipeline erneut ausführen.
   > Um den Agent neu zu starten, navigieren Sie im Azure-Portal zu der Seite, auf der die Azure VM **eshoponweb-vm** angezeigt wird, die Sie im vorherigen Lab bereitgestellt haben, stellen Sie mithilfe der Schaltfläche **Verbinden** eine Verbindung mit dem virtuellen Computer her, und starten Sie den Namen des Azure Pipelines-Agent-Dienstes ab „vstsagent“ neu. Klicken Sie mit der rechten Maustaste auf den Agentdienst, und wählen Sie **Neu starten** aus.

1. Sie sollten aus den Pipelineprotokollen ersehen können, dass die Pipeline die verwaltete Identität verwendet.

   ![Screenshot der Pipelineprotokolle bei Verwendung der verwalteten Identität](media/pipeline-logs-managed-identity.png)

   > **Hinweis**: Nach Abschluss der Pipelineausführung können Sie das Azure-Portal verwenden, um den Status der App Service Web App-Ressourcen zu überprüfen.

   > [!IMPORTANT]
   > Denken Sie daran, die im Azure-Portal erstellten Ressourcen zu löschen, um unnötige Gebühren zu vermeiden.

## Überprüfung

In dieser Übung haben Sie erfahren, wie Sie eine verwaltete Identität verwenden, die selbst gehosteten Agents in Azure DevOps YAML-Pipelines zugeordnet ist.
