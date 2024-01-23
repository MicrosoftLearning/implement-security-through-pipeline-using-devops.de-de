---
lab:
  title: Verwaltete Identitäten für Projekte und Pipelines
  module: 'Module 2: Manage identity for projects, pipelines, and agents'
---

# Verwaltete Identitäten für Projekte und Pipelines

Verwaltete Identitäten bieten eine sichere Methode zum Steuern des Zugriffs auf Azure-Ressourcen. Azure verarbeitet diese Identitäten automatisch, sodass Sie den Zugriff auf Dienste überprüfen können, die mit der Azure AD-Authentifizierung kompatibel sind. Dies bedeutet, dass Sie keine Anmeldeinformationen in Ihren Code einbetten müssen, wodurch die Sicherheit erhöht wird. In Azure DevOps können verwaltete Identitäten Azure-Ressourcen innerhalb Ihrer selbstgehosteten Agents authentifizieren, wodurch die Zugriffssteuerung vereinfacht wird, ohne die Sicherheit zu beeinträchtigen.

In diese Lab erstellen Sie eine verwaltete Identität und verwenden diese in Azure DevOps YAML-Pipelines, die auf selbst gehosteten Agenten ausgeführt werden, um Azure-Ressourcen bereitzustellen.

Dieses Lab nimmt etwa **45** Minuten in Anspruch.

## Vorbereitung

Sie benötigen ein Azure-Abonnement, eine Azure DevOps-Organisation und die eShopOnWeb-Anwendung, um den Labs zu folgen.

- Folgen Sie den Schritten, um Ihre [Lab-Umgebung zu überprüfen](APL2001_M00_Validate_Lab_Environment.md).
- Vergewissern Sie sich, dass Sie über ein Microsoft- oder ein Microsoft Entra-Konto mit der Rolle „Mitwirkender“ oder „Besitzer“ im Azure-Abonnement verfügen. Ausführliche Informationen finden Sie in den Artikeln zum [Auflisten von Azure-Rollenzuweisungen mithilfe des Azure-Portals](https://learn.microsoft.com/azure/role-based-access-control/role-assignments-list-portal) und [Anzeigen und Zuweisen von Administratorrollen in Azure Active Directory](https://learn.microsoft.com/azure/active-directory/roles/manage-roles-portal).
- Schließen Sie das Lab [Konfigurieren von Agents und Agentpools für sichere Pipelines](APL2001_M03_L02_Configure_Agents_And_Agent_Pools_for_Secure_Pipelines.md) ab.

## Anweisungen

### Übung 1: Importieren und Ausführen von CI/CD-Pipelines

In dieser Übung werden Sie die CI-Pipeline importieren und ausführen, die Dienstverbindung mit Ihrem Azure-Abonnement konfigurieren und dann die CD-Pipeline importieren und ausführen.

#### Aufgabe 1: Importieren und Ausführen der CI-Pipeline

Beginnen wir mit dem Importieren der CI-Pipeline mit dem Namen [eshoponweb-ci.yml](https://github.com/MicrosoftLearning/eShopOnWeb/blob/main/.ado/eshoponweb-ci.yml).

1. Navigieren Sie zum Azure DevOps-Portal unter `https://dev.azure.com` und öffnen Sie Ihre Organisation.

1. Öffnen Sie das Projekt **eShopOnWeb**.

1. Navigieren Sie zu **Pipelines > Pipelines**.

1. Wählen Sie die Schaltfläche **Pipeline erstellen** aus.

1. Wählen Sie **Azure Repos Git (Yaml)**.

1. Wählen Sie das Repository **eShopOnWeb** aus.

1. Wählen Sie die Option **Vorhandene Azure Pipelines-YAML-Datei** aus.

1. Wählen Sie die Datei **/.ado/eshoponweb-ci.yml** aus und klicken Sie dann auf **Weiter**.

1. Klicken Sie auf die Schaltfläche **Ausführen**, um die Pipeline auszuführen.

   > [!NOTE]
   > Ihre Pipeline nimmt einen Namen auf der Grundlage des Projektnamens an. Benennen Sie sie um, um die Pipeline besser zu identifizieren.

1. Wechseln Sie zu **Pipelines > Pipelines**, wählen Sie zunächst die zuletzt erstellte Pipeline, dann die Auslassungspunkte und anschließend die Option **Umbenennen/Verschieben** aus.

1. Geben Sie ihr den Namen **eshoponweb-ci**, und wählen Sie **Speichern** aus.

#### Übung 2: Importieren und Ausführen der CD-Pipeline

> [!NOTE]
> In dieser Aufgabe werden die CD-Pipeline [eshoponweb-cd-webapp-code.yml](https://github.com/MicrosoftLearning/eShopOnWeb/blob/main/.ado/eshoponweb-cd-webapp-code.yml) importieren und ausführen.

1. Wählen Sie im Bereich **Pipelines** des Projekts **eShopOnWeb** die Schaltfläche **Neue Pipeline** aus.

1. Wählen Sie **Azure Repos Git (Yaml)**.

1. Wählen Sie das Repository **eShopOnWeb** aus.

1. Wählen Sie die Option **Vorhandene Azure Pipelines-YAML-Datei** aus.

1. Wählen Sie zunächst die Datei **/.ado/eshoponweb-cd-webapp-code.yml** und dann **Weiter** aus.

1. Legen Sie in der YAML-Pipelinedefinition im Abschnitt „Variablen“ Folgendes fest:

   ```yaml
   variables:
     resource-group: 'AZ400-EWebShop-NAME'
     location: 'southcentralus'
     templateFile: '.azure/bicep/webapp.bicep'
     subscriptionid: 'YOUR-SUBSCRIPTION-ID'
     azureserviceconnection: 'azure subs'
     webappname: 'az400-webapp-NAME'
   ```

1. Ersetzen Sie die Platzhalter im Abschnitt „Variablen“ durch die folgenden Werte:

   - **AZ400-EWebShop-NAME** durch den Namen Ihrer Einstellung, z. B. **rg-eshoponweb**.
   - **Standort** durch den Namen der Azure-Region, in der Sie Ihre Ressourcen bereitstellen möchten, z. B. **southcentralus**.
   - Ersetzen Sie **IHRE-ABONNEMENT-ID** durch Ihre Azure-Abonnement-ID.
   - **az400-webapp-NAME** durch einen global eindeutigen Namen der Web-App, die bereitgestellt werden soll, z. B. die Zeichenfolge **eshoponweb-lab-id-** gefolgt von einer zufälligen sechsstelligen Zahl. 

1. Wählen Sie zunächst **Speichern und ausführen** und dann den direkten Commit zu Mainbranch aus.

1. Wählen Sie erneut **Speichern und ausführen** aus.

1. Öffnen Sie die Pipeline. Wenn die Meldung „Diese Pipeline benötigt die Berechtigung zum Zugriff auf eine Ressource, bevor diese Ausführung mit der Bereitstellung an WebApp fortgesetzt werden kann“ angezeigt wird, wählen Sie **Anzeigen**, **Zulassen** und erneut **Zulassen** aus. Diese Berechtigung ist erforderlich, damit die Pipeline die Azure App Service-Ressource erstellen kann.

   ![Screenshot der Genehmigung des Zugriffs über die YAML-Pipeline](media/pipeline-deploy-permit-resource.png)

1. Die Bereitstellung kann einige Minuten dauern. Warten Sie, bis die Pipeline ausgeführt wird. Die CD-Definition besteht aus den folgenden Aufgaben:

   - **AzureResourceManagerTemplateDeployment**: Stellt die Azure App Service-Web-App mithilfe der Bicep-Vorlage bereit.
   - **AzureRmWebAppDeployment**: Veröffentlicht die Website in der Azure App Service-Web-App.

   > [!NOTE]
   > Wenn die Bereitstellung fehlschlägt, navigieren Sie zur Seite „Pipeline ausführen“, und wählen Sie **Fehlgeschlagene Aufträge erneut ausführen** aus, um eine weitere Pipelineausführung zu starten.

   > [!NOTE]
   > Ihre Pipeline bekommt einen Namen basierend auf dem Projektnamen. Wir **benennen sie um**, damit wir die Pipeline besser identifizieren können.

1. Wechseln Sie zu **Pipelines > Pipelines**, wählen Sie zunächst die zuletzt erstellte Pipeline, dann die Auslassungspunkte und anschließend die Option **Umbenennen/Verschieben** aus.

1. Geben Sie ihr den Namen **eshoponweb-cd-webapp-code**, und wählen Sie dann **Speichern** aus.

### Übung 2: Konfigurieren der verwalteten Identität in Azure-Pipelines

In dieser Übung verwenden Sie eine verwaltete Identität, um eine neue Dienstverbindung zu konfigurieren und sie in die CI/CD-Pipelines einzubinden.

#### Aufgabe 1: Konfigurieren eines selbst gehosteten Agents für die Verwendung der verwalteten Identität und Aktualisieren der CI-Pipeline

1. Öffnen Sie das Azure-Portal in Ihrem Browser auf `https://portal.azure.com`.

1. Navigieren Sie im Azure-Portal zu der Seite, auf der der virtuelle Azure-Computer **eshoponweb-vm** angezeigt wird, den Sie in diesem Lab bereitgestellt haben.

1. Wählen Sie auf der Seite des virtuellen Azure-Computers **eshoponweb-vm** auf der Symbolleiste die Option **Starten** aus, um ihn zu starten.

1. Wählen Sie auf der Seite **eshoponweb-vm** der Azure VM im vertikalen Menü auf der linken Seite im Abschnitt **Sicherheit** die Option **Identität**aus.

1. Überprüfen Sie auf der Seite **eshoponweb-vm /| Identität**, ob der **Status** auf **Ein** festgelegt ist, und wählen Sie **Azure-Rollenzuweisungen** aus.

1. Wählen Sie die Schaltfläche **Rollenzuweisung hinzufügen** aus, und führen Sie die folgenden Aktionen aus:

   | Einstellung | Aktion |
   | -- | -- |
   | Dropdownliste **Bereich** | Wählen Sie **Abonnement** aus. |
   | Dropdownliste **Abonnement** | Wählen Sie Ihr Azure-Abonnement. |
   | Dropdownliste **Rolle** | Wählen Sie die Rolle **Mitwirkender** aus. |

   > [!NOTE]
   > Der Abonnementbereich ist für die Bereitstellungen in den nachfolgenden Labs erforderlich.

1. Wählen Sie die Schaltfläche **Speichern** aus.

    ![Screenshot des Bereichs „Rollenzuweisung hinzufügen“](media/add-role-assignment.png)

#### Aufgabe 2: Erstellen einer auf einer verwalteten Identität basierenden Dienstverbindung

1. Wechseln Sie zum Webbrowser, in dem das Projekt **eShopOnWeb** im Azure DevOps-Portal auf `https://dev.azure.com`angezeigt wird.

1. Navigieren Sie im Projekt **eShopOnWeb** zu **Project-Einstellungen > Dienstverbindungen**.

1. Wählen Sie die Schaltfläche **Neue Dienstverbindung** und dann **Azure Resource Manager** aus.

1. Wählen Sie**Verwaltete Identität** für die **Authentifizierungsmethode** aus.

1. Legen Sie die Bereichsebene auf **Abonnement** fest, und stellen Sie die Informationen bereit, die während der Überprüfung der Labumgebung gesammelt wurden, einschließlich Abonnement-ID, Abonnementname und Mandanten-ID. 

1. Geben Sie in **Dienstverbindungsname** den Namen **azure subs managed** ein. Auf diesen Namen wird in YAML-Pipelines beim Zugriff auf Ihre Azure-Abonnement verwiesen.

1. Wählen Sie **Speichern**.

#### Aufgabe 3: Aktualisieren der CD-Pipeline

1. Wechseln Sie zu dem Bowserfenster, in dem das Projekt **eShopOnWeb** im Azure DevOps-Portal angezeigt wird.

1. Navigieren Sie auf der Projektseite **eShopOnWeb** zu **Pipelines > Pipelines**.

1. Wählen Sie zunächst die Pipeline **eshoponweb-cd-webapp-code** und dann **Bearbeiten** aus.

1. Aktualisieren Sie im Abschnitt „Variablen“ die Variable **serviceConnection**, sodass der Name der Dienstverbindung, die Sie in der vorherigen Aufgabe erstellt haben, verwendet wird – **azure subs managed**.

   ```yaml
     azureserviceconnection: 'azure subs managed'
   ```

1. Aktualisieren Sie im Unterabschnitt **Aufträge** des Abschnitts **Phasen** den Wert der Eigenschaft **Pool**, um auf den selbstgehosteten Agentpool **eShopOnWebSelfPool** zu verweisen, den Sie im vorigen Lab erstellt haben, sodass er das folgende Format aufweist:

   ```yaml
     jobs:
     - job: Deploy
       pool: eShopOnWebSelfPool
       steps:
       #download artifacts
       - download: eshoponweb-ci
   ```

1. Wählen Sie zunächst **Speichern** und dann den direkten Commit zu Mainbranch aus.

1. Wählen Sie erneut **Speichern** aus.

1. Wählen Sie die Option zum **Ausführen** der Pipeline aus, und klicken Sie dann erneut auf **Ausführen**.

1. Öffnen Sie die Pipeline. Wenn die Meldung „Diese Pipeline benötigt die Berechtigung zum Zugriff auf eine Ressource, bevor diese Ausführung mit der Bereitstellung an WebApp fortgesetzt werden kann“ angezeigt wird, wählen Sie **Anzeigen**, **Zulassen** und erneut **Zulassen** aus. Diese Berechtigung ist erforderlich, damit die Pipeline die Azure App Service-Ressource erstellen kann.

1. Die Bereitstellung kann einige Minuten dauern. Warten Sie, bis die Pipeline ausgeführt wird.

1. Sie sollten aus den Pipelineprotokollen ersehen können, dass die Pipeline die verwaltete Identität verwendet.

   ![Screenshot der Pipelineprotokolle bei Verwendung der verwalteten Identität](media/pipeline-logs-managed-identity.png)

   > [!NOTE]
   > Nach Abschluss der Pipelineausführung können Sie das Azure-Portal verwenden, um den Status der App Service-Web-App-Ressourcen zu überprüfen.

### Übung 3: Bereinigung von Azure- und Azure DevOps-Ressourcen

In dieser Übung bereinigen Sie nach der Durchführung des Labs einige Azure- und Azure DevOps-Ressourcen, die in diesem Lab erstellt wurden.

#### Aufgabe 1: Beenden des virtuellen Azure-Computers und Aufheben seiner Zuordnung

1. Navigieren Sie zu der Seite, auf der die Ressourcengruppe **rg-eshoponweb-** angezeigt wird, und wählen Sie **Ressourcengruppe löschen** aus, um alle zugehörigen Ressourcen zu löschen.

   > [!IMPORTANT]
   > Löschen Sie die Ressourcengruppe **rg-eshoponweb-agentpool** nicht, die die selbst gehosteten Agentressourcen enthält. Sie benötigen diese im nächsten Lab.

1. Navigieren Sie im Azure-Portal zu der Seite, auf der der virtuelle Azure-Computer **eshoponweb-vm** angezeigt wird, den Sie in diesem Lab bereitgestellt haben.

1. Wählen Sie auf der Seite des virtuellen Azure-Computers **eshoponweb-vm** auf der Symbolleiste die Option **Beenden** aus, um ihn zu beenden und seine Zuordnung aufzuheben.

#### Aufgabe 2: Entfernen von Azure DevOps-Pipelines

1. Navigieren Sie zum Azure DevOps-Portal unter `https://dev.azure.com` und öffnen Sie Ihre Organisation.

1. Öffnen Sie das Projekt **eShopOnWeb**.

1. Navigieren Sie zu **Pipelines > Pipelines**.

1. Wechseln Sie zu **Pipelines > Pipelines**, und löschen Sie die vorhandenen Pipelines.

   > [!IMPORTANT]
   > Löschen Sie nicht die Dienstverbindung und den Agentpool, den Sie in diesem Lab erstellt haben. Sie benötigen beide im nächsten Lab.

#### Aufgabe 3: Neuerstellen des Azure DevOps-Repositorys

1. Wählen Sie im Azure DevOps-Portal im Projekt **eShopOnWeb-** in der unteren linken Ecke die Option **Projekteinstellungen** aus.

1. Wählen Sie im vertikalen Menü **Projekteinstellungen** auf der linken Seite im Abschnitt **Repositorys** die Option **Repositorys** aus.

1. Zeigen Sie im Bereich **Alle Repositorys** mit der Maus auf das rechte Ende des Repositoryeintrags **eShopOnWeb**, bis das Symbol mit den Auslassungszeichen **Weitere Optionen** angezeigt wird, wählen Sie es aus, und wählen Sie im Menü **Weitere Option** die Option **Umbenennen** aus.  

1. Geben Sie im Fenster **eShopOnWeb-Repository umbenennen** im Textfeld **Repositoryname** den Text **eShopOnWeb_old** ein, und wählen Sie **Umbenennen** aus.

1. Wählen Sie im Bereich **Alle Repositorys** die Option **+ Erstellen** aus.

1. Geben Sie im Bereich **Repository erstellen** im Textfeld **Repositoryname** den Text **eShopOnWeb** ein, deaktivieren Sie das Kontrollkästchen **README hinzufügen**, und wählen Sie **Erstellen** aus.

1. Zeigen Sie im Bereich **Alle Repositorys** mit der Maus auf des rechte Ende des **eShopOnWeb_old**-Repositoryeintrags, bis das Symbol mit den Auslassungszeichen **Weitere Optionen** angezeigt wird, wählen Sie es aus, und wählen Sie im Menü **Weitere Option** die Option **Umbenennen** aus.  

1. Geben Sie im Fenster **eShopOnWeb_old-Repository löschen** den Text **eShopOnWeb_old** ein, und wählen Sie **Löschen** aus.

1. Wählen Sie im linken Navigationsmenü des Azure DevOps-Portals die Option **Repositorys** aus.

1. Wählen Sie im Bereich **eShopOnWeb ist leer. Code hinzufügen!** die Option **Repository importieren** aus.

1. Fügen Sie im Fenster **Git-Repository importieren** die folgende URL `https://github.com/MicrosoftLearning/eShopOnWeb` ein, und wählen Sie **Importieren** aus:

## Überprüfung

In dieser Übung haben Sie erfahren, wie Sie eine verwaltete Identität verwenden, die selbst gehosteten Agents in Azure DevOps YAML-Pipelines zugeordnet ist.