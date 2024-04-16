---
lab:
  title: Integrieren von Azure Key Vault mit Azure Pipelines
  module: 'Module 6: Configure secure access to Azure Repos from pipelines'
---

# Integrieren von Azure Key Vault mit Azure Pipelines

Azure Key Vault bietet eine sichere Speicherung und Verwaltung von vertraulichen Daten, wie z. B. Schlüsseln, Passwörtern und Zertifikaten. Azure Key Vault unterstützt Hardware-Sicherheitsmodule und eine Reihe von Verschlüsselungsalgorithmen und Schlüssellängen. Mit Azure Key Vault können Sie die potenzielle Offenlegung vertraulicher Daten über den Quellcode minimieren, was einen häufigen Fehler von Entwicklern darstellt. Der Zugriff auf Azure Key Vault erfordert eine ordnungsgemäße Authentifizierung und Autorisierung, die fein gestrichelte Berechtigungen für seine Inhalte unterstützt.

Diese Übungen nehmen ungefähr **40** Minuten in Anspruch.

## Vorbereitung

Sie benötigen ein Azure-Abonnement, eine Azure DevOps-Organisation und die eShopOnWeb-Anwendung, um den Labs zu folgen.

- Folgen Sie den Schritten, um Ihre [Lab-Umgebung zu überprüfen](APL2001_M00_Validate_Lab_Environment.md).

In dieser Übung nutzen Sie den Dienstprinzipal, der beim Validieren Ihrer Labumgebung erstellt wurde, um Folgendes zu tun:

- Ressourcen in Ihrem Azure-Abonnement bereitstellen.
- Erhalten von Lesezugriff auf Azure Key Vault-Geheimnisse.

## Anweisungen

In diesem Lab erfahren Sie, wie Sie Azure Key Vault mit Hilfe der folgenden Schritte in Azure Pipelines integrieren können:

- Erstellen Sie einen Azure Key Vault, um ein ACR-Kennwort als Geheimnis zu speichern.
- Konfigurieren Sie die Berechtigungen, damit der Dienstprinzipal das Geheimnis lesen kann.
- Konfigurieren Sie die Pipeline, sodass das Kennwort von Azure Key Vault abgerufen und an nachfolgende Aufgaben weitergegeben wird.

### Übung 1: Einrichten einer CI-Pipeline zur Erstellung des eShopOnWeb-Containers

In dieser Übung richten Sie eine CI YAML-Pipeline zu folgendem Zweck ein:

- Erstellen einer Azure Container Registry zum Speichern der Containerimages
- Verwendung von Docker Compose zum Erstellen und Verteilen von **eshoppublicapi** und **eshopwebmvc** Container Images. Es wird nur der Container**eshopwebmvc**eingesetzt.

#### Aufgabe 1: Einrichten und Ausführen der CI-Pipeline

In dieser Aufgabe werden Sie eine vorhandene CI YAML-Pipelinedefinition importieren, ändern und ausführen. Die Pipeline erstellt eine Azure Container Registry (ACR) und erstellt/veröffentlicht die eShopOnWeb-Containerimages.

1. Navigieren Sie zum Azure DevOps-Portal unter `https://dev.azure.com` und öffnen Sie Ihre Organisation.

1. Navigieren Sie zum Azure DevOps-Projekt **eShopOnWeb**. Wechseln Sie zu **Pipelines > Pipelines**, und wählen Sie dann **Pipeline erstellen** aus.

1. Wählen Sie auf der Seite **Wo befindet sich Ihr Code?** den Eintrag **Azure Repos Git (YAML)** und anschließend das Repository **eShopOnWeb** aus.

1. Wählen Sie auf der Registerkarte **Pipeline konfigurieren** die Option **Vorhandene Azure Pipelines-YAML-Datei** aus. Geben Sie den folgenden Pfad **/.ado/eshoponweb-ci-dockercompose.yml** an, und wählen Sie **Weiter**aus.

   ![Screenshot der Pipelineauswahl aus der YAML-Datei](media/select-ci-container-compose.png)

1. Führen Sie in der YAML-Pipelinedefinition im Abschnitt „Variablen“ folgende Aktionen aus:

   - Ersetzen Sie **AZ400-EWebShop-NAME** durch **rg-eshoponweb-docker**.
   - Legen Sie den Wert der Standortvariable auf den Namen einer Azure-Region fest, die Sie in den vorherigen Labs dieses Kurses verwendet haben (z. B. **southcentralus**).
   - Ersetzen Sie **YOUR-SUBSCRIPTION-ID** durch Ihre Azure-Abonnement-ID.

1. Wählen Sie zunächst **Speichern und ausführen** und dann den direkten Commit zur Mainbranch aus.

1. Wählen Sie erneut **Speichern und ausführen** aus.

   > [!NOTE]
   > Wenn Sie eine neue Verzweigung erstellen, müssen Sie eine Pullanforderung erstellen, um die Änderungen der Mainbranch zusammenzuführen.

1. Öffnen Sie die Pipeline. Wenn die Meldung „Diese Pipeline benötigt die Berechtigung zum Zugriff auf eine Ressource, bevor diese Ausführung mit Docker Compose an WebApp fortgesetzt werden kann“, wählen Sie **Ansicht**, **Zulassen** und erneut **Zulassen** aus. Diese Berechtigung ist erforderlich, damit die Pipeline die Azure-Ressourcen erstellen kann.

   ![Screenshot der Genehmigung des Zugriffs über die YAML-Pipeline](media/pipeline-permit-resource.png)

1. Warten Sie, bis die Pipelineausführung abgeschlossen ist. Dieser Vorgang kann einige Minuten in Anspruch nehmen. Die Buildpipeline besteht aus den folgenden Aufgaben:

     - **AzureResourceManagerTemplateDeployment** verwendet **bicep** zum Erstellen einer Azure Container-Registrierung.
     - **PowerShell** nimmt die Bicep-Ausgabe (acr login server) und erstellt eine Pipeline-Variable.
     - Die Aufgabe **DockerCompose** erstellt und pusht die Containerimages für eShopOnWeb in die Azure Container-Registrierung.

1. Der Name Ihrer Pipeline basiert standardmäßig auf dem Projektnamen. Benennen Sie die Pipeline in **eshoponweb-ci-dockercompose** um, damit sie leichter zu identifizieren ist.

1. Nachdem die Pipeline ausgeführt wurde, verwenden Sie den Webbrowser, um zum Azure-Portal zu navigieren. Öffnen Sie die Ressourcengruppe **rg-eshoponweb-docker**, und wählen Sie den Eintrag aus, der die von der Pipeline bereitgestellte Azure Container Registry (ACR) darstellt.

   > [!NOTE]
   > Damit Sie Repositorys in der Registrierung anzeigen können, müssen Sie Ihrem Benutzerkonto eine Rolle gewähren, die einen solchen Zugriff ermöglicht. Zu diesem Zweck verwenden Sie die Rolle „AcrPull“.

1. Wählen Sie auf der Seite „Containerregistrierung“ zunächst **Zugriffssteuerung (IAM)**, dann **+ Hinzufügen** und im Dropdownmenü **Rollenzuweisung hinzufügen** aus.

1. Wählen Sie auf der Registerkarte **Rolle** der Seite **Rollenzuweisung hinzufügen** die Option **AcrPull** und anschließend **Weiter** aus.

1. Klicken Sie auf der Registerkarte **Mitglieder** auf **+ Mitglieder auswählen** und dann auf **Auswählen**, und wählen Sie anschließend **Weiter** aus.

1. Wählen Sie **Überprüfen + Zuweisen** aus, und aktualisieren Sie nach erfolgreicher Ausführung der Zuweisung die Browserseite.

1. Wählen Sie auf der Seite „Containerregistrierung“ in der vertikalen Menüleiste auf der linken Seite im Abschnitt **Services** den Eintrag **Repositorys** aus.

1. Vergewissern Sie sich, dass die Registrierung die Images **eshoppublicapi** und **eshopwebmvc** enthält. In der Bereitstellungsphase verwenden Sie lediglich **eshopwebmvc**.

   ![Screenshot der Containerimages in ACR aus dem Azure-Portal](media/azure-container-registry.png)

1. Wählen Sie **Zugriffsschlüssel**aus, aktivieren Sie das Kontrollkästchen **Administratorbenutzer**, und kopieren Sie den Wert von **Kennwort**, der in der folgenden Aufgabe verwendet wird, wo Sie ihn als Geheimnis zu Azure Key Vault hinzufügen.

   ![Screenshot des ACR-Kennworts aus der Einstellung „Zugriffsschlüssel“](media/acr-password.png)

1. Notieren Sie den Wert **Registrierungsnamen** auf derselben Seite. Sie benötigen diese später in diesem Lab.

#### Aufgabe 2: Erstellen eines Azure Key Vault

In dieser Aufgabe werden Sie einen Azure Key Vault mithilfe des Azure-Portals erstellen.

In diesem Labszenario verfügen wir über eine Instanz von Azure Container Instances (ACI), die ein Containerimage abruft und ausführt, das in Azure Container Registry (ACR) gespeichert ist. Wir möchten das Kennwort für die ACR als Geheimnis im Azure Key Vault speichern.

1. Geben Sie im Azure-Portal in das Textfeld **Ressourcen, Dienste und Dokumente suchen** **Key Vault** ein und drücken Sie die **Eingabetaste**.

1. Wählen Sie das Blatt **Schlüsseltresor** aus, und klicken Sie auf **Erstellen > Schlüsseltresor**.

1. Geben Sie auf der Registerkarte **Allgemein** des Blatts **Key Vault erstellen** die folgenden Einstellungen an und klicken Sie auf **Weiter**:

   | Einstellung | Wert |
   | --- | --- |
   | Subscription | Der Name des Azure-Abonnements, das Sie in diesem Lab verwenden. |
   | Resource group | der Ressourcengruppenname **rg-eshoponweb-docker** |
   | Name des Schlüsseltresors | ein eindeutiger gültiger Name, z. B. **ewebshop-kv-** gefolgt von einer zufälligen sechsstelligen Zahl |
   | Region | dieselbe Azure-Region, die Sie zuvor in diesem Lab verwendet haben |
   | Tarif | **Standard** |
   | Aufbewahrungsdauer für gelöschte Tresore in Tagen | **7** |
   | Bereinigungsschutz | **Bereinigungsschutz deaktivieren** |

1. Wählen Sie auf der Registerkarte **Zugriffskonfiguration** auf dem Blatt **Schlüsseltresor erstellen** im Abschnitt **Berechtigungsmodell** die Option **Tresorzugriffsrichtlinie** aus. 

1. Wählen Sie im Abschnitt **Zugriffsrichtlinien** **+ Erstellen** aus, um eine neue Richtlinie einzurichten.

   > **Hinweis**: Sie müssen den Zugriff auf Ihre Key Vaults sichern, indem Sie nur autorisierte Anwendungen und Benutzer*innen zulassen. Um auf die Daten aus dem Vault zuzugreifen, müssen Sie dem zuvor erstellten Dienstprinzipal, den Sie für die Authentifizierung in der Pipeline verwenden werden, Leseberechtigungen (Get/List) erteilen.

   - Überprüfen Sie auf dem Blatt **Berechtigung** die Berechtigungen **Get** und **List** unterhalb von **Geheimnis- Berechtigung**. Wählen Sie **Weiter** aus.
   - Suchen Sie auf dem Blatt **Prinzipal** entweder mithilfe der ID oder des Namens nach dem Dienstprinzipal, den Sie beim Überprüfen der Labumgebung erstellt haben. Wählen Sie **Weiter** und dann wieder **Weiter** aus.
   - Wählen Sie auf dem Blatt **Überprüfen + erstellen** die Option **Erstellen** aus.

1. Kehren Sie zum Blatt **Schlüsseltresor erstellen** zurück, und wählen Sie **Überprüfen und erstellen > Erstellen** aus.

   > [!NOTE]
   > Warten Sie, bis der Azure Key Vault bereitgestellt wird. Das sollte weniger als eine Minute dauern.

1. Wählen Sie auf dem Blatt **Ihre Bereitstellung wurde abgeschlossen** die Option **Zu Ressource wechseln** aus.

1. Wählen Sie auf dem Blatt „Azure Key Vault“ im vertikalen Menü auf der linken Seite des Blatts im Abschnitt **Objekte** **Geheimnisse** aus.

1. Wählen Sie auf dem Blatt **Geheimnis** die Option **+ Generieren/importieren** aus.

1. Legen Sie auf dem Blatt **Geheimnis erstellen** die folgenden Einstellungen fest, und wählen Sie **Erstellen** aus (Standardwerte für die anderen Einstellungen übernehmen):

   | Einstellung | Wert |
   | --- | --- |
   | Uploadoptionen | **Manuell** |
   | Name | **acr-secret** |
   | Wert | ACR-Zugriffskennwort, das in der vorherigen Aufgabe kopiert wurde |

#### Aufgabe 3: Erstellen einer mit Azure Key Vault verbundenen Variablengruppe

In dieser Aufgabe erstellen Sie eine variable Gruppe in Azure DevOps, die das ACR-Kennwortgeheimnis aus Key Vault über die Dienstverbindung (Dienstprinzipal) abruft

1. Navigieren Sie zum Azure DevOps-Portal unter `https://dev.azure.com` und öffnen Sie Ihre Organisation.

1. Navigieren Sie zum Azure DevOps-Projekt **eShopOnWeb**.

1. Wählen Sie im vertikalen Navigationsbereich des Azure DevOps-Portals **Pipelines > Bibliothek**aus. Wählen Sie **+ Variablengruppe** aus.

1. Geben Sie auf dem Blatt **Neue Variablengruppe** die folgenden Einstellungen an:

   | Einstellung | Wert |
   | --- | --- |
   | Variablengruppenname | **eshopweb-vg** |
   | Verknüpfen von Geheimnisse von Azure Key Vault als Variablen | **enable** |
   | Azure-Abonnement | **Verfügbare Azure-Dienstverbindung > azure subs** |
   | Name des Schlüsseltresors | der Name, den Sie dem Azure Key Vault in der vorherigen Aufgabe zugewiesen haben |

1. Wählen Sie unter **Variablen** **+ Hinzufügen** und dann das Geheimnis **acr-secret** aus. Klickan Sie auf **OK**.

1. Wählen Sie **Speichern**.

   ![Screenshot der Variablengruppenerstellung](media/vg-create.png)

#### Aufgabe 4: Einrichten der CD-Pipeline zum Bereitstellen von Containern in Azure Container Instances (ACI)

In dieser Aufgabe importieren Sie eine CD-Pipeline, passen sie an und führen sie aus, um das zuvor in einer Instanz von Azure Container Instances erstellte Containerimage bereitzustellen.

1. Wählen Sie im Azure DevOps-Portal, in dem das Projekt **eShopOnWeb** angezeigt wird, **Pipelines > Pipelines** aus, und wählen Sie dann **Neue Pipeline-** aus.

1. Wählen Sie auf der Seite **Wo befindet sich Ihr Code?** den Eintrag **Azure Repos Git (YAML)** und dann das Repository **eShopOnWeb** aus.

1. Wählen Sie auf der Registerkarte **Pipeline konfigurieren** die Option **Vorhandene Azure Pipelines-YAML-Datei** aus. Geben Sie den Pfad **/.ado/eshoponweb-cd-aci.yml** an, und wählen Sie **Weiter** aus.

1. Führen Sie in der YAML-Pipelinedefinition im Variablenabschnitt folgende Aktionen aus:

   - Legen Sie den Wert der Standortvariable auf den Namen einer Azure-Region fest, die Sie zuvor in diesem Lab verwendet haben.
   - Ersetzen Sie **YOUR-SUBSCRIPTION-ID** durch Ihre Azure-Abonnement-ID.
   - Ersetzen von **az400eshop-NAME** durch einen global eindeutigen Namen der Azure-Containerinstanz, die bereitgestellt werden soll, z. B. die Zeichenfolge **eshoponweb-lab-docker** gefolgt von einer zufälligen sechsstelligen Zahl. 
   - Ersetzen Sie **YOUR-ACR-** und **ACR-USERNAME** durch Ihren ACR-Registrierungsnamen, den Sie zuvor in diesem Lab aufgezeichnet haben.
   - Ersetzen Sie **AZ400-EWebShop-NAME** durch den Namen der Ressourcengruppe, die Sie zuvor in diesem Lab erstellt haben (**rg-eshoponweb-docker**).

1. Wählen Sie **Speichern und Ausführen** und dann erneut **Speichern und Ausführen** aus.

1. Öffnen Sie die Pipeline, und beachten Sie die Meldung „Diese Pipeline benötigt die Berechtigung für den Zugriff auf 2 Ressourcen, bevor diese Ausführung mit Docker Compose an ACI fortgesetzt werden kann“. Wählen Sie **Anzeigen** und anschließend zweimal **Zulassen** (für jede Ressource) aus, damit die Pipeline ausgeführt werden kann.

1. Warten Sie, bis die Pipelineausführung abgeschlossen ist. Dieser Vorgang kann einige Minuten in Anspruch nehmen. Die Builddefinition besteht aus einer einzelnen Aufgabe **AzureResourceManagerTemplateDeployment**, die die Instanz von Azure Container Instances (ACI) mithilfe einer Bicep-Vorlage bereitstellt und die ACR-Anmeldeparameter angibt, damit die ACI-Instanz das zuvor erstellte Containerimage herunterladen kann.

1. Ihre Pipeline bekommt einen Namen basierend auf dem Projektnamen. Benennen Sie sie in **eshoponweb-cd-aci** um, damit ihr Zweck leichter erkennbar ist.

### Übung 2: Bereinigung von Azure- und Azure DevOps-Ressourcen

In dieser Übung entfernen Sie Azure- und Azure DevOps-Ressourcen, die in diesem Lab erstellt wurden.

#### Aufgabe 1: Entfernen Sie Azure-Ressourcen,

1. Navigieren Sie im Azure-Portal zur Ressourcengruppe **rg-eshoponweb-docker** mit bereitgestellten Ressourcen, und wählen Sie **Ressourcengruppe löschen** aus, um alle in diesem Lab erstellten Ressourcen zu löschen.

#### Aufgabe 2: Entfernen der Azure DevOps-Pipelines

1. Navigieren Sie zum Azure DevOps-Portal unter `https://dev.azure.com` und öffnen Sie Ihre Organisation.

1. Öffnen Sie das Projekt **eShopOnWeb**.

1. Navigieren Sie zu **Pipelines > Pipelines**.

1. Wechseln Sie zu **Pipelines > Pipelines**, und löschen Sie die vorhandenen Pipelines.

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

In dieser Übung haben Sie Azure Key Vault mit einer Azure DevOps-Pipeline integriert, indem Sie die folgenden Schritte durchgeführt haben:

- Es wurde ein Azure-Dienstprinzipal verwendet, um den Zugriff auf die Geheimnisse von Azure Key Vault und den Zugriff auf Azure-Ressourcen aus Azure DevOps zu ermöglichen.
- Es wurden zwei YAML-Pipelines ausgeführt, die aus einem Git-Repository importiert wurden.
- Die Pipeline wurde so konfiguriert, dass das Kennwort mit einer Variablengruppe aus Azure Key Vault abgerufen und für nachfolgende Aufgaben wiederverwendet wird.
