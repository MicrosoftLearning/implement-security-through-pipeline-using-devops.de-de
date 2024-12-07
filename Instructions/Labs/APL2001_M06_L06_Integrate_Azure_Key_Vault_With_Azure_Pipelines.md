---
lab:
  title: Integration von Azure Key Vault mit Azure Pipelines
  module: 'Module 6: Configure secure access to Azure Repos from pipelines'
---

# Integration von Azure Key Vault mit Azure Pipelines

Azure Key Vault bietet eine sichere Speicherung und Verwaltung von vertraulichen Daten, wie z. B. Schlüsseln, Passwörtern und Zertifikaten. Azure Key Vault unterstützt Hardware-Sicherheitsmodule und eine Reihe von Verschlüsselungsalgorithmen und Schlüssellängen. Mit Azure Key Vault können Sie die potenzielle Offenlegung vertraulicher Daten über den Quellcode minimieren, was einen häufigen Fehler von Entwicklern darstellt. Der Zugriff auf Azure Key Vault erfordert eine ordnungsgemäße Authentifizierung und Autorisierung, die fein gestrichelte Berechtigungen für seine Inhalte unterstützt.

In diesem Lab erfahren Sie, wie Sie Azure Key Vault mit Hilfe der folgenden Schritte in Azure Pipelines integrieren können:

- Erstellen Sie einen Azure Key Vault, um ein ACR-Kennwort als Geheimnis zu speichern.
- Konfigurieren Sie die Berechtigungen, damit der Dienstprinzipal das Geheimnis lesen kann.
- Konfigurieren Sie die Pipeline, sodass das Kennwort von Azure Key Vault abgerufen und an nachfolgende Aufgaben weitergegeben wird.

Diese Übungen nehmen ungefähr **30** Minuten in Anspruch.

## Vorbereitung

Sie benötigen ein Azure-Abonnement, eine Azure DevOps-Organisation und die eShopOnWeb-Anwendung, um den Labs zu folgen.

- Folgen Sie den Schritten, um Ihre [Lab-Umgebung zu überprüfen](APL2001_M00_Validate_Lab_Environment.md).

Dieses Lab deckt Folgendes ab:

- Ressourcen in Ihrem Azure-Abonnement bereitstellen.
- Erhalten von Lesezugriff auf Azure Key Vault-Geheimnisse.

## Anweisungen

### Übung 1: Einrichten einer CI-Pipeline zur Erstellung des eShopOnWeb-Containers

In dieser Übung richten Sie eine CI YAML-Pipeline zu folgendem Zweck ein:

- Erstellen einer Azure Container Registry zum Speichern der Containerimages
- Verwendung von Docker Compose zum Erstellen und Verteilen von **eshoppublicapi** und **eshopwebmvc** Container Images. Es wird nur der Container**eshopwebmvc**eingesetzt.

#### Aufgabe 1: Einrichten und Ausführen der CI-Pipeline

In dieser Aufgabe werden Sie eine vorhandene CI YAML-Pipelinedefinition importieren, ändern und ausführen. Die Pipeline erstellt eine Azure Container Registry (ACR) und erstellt/veröffentlicht die eShopOnWeb-Containerimages.

1. Navigieren Sie zum Azure DevOps-Portal unter `https://aex.dev.azure.com` und öffnen Sie Ihre Organisation.

1. Navigieren Sie zum Azure DevOps-Projekt **eShopOnWeb**. Wechseln Sie zu **Pipelines > Pipelines** und wählen Sie **Neue Pipeline** aus.

1. Wählen Sie auf der Seite **Wo befindet sich Ihr Code?** den Eintrag **Azure Repos Git (YAML)** und anschließend das Repository **eShopOnWeb** aus.

1. Wählen Sie auf der Registerkarte **Pipeline konfigurieren** die Option **Vorhandene Azure Pipelines-YAML-Datei** aus. Geben Sie den folgenden Pfad **/.ado/eshoponweb-ci-dockercompose.yml** an, und wählen Sie **Weiter**aus.

   ![Screenshot der Pipelineauswahl aus der YAML-Datei](media/select-ci-container-compose.png)

1. Führen Sie in der YAML-Pipelinedefinition im Abschnitt „Variablen“ folgende Aktionen aus:

   - Ersetzen Sie **AZ400-EWebShop-NAME** durch **rg-eshoponweb-secure**
   - Legen Sie den Wert der Standortvariable auf den Namen einer Azure-Region fest, die Sie in den vorherigen Übungen dieses Kurses verwendet haben (z. B. **centralus**)
   - Ersetzen Sie **YOUR-SUBSCRIPTION-ID** durch Ihre Azure-Abonnement-ID

1. Wählen Sie zunächst **Speichern und ausführen** und dann den direkten Commit zur Mainbranch aus.

1. Wählen Sie erneut **Speichern und ausführen** aus.

   > **Hinweis**: Wenn Sie sich entscheiden, eine neue Verzweigung zu erstellen, müssen Sie einen Pull-Request erstellen, um die Änderungen in die Hauptverzweigung einzubringen.

1. Öffnen Sie die Pipeline. Wenn Sie die Meldung „Diese Pipeline benötigt die Erlaubnis, auf eine Ressource zuzugreifen, bevor dieser Lauf fortgesetzt werden kann, um ACR für Bilder zu erstellen“ sehen, wählen Sie **Ansicht**, **Erlaubnis** aus und klicken erneut auf **Erlaubnis**. Die Pipeline wird in ein paar Minuten ausgeführt.

   ![Screenshot der Genehmigung des Zugriffs über die YAML-Pipeline](media/pipeline-permit-resource.png)

1. Warten Sie, bis die Pipelineausführung abgeschlossen ist. Dieser Vorgang kann einige Minuten in Anspruch nehmen. Die Buildpipeline besteht aus den folgenden Aufgaben:

     - **AzureResourceManagerTemplateDeployment** verwendet **bicep** zum Erstellen einer Azure Container-Registrierung.
     - **PowerShell** nimmt die Bicep-Ausgabe (acr login server) und erstellt eine Pipeline-Variable.
     - Die Aufgabe **DockerCompose** erstellt und pusht die Containerimages für eShopOnWeb in die Azure Container-Registrierung.

1. Der Name Ihrer Pipeline basiert standardmäßig auf dem Projektnamen. Benennen Sie die Pipeline in **eshoponweb-ci-dockercompose** um, damit sie leichter zu identifizieren ist.

1. Wenn die Pipelineausführung abgeschlossen ist, verwenden Sie den Webbrowser, um zum Azure-Portal zu navigieren, öffnen Sie die Ressourcengruppe **rg-eshoponweb-secure** und wählen Sie den Eintrag aus, der die Azure Container Registry (ACR) darstellt, die von der Pipeline bereitgestellt wird.

   > **Hinweis**: Damit Sie Repositorys in der Registrierung anzeigen können, müssen Sie Ihrem Benutzerkonto eine Rolle gewähren, die einen solchen Zugriff ermöglicht. Zu diesem Zweck verwenden Sie die Rolle „AcrPull“.

1. Wählen Sie auf der Seite „Containerregistrierung“ zunächst **Zugriffssteuerung (IAM)**, dann **+ Hinzufügen** und im Dropdownmenü **Rollenzuweisung hinzufügen** aus.

1. Wählen Sie auf der Registerkarte **Rolle** der Seite **Rollenzuweisung hinzufügen** die Option **AcrPull** und anschließend **Weiter** aus.

1. Klicken Sie auf der Registerkarte **Mitglieder** auf **+ Mitglieder auswählen** und dann auf **Auswählen**, und wählen Sie anschließend **Weiter** aus.

1. Wählen Sie **Überprüfen + Zuweisen** aus, und aktualisieren Sie nach erfolgreicher Ausführung der Zuweisung die Browserseite.

1. Wählen Sie auf der Seite „Containerregistrierung“ in der vertikalen Menüleiste auf der linken Seite im Abschnitt **Services** den Eintrag **Repositorys** aus.

1. Vergewissern Sie sich, dass die Registrierung die Images **eshoppublicapi** und **eshopwebmvc** enthält. In der Bereitstellungsphase verwenden Sie lediglich **eshopwebmvc**.

   ![Screenshot der Containerimages in ACR aus dem Azure-Portal](media/azure-container-registry.png)

1. Wählen Sie unter **Einstellungen** die Option **Zugangsschlüssel**, aktivieren Sie das Kontrollkästchen **Admin-Benutzer** und kopieren Sie die Werte **Benutzername** und **Registrierungsname**, die in der folgenden Aufgabe verwendet werden, da Sie sie als Geheimnis zum Azure Key Vault hinzufügen werden.

   ![Screenshot des ACR-Kennworts aus der Einstellung „Zugriffsschlüssel“](media/acr-password.png)

1. Notieren Sie den Wert **Registrierungsnamen** auf derselben Seite. Sie benötigen diese später in diesem Lab.

#### Aufgabe 2: Erstellen eines Azure Key Vault

In dieser Aufgabe werden Sie einen Azure Key Vault mithilfe des Azure-Portals erstellen.

In diesem Labszenario verfügen wir über eine Instanz von Azure Container Instances (ACI), die ein Containerimage abruft und ausführt, das in Azure Container Registry (ACR) gespeichert ist. Wir möchten das Kennwort für die ACR als Geheimnis im Azure Key Vault speichern.

1. Geben Sie im Azure-Portal in das Textfeld **Ressourcen, Dienste und Dokumente suchen** **Schlüsseltresore** ein und drücken Sie die Taste **Eingabe**.

1. Wählen Sie den **Schlüsseltresor**-Blade, klicken Sie auf **Schlüsseltresor erstellen**.

1. Geben Sie auf der Registerkarte **Allgemein** des Blatts **Key Vault erstellen** die folgenden Einstellungen an und klicken Sie auf **Weiter**:

   | Einstellung | Wert |
   | --- | --- |
   | Subscription | Der Name des Azure-Abonnements, das Sie in diesem Lab verwenden. |
   | Resource group | der Ressourcengruppenname **rg-eshoponweb-secure** |
   | Name des Schlüsseltresors | ein beliebiger eindeutiger gültiger Name, wie **ewebshop-kv** |
   | Region | dieselbe Azure-Region, die Sie zuvor in diesem Lab verwendet haben |
   | Tarif | **Standard** |
   | Aufbewahrungsdauer für gelöschte Tresore in Tagen | **7** |
   | Bereinigungsschutz | **Bereinigungsschutz deaktivieren** |

1. Klicken Sie auf **Weiter: Access-Konfiguration**.

1. Wählen Sie **Tresorzugriffsrichtlinie** im Abschnitt **Berechtigungsmodell**.

1. Wählen Sie im Abschnitt **Zugriffsrichtlinien** **+ Erstellen** aus, um eine neue Richtlinie einzurichten.

   > **Hinweis**: Sie müssen den Zugriff auf Ihre Key Vaults sichern, indem Sie nur autorisierte Anwendungen und Benutzer*innen zulassen. Um auf die Daten aus dem Vault zuzugreifen, müssen Sie dem zuvor erstellten Dienstprinzipal, den Sie für die Authentifizierung in der Pipeline verwenden werden, Leseberechtigungen (Get/List) erteilen.

   - Überprüfen Sie auf dem Blatt **Berechtigung** die Berechtigungen **Get** und **List** unterhalb von **Geheimnis- Berechtigung**. Wählen Sie **Weiter** aus.
   - Auf dem **Hauptblatt** suchen und wählen Sie Ihren Benutzer aus, wählen Sie **Weiter** und klicken erneut auf **Weiter**.
   - Wählen Sie auf dem Blatt **Überprüfen + erstellen** die Option **Erstellen** aus.

1. Kehren Sie zum Blatt **Schlüsseltresor erstellen** zurück, und wählen Sie **Überprüfen und erstellen > Erstellen** aus.

   > **Hinweis**: Warten Sie darauf, dass der Azure Key Vault bereitgestellt wird. Das sollte weniger als eine Minute dauern.

1. Wählen Sie auf dem Blatt **Ihre Bereitstellung wurde abgeschlossen** die Option **Zu Ressource wechseln** aus.

1. Wählen Sie auf dem Blatt „Azure Key Vault“ im vertikalen Menü auf der linken Seite des Blatts im Abschnitt **Objekte** **Geheimnisse** aus.

1. Wählen Sie auf dem Blatt **Geheimnis** die Option **+ Generieren/importieren** aus.

1. Legen Sie auf dem Blatt **Geheimnis erstellen** die folgenden Einstellungen fest, und wählen Sie **Erstellen** aus (Standardwerte für die anderen Einstellungen übernehmen):

   | Einstellung | Wert |
   | --- | --- |
   | Uploadoptionen | **Manuell** |
   | Name | **acr-secret** |
   | Wert | ACR-Zugriffskennwort, das in der vorherigen Aufgabe kopiert wurde |

1. Warten Sie, bis das Geheimnis erstellt wurde.

#### Aufgabe 3: Erstellen einer mit Azure Key Vault verbundenen Variablengruppe

In dieser Aufgabe erstellen Sie eine variable Gruppe in Azure DevOps, die das ACR-Kennwortgeheimnis aus Key Vault über die Dienstverbindung (Dienstprinzipal) abruft

1. Navigieren Sie zum Azure DevOps-Portal unter `https://aex.dev.azure.com` und öffnen Sie Ihre Organisation.

1. Navigieren Sie zum Azure DevOps-Projekt **eShopOnWeb**.

1. Wählen Sie im vertikalen Navigationsbereich des Azure DevOps-Portals **Pipelines > Bibliothek**aus. Wählen Sie **+ Variablengruppe** aus.

1. Geben Sie auf dem Blatt **Neue Variablengruppe** die folgenden Einstellungen an:

   | Einstellung | Wert |
   | --- | --- |
   | Variablengruppenname | **eshopweb-vg** |
   | Verknüpfen von Geheimnisse von Azure Key Vault als Variablen | **enable** |
   | Azure-Abonnement | **Verfügbare Azure-Dienstverbindung > azure subs** |
   | Name des Schlüsseltresors | der Name, den Sie dem Azure Key Vault in der vorherigen Aufgabe zugewiesen haben |

1. Klicken Sie auf die Schaltfläche **Autorisieren**.

1. Wählen Sie unter **Variablen** **+ Hinzufügen** und dann das Geheimnis **acr-secret** aus. Klickan Sie auf **OK**.

1. Wählen Sie **Speichern**.

   ![Screenshot der Variablengruppenerstellung](media/vg-create.png)

#### Aufgabe 4: Einrichten der CD-Pipeline zum Bereitstellen von Containern in Azure Container Instances (ACI)

In dieser Aufgabe importieren Sie eine CD-Pipeline, passen sie an und führen sie aus, um das zuvor in einer Instanz von Azure Container Instances erstellte Containerimage bereitzustellen.

1. Wählen Sie im Azure DevOps-Portal, in dem das Projekt **eShopOnWeb** angezeigt wird, **Pipelines > Pipelines** aus, und wählen Sie dann **Neue Pipeline-** aus.

1. Wählen Sie auf der Seite **Wo befindet sich Ihr Code?** den Eintrag **Azure Repos Git (YAML)** und dann das Repository **eShopOnWeb** aus.

1. Wählen Sie auf der Registerkarte **Pipeline konfigurieren** die Option **Vorhandene Azure Pipelines-YAML-Datei** aus. Geben Sie den Pfad **/.ado/eshoponweb-cd-aci.yml** an, und wählen Sie **Weiter** aus.

1. Führen Sie in der YAML-Pipelinedefinition im Variablenabschnitt folgende Aktionen aus:

   - Legen Sie den Wert der Standortvariablen auf den Namen einer Azure-Region fest, die Sie zuvor in diesem Lab verwendet haben, z. B. **centralus**.
   - Ersetzen Sie **YOUR-SUBSCRIPTION-ID** durch Ihre Azure-Abonnement-ID
   - Ersetzen Sie **az400eshop-NAME** durch einen global eindeutigen Namen der Instanz von Azure Container Instances, die bereitgestellt werden soll, z. B. die Zeichenfolge **eshoponweb-lab-docker** gefolgt von einer zufälligen sechsstelligen Zahl.
   - Ersetzen Sie **YOUR-ACR** und **ACR-USERNAME** durch Ihren ACR-Registrierungsnamen, den Sie zuvor in diesem Lab aufgezeichnet haben.
   - Ersetzen Sie **AZ400-EWebShop-NAME** durch den Namen der Ressourcengruppe, die Sie zuvor in diesem Lab erstellt haben (**rg-eshoponweb-secure**).

1. Wählen Sie **Speichern und Ausführen** und dann erneut **Speichern und Ausführen** aus.

1. Öffnen Sie die Pipeline und beachten Sie die Meldung „Diese Pipeline benötigt eine Zugriffsberechtigung für 2 Ressourcen, bevor dieser Lauf mit Docker Compose to ACI fortgesetzt werden kann“. Wählen Sie **Anzeigen** und anschließend zweimal **Zulassen** (für jede Ressource) aus, damit die Pipeline ausgeführt werden kann.

1. Warten Sie, bis die Pipelineausführung abgeschlossen ist. Dieser Vorgang kann einige Minuten in Anspruch nehmen. Die Builddefinition besteht aus einer einzelnen Aufgabe **AzureResourceManagerTemplateDeployment**, die die Instanz von Azure Container Instances (ACI) mithilfe einer Bicep-Vorlage bereitstellt und die ACR-Anmeldeparameter angibt, damit die ACI-Instanz das zuvor erstellte Containerimage herunterladen kann.

1. Ihre Pipeline bekommt einen Namen basierend auf dem Projektnamen. Benennen Sie sie in **eshoponweb-cd-aci** um, damit ihr Zweck leichter erkennbar ist.

> [!IMPORTANT]
> Denken Sie daran, die im Azure-Portal erstellten Ressourcen zu löschen, um unnötige Gebühren zu vermeiden.

## Überprüfung

In dieser Übung haben Sie Azure Key Vault mit einer Azure DevOps-Pipeline integriert, indem Sie die folgenden Schritte durchgeführt haben:

- Zugriff auf die Geheimnisse des Azure Key Vault und Azure-Ressourcen von Azure DevOps aus.
- Es wurden zwei YAML-Pipelines ausgeführt, die aus einem Git-Repository importiert wurden.
- Die Pipeline wurde so konfiguriert, dass das Kennwort mit einer Variablengruppe aus Azure Key Vault abgerufen und für nachfolgende Aufgaben wiederverwendet wird.
