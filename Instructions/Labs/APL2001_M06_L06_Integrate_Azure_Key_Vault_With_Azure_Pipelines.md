---
lab:
  title: Integrieren von Azure Key Vault mit Azure Pipelines
  module: 'Module 6: Configure secure access to Azure Repos from pipelines'
---

# Integrieren von Azure Key Vault mit Azure Pipelines

Azure Key Vault bietet eine sichere Speicherung und Verwaltung von vertraulichen Daten, wie z. B. Schlüsseln, Passwörtern und Zertifikaten. Azure Key Vault unterstützt Hardware-Sicherheitsmodule und eine Reihe von Verschlüsselungsalgorithmen und Schlüssellängen. Mit Azure Key Vault können Sie die potenzielle Offenlegung vertraulicher Daten über den Quellcode minimieren, was einen häufigen Fehler von Entwicklern darstellt. Der Zugriff auf Azure Key Vault erfordert eine ordnungsgemäße Authentifizierung und Autorisierung, die fein gestrichelte Berechtigungen für seine Inhalte unterstützt.

Diese Übung dauert ca. **30** Minuten.

## Vorbereitung

Sie benötigen ein Azure-Abonnement, eine Azure DevOps-Organisation und die eShopOnWeb-Anwendung, um den Laboren zu folgen.

- Führen Sie die Schritte aus, um Ihre Lab-Umgebung[ zu ](APL2001_M00_Validate_Lab_Environment.md)überprüfen.

## Anweisungen

In diesem Lab werden Sie sehen, wie Sie Azure Key Vault mit Hilfe der folgenden Schritte in eine Azure-Pipeline integrieren können:

- Erstellen Sie einen Azure Key Vault, um ein ACR-Kennwort als Geheimnis zu speichern.
- Erstellen Sie einen Azure-Dienstprinzipal, um auf die Geheimnisse von Azure Key Vault zuzugreifen.
- Konfigurieren Sie die Berechtigungen, damit der Dienstprinzipal das Geheimnis lesen kann.
- Konfigurieren Sie die Pipeline so, dass das Kennwort von Azure Key Vault abgerufen und an nachfolgende Aufgaben weitergegeben wird.

### Übung 1: Einrichten einer CI-Pipeline zum Erstellen eines eShopOnWeb-Containers

Einrichten der CI YAML-Pipeline für:

- Pushen des Containerimages in die Azure-Containerregistrierung
- Verwenden von Docker Compose zum Erstellen und Pushen **von eshoppublicapi** und **eshopwebmvc-Containerimages** . Nur **eshopwebmvc-Container** wird bereitgestellt.

#### Aufgabe 1: (Wenn erledigt, überspringen) Erstellen eines Dienstprinzipals

In dieser Aufgabe erstellen Sie einen Dienstprinzipal mithilfe der Azure CLI, der Azure DevOps zulässt:

- Bereitstellen von Ressourcen in einem Azure-Abonnement
- Haben Sie Lesezugriff auf die später erstellten Schlüsseltresorschlüssel.

Sie benötigen einen Dienstprinzipal, um Azure-Ressourcen aus Azure-Pipelines bereitzustellen. Da Sie geheime Schlüssel in einer Pipeline abrufen, müssen Sie dem Dienst die Berechtigung erteilen, wenn wir den Azure Key Vault erstellen.

Azure Pipelines erstellt automatisch einen Dienstprinzipal, wenn Sie eine Verbindung mit einem Azure-Abonnement aus einer Pipelinedefinition herstellen oder wenn Sie eine neue Dienst-Verbinden ion über die Projekteinstellungsseite (automatische Option) erstellen. Sie können den Dienstprinzipal auch manuell über das Portal erstellen oder Azure CLI verwenden und für alle Projekte wiederverwenden.

1. Starten Sie auf Ihrem Labcomputer einen Webbrowser, navigieren Sie zum Azure-Portal, und melden Sie sich an. Verwenden Sie dabei die Anmeldeinformationen eines Benutzerkontos, das in dem Abonnement, das Sie in diesem Lab verwenden, über die Rolle „Besitzer“ und in dem Azure AD-Mandanten, der dem Azure-Abonnement zugeordnet ist, über die Rolle „Globaler Administrator“ verfügt.

1. Öffnen Sie im Azure-Portal den Bereich **Cloud Shell**, indem Sie auf das Symbolleistensymbol rechts neben dem Textfeld für die Suche klicken.

1. Wählen Sie bei Aufforderung zur Auswahl von **Bash** oder **PowerShell** die Option **Bash** aus.

   > [!NOTE]
   > Wenn Sie **Cloud Shell** zum ersten Mal starten und die Meldung **Für Sie wurde kein Speicher bereitgestellt.** angezeigt wird, wählen Sie das in diesem Lab verwendete Abonnement und anschließend **Speicher erstellen** aus.

1. Führen Sie in der **Bash-Eingabeaufforderung** im **Cloud Shell-Bereich** die folgenden Befehle aus, um die Werte der Azure-Abonnement-ID und der Abonnementnamenattribute abzurufen:

    ```bash
    az account show --query id --output tsv
    az account show --query name --output tsv
    ```

    > [!NOTE]
    > Kopieren Sie beide Werte in eine Textdatei. Sie werden sie später in diesem Lab benötigen.

1. Führen Sie in der Bash-Eingabeaufforderung** im **Cloud Shell-Bereich** den folgenden Befehl aus, um einen Dienstprinzipal zu erstellen (ersetzen Sie den **myServicePrincipalName** durch eine beliebige Zeichenfolge aus Buchstaben und Ziffern) und **mySubscriptionID** durch Ihre Azure subscriptionId**:

    ```bash
    az ad sp create-for-rbac --name myServicePrincipalName \
                         --role contributor \
                         --scopes /subscriptions/mySubscriptionID
    ```

    > [!NOTE]
    > Der Befehl generiert eine JSON-Ausgabe. Speichern der Ausgabe in einer Textdatei Sie benötigen ihn später in diesem Lab.

1. Navigieren Sie als Nächstes zum Azure DevOps-Portal unter `https://dev.azure.com` und öffnen Sie Ihre Organisation.

1. um zum Azure DevOps-Projekt zu navigieren. Wählen Sie unter Pipelines die Option Dienstverbindungen und anschließend rechts oben die Option Neue Dienstverbindung aus.

    ![Screenshot der Schaltfläche „Verbindung erstellen“](media/new-service-connection.png)

1. Wählen Sie im Bildschirm **Neue Dienstverbindung** die Option **Azure Resource Manager** und anschließend **Weiter** aus.

1. Wählen Sie **Dienstprinzipal (manuell)** und dann **Weiter** aus.

1. Füllen Sie die leeren Felder mit den informationen aus, die während der vorherigen Schritte gesammelt wurden:
    - Abonnement-ID oder -Name.
    - Dienstprinzipal-ID (oder clientId/AppId), Dienstprinzipalschlüssel (oder Kennwort) und TenantId.
    - Geben Sie unter **"Dienstverbindungsname****" Azure-Untere ein**. Auf diesen Namen wird in YAML-Pipelines verwiesen, wenn ein Azure DevOps Service Verbinden ion erforderlich ist, um mit Ihrem Azure-Abonnement zu kommunizieren.

        ![Screenshot: Konfiguration der GitHub-Dienstverbindung.](media/azure-service-connection.png)

1. Gewähren Sie allen Pipelines die Zugriffsberechtigung. Wählen Sie **Überprüfen und Speichern**.

    > [!NOTE]
    > Die **Option "Zugriff gewähren" für alle Pipelines** wird für Produktionsumgebungen nicht empfohlen. Es wird nur in dieser Übung verwendet, um die Konfiguration der Pipeline zu vereinfachen.

#### Aufgabe 2: Einrichten und Ausführen der CI-Pipeline

In dieser Aufgabe importieren Sie eine vorhandene CI YAML-Pipelinedefinition, ändern und ausführen sie. Sie erstellt eine neue Azure Container Registry (ACR) und erstellt/veröffentlicht die eShopOnWeb-Containerimages.

1. Navigieren Sie zum Azure DevOps-Portal unter `https://dev.azure.com` und öffnen Sie Ihre Organisation.

1. um zum Azure DevOps-Projekt zu navigieren. Wechseln Sie zu **Pipelines > Pipelines**, und klicken Sie auf "Pipeline** erstellen" (oder **"** Neue Pipeline").**

1. Wählen Sie **im **Fenster "Wo befindet Sich Ihr Code?** " Azure Repos Git (YAML)** aus, und wählen Sie das **eShopOnWeb-Repository** aus.

1. Wählen Sie auf der Registerkarte **Konfigurieren** die Option aus, um eine **Vorhandene Azure Pipelines YAML-Datei** zu verwenden. Geben Sie den folgenden Pfad **/.ado/eshoponweb-ci-dockercompose.yml** an, und wählen Sie "Weiter"** aus**.

    ![Screenshot der Pipelineauswahl aus der YAML-Datei.](media/select-ci-container-compose.png)

1. Passen Sie in der YAML-Pipelinedefinition im Abschnitt "Variablen" Ihren Ressourcengruppennamen an, indem **Sie AZ400-EWebShop-NAME** durch den Namen Ihrer Einstellung ersetzen, **z. B. "rg-eshoponweb**", "YOUR-SUBSCRIPTION-ID **" durch Ihre eigene Azure subscriptionId ersetzen **und den Standort Ihrer Einstellung auswählen, z **. B. Southcentralus**.

1. (Optional) Sie können den selbst gehosteten Agent verwenden, der in der vorherigen Übung erstellt wurde, um den Poolnamen zu aktualisieren, der derzeit auf den von Microsoft gehosteten Agent auf den Namen des von Ihnen erstellten Agentpools, **eShopOnWebSelfPool**, festgelegt ist.

    Anstelle von:

    ```YAML
      - job: Build
        pool:
          vmImage: ubuntu-latest
    
    ```

    Verwendung:

    ```YAML
      - job: Build
        pool: eShopOnWebSelfPool
    
    ```

    > [!NOTE]
    > Um die Pipeline mit dem selbst gehosteten Agent auszuführen, müssen Sie den Agent ausführen lassen und alle erforderlichen Komponenten installiert haben, z. B. Visual Studio, um die Lösung zu erstellen. Wenn Sie die erforderlichen Komponenten nicht installiert haben, können Sie den von Microsoft gehosteten Agent verwenden.

1. Wählen Sie **Save** (Speichern) aus, um den Commit direkt im Mainbranch auszuführen, oder erstellen Sie einen neuen Branch für diesen Commit.

1. Wählen Sie erneut **Speichern und ausführen** aus.

    > [!NOTE]
    > Wenn Sie eine neue Verzweigung erstellen, müssen Sie eine Pullanforderung erstellen, um die Änderungen an der Standard Verzweigung zusammenzuführen.

1. Öffnen Sie die Pipeline. Wenn die Meldung "Diese Pipeline benötigt Die Berechtigung für den Zugriff auf eine Ressource benötigt, bevor diese Ausführung weiterhin ACR für Bilder erstellen kann" angezeigt wird, wählen Sie **"Ansicht**", **"Zulassen" und **"Zulassen**"** erneut aus. Dies ist erforderlich, damit die Pipeline die Azure Container Registry (ACR)-Ressource erstellen kann.

    ![Screenshot des Genehmigungszugriffs über die YAML-Pipeline.](media/pipeline-permit-resource.png)

1. Der Build kann einige Minuten dauern, bis die Pipeline ausgeführt wird. Die Buildpipeline besteht aus den folgenden Aufgaben:
      - **AzureResourceManagerTemplateDeployment** verwendet **bicep** zum Bereitstellen einer Azure-Containerregistrierung.
      - **Die PowerShell-Aufgabe** übernimmt die Bicep-Ausgabe (acr-Anmeldeserver) und erstellt eine Pipelinevariable.
      - **DockerCompose-Aufgabenbuilds** und pushen die Containerimages für eShopOnWeb in die Azure Container Registry.

1. Ihre Pipeline nimmt einen Namen auf der Grundlage des Projektnamens an. Benennen Sie sie um, um die Pipeline besser zu identifizieren.

1. Wechseln Sie zu **Pipelines > Pipelines** in der kürzlich erstellten Pipeline, zeigen Sie mit der Maus auf die ausgeführte Pipeline, und wählen Sie die Auslassungspunkte und **option "Umbenennen/Verschieben** " aus.

1. Nennen Sie es **eshoponweb-ci-dockercompose**, und wählen Sie "Speichern" aus****.

1. Nachdem die Ausführung abgeschlossen ist, öffnen Sie im Azure-Portal die zuvor definierte Ressourcengruppe, und wählen Sie den Eintrag aus, der die von der Pipeline bereitgestellte Azure Container Registry (ACR) darstellt.

    > [!NOTE]
    > Um Repositorys in der Registrierung anzuzeigen, müssen Sie eine Rolle zuweisen, die einen solchen Zugriff ermöglicht. Zu diesem Zweck verwenden Sie die Rolle "AcrPull".

1. Klicken Sie auf dem Blatt **Zugriffssteuerung (IAM)** auf **+ Hinzufügen** und im Dropdownmenü auf **Rollenzuweisung hinzufügen**.

1. Wählen Sie auf der **Registerkarte "Rolle**" auf der **Seite "Rollenzuweisung** hinzufügen" AcrPull** und dann "**Weiter"**** aus.

1. Klicken Sie auf der **Registerkarte "Mitglieder**" auf **"+Mitglieder** auswählen", wählen Sie Ihr Benutzerkonto aus, klicken Sie auf **"Auswählen**", und wählen Sie dann "Weiter"** aus**.

1. Wählen Sie "Überprüfen+ Zuweisen **" aus, und aktualisieren Sie **die Browserseite, sobald die Aufgabe erfolgreich abgeschlossen wurde.

1. Wählen Sie auf der Seite "Containerregistrierung" in der vertikalen Menüleiste auf der linken Seite im **Abschnitt "Dienste**" die Option "Repositorys"** aus**.

1. Stellen Sie sicher, dass die Registrierung Bilder **eshoppublicapi** und **eshopwebmvc** enthält. Sie verwenden **eshopwebmvc** nur in der Bereitstellungsphase.

    ![Screenshot: Seite „Neuen Container erstellen“ im Azure-Portal](media/azure-container-registry.png)

1. Wählen Sie Zugriffstasten** aus, aktivieren Sie **das **Kontrollkästchen "Administratorbenutzer**", und kopieren Sie den **Kennwortwert**, der in der folgenden Aufgabe verwendet wird, da wir ihn als geheimer Schlüssel im Azure Key Vault beibehalten.

    ![Screenshot des ACR-Kennworts aus der Zugriffstasteneinstellung.](media/acr-password.png)

#### Aufgabe 3: Erstellen einer Azure Key Vault-Instanz

In dieser Aufgabe erstellen Sie einen Azure Key Vault mithilfe der Azure-Portal.

Für dieses Übungsszenario verfügen wir über eine Azure Container Instance (ACI), die ein Containerimage abruft und ausführt, das in der Azure Container Registry (ACR) gespeichert ist. Wir beabsichtigen, das Kennwort für die ACR als geheimen Schlüssel im Schlüsseltresor zu speichern.

1. Geben Sie im Azure-Portal oben auf der Azure-Portalseite im Textfeld **Nach Ressourcen, Diensten und Dokumenten suchen** den Begriff **Ressourcen** ein, und drücken Sie die **EINGABETASTE**.

1. Wählen Sie auf dem Blatt **Schlüsseltresore** die Option **Schlüsseltresor erstellen** aus.

1. Geben Sie auf dem Blatt **Arbeitsbereich erstellen** auf der Registerkarte **Grundeinstellungen** die folgenden Einstellungen an, und wählen Sie Weiter:

    | Einstellung | Wert |
    | --- | --- |
    | Subscription | Der Name des Azure-Abonnements, das Sie in diesem Lab verwenden. |
    | Resource group | der Ressourcengruppenname **rg-eshoponweb** |
    | Name des Schlüsseltresors | beliebiger eindeutiger gültiger Name, z **. B. ewebshop-kv-NAME** (name ersetzen) |
    | Region | Wählen Sie die Azure-Region aus, die dem Standort Ihrer Laborumgebung am nächsten ist. |
    | Tarif | **Standard** |
    | Aufbewahrungsdauer für gelöschte Tresore in Tagen | **7** |
    | Bereinigungsschutz | Aktivieren des Löschschutzes |

1. Wählen Sie auf der **Registerkarte "Access-Konfiguration** " des **Blatts "Schlüsseltresor** erstellen" im **Abschnitt "Berechtigungsmodell** " die **Option "Tresorzugriffsrichtlinie**" aus. 

1. Wählen Sie im **Abschnitt "Zugriffsrichtlinien** " die Option **"+Erstellen** " aus, um eine neue Richtlinie einzurichten.

    > Sie müssen den Zugriff auf Ihre wichtigsten Tresore sichern, indem Sie nur autorisierte Anwendungen und Benutzer zulassen. Um auf die Daten aus dem Tresor zuzugreifen, müssen Sie Leseberechtigungen (Get/List) für den zuvor erstellten Dienstprinzipal bereitstellen, den Sie für die Authentifizierung in der Pipeline verwenden.

    - Überprüfen Sie auf dem **Blatt "Berechtigung"** die Berechtigung "Abrufen** und **Auflisten**" unter "**Geheime Berechtigung"**.** Wählen Sie **Weiter** aus.
    - suchen Sie auf dem **Blatt "Prinzipal** " nach dem **zuvor erstellten Dienstprinzipal**, entweder mithilfe der id oder des angegebenen Namens. Wählen Sie **Weiter** und dann wieder **Weiter** aus.
    - Wählen Sie auf dem Blatt "Überprüfen + Erstellen" die Option "Erstellen".

1. Zurück auf dem **Blatt "Key Vault** erstellen" wählen Sie "Überprüfen" und "Erstellen" aus, und wählen Sie **"> Erstellen" aus.**

    > [!NOTE]
    > Warten Sie, bis der Azure Key Vault bereitgestellt wird. Das sollte weniger als eine Minute dauern.

1. Klicken Sie im Bereich **Ihre Bereitstellung wurde abgeschlossen** auf **Zu Ressource wechseln**.

1. Wählen Sie auf dem Blatt "Azure Key Vault" im vertikalen Menü auf der linken Seite des Blatts im **Abschnitt "Objekte**" die Option "Geheime Schlüssel"** aus**.

1. Wählen Sie im Bereich **Geheimnis** die Option **+ Generieren/importieren** aus.

1. Legen Sie auf dem Blatt **Neue Dateifreigabe** die folgenden Einstellungen fest, und klicken Sie auf **Erstellen** (Standardwerte für die anderen Einstellungen übernehmen):

    | Einstellung | Wert |
    | --- | --- |
    | Uploadoptionen | **Manuell** |
    | Name | **acr-secret** |
    | Wert | ACR-Zugriffskennwort, das in der vorherigen Aufgabe kopiert wurde |

#### Aufgabe 3: Erstellen einer variablen Gruppe, die mit Azure Key Vault verbunden ist

In dieser Aufgabe erstellen Sie eine Variable Gruppe in Azure DevOps, die den ACR-Kennwortschlüssel mithilfe des Dienst-Verbinden ion (Dienstprinzipal) aus dem Key Vault abruft.

1. Navigieren Sie zum Azure DevOps-Portal unter `https://dev.azure.com` und öffnen Sie Ihre Organisation.

1. um zum Azure DevOps-Projekt zu navigieren.

1. Wählen Sie **im vertikalen Navigationsbereich des Azure DevOps-Portals Pipelines > Bibliothek** aus. Wählen Sie **+ Variable group** (Variablengruppe) aus.

1. Geben Sie auf dem Blatt **Neue Dateifreigabe** die folgenden Einstellungen an:

    | Einstellung | Wert |
    | --- | --- |
    | Variablengruppenname | **eshopweb-vg** |
    | Geheimnisse von Azure KV verknüpfen ... | **enable** |
    | Azure-Abonnement | Verfügbare Azure-Dienstverbindungen |
    | Name des Schlüsseltresors | Der Name Ihres Schlüsseltresors|

1. Wählen Sie **unter **"Variablen**" +Hinzufügen** aus, und wählen Sie den **geheimen Schlüssel "acr-secret**" aus. Klickan Sie auf **OK**.

1. Wählen Sie **Speichern**.

    ![Screenshot der Variablengruppenerstellung.](media/vg-create.png)

#### Aufgabe 4: Einrichten der CD-Pipeline zum Bereitstellen von Containern in Azure Container Instance(ACI)

In dieser Aufgabe importieren Sie eine CD-Pipeline, passen sie an und führen sie aus, um das zuvor in einer Azure-Containerinstanz erstellte Containerimage bereitzustellen.

1. Navigieren Sie zum Azure DevOps-Portal unter `https://dev.azure.com` und öffnen Sie Ihre Organisation.

1. um zum Azure DevOps-Projekt zu navigieren. Wechseln Sie zu **Pipelines**, und klicken Sie auf **Neue Pipeline**.

1. Wählen Sie **im **Fenster "Wo befindet Sich Ihr Code?** " Azure Repos Git (YAML)** aus, und wählen Sie das **eShopOnWeb-Repository** aus.

1. Wählen Sie auf der Registerkarte **Konfigurieren** die Option aus, um eine **Vorhandene Azure Pipelines YAML-Datei** zu verwenden. Geben Sie den folgenden Pfad **/.ado/eshoponweb-cd-aci.yml** an, und wählen Sie "Weiter"** aus**.

1. Passen Sie in der YAML-Pipelinedefinition Folgendes an:

    - Ersetzen Sie <yourSubscriptionId> durch Ihre Azure-Abonnement-ID.
    - **az400eshop-NAME** ersetzt NAME, um ihn global eindeutig zu machen.
    - **** YOUR-ACR.azurecr.io und **ACR-USERNAME** mit Ihrem ACR-Anmeldeserver (beide benötigen den ACR-Namen, können auf der ACR-> Zugriffstasten überprüft werden).
    - **rg-eshoponweb** mit dem zuvor im Labor definierten Ressourcengruppennamen.

1. Wählen Sie "Speichern" und "Ausführen"** aus, und warten Sie**, bis die Pipeline erfolgreich ausgeführt wird.

    > Die Bereitstellung kann einige Minuten dauern. Die CD-Definition besteht aus den folgenden Aufgaben:
    - **Ressourcen** : Es ist bereit, basierend auf dem Abschluss der CI-Pipeline automatisch auszulösen. Außerdem wird das Repository für die Bicep-Datei heruntergeladen.
    - **Variablen (für die Bereitstellungsphase)** stellen eine Verbindung mit der Variablengruppe zur Nutzung des geheimen Azure Key Vault-Schlüssels "acr-secret **" bereit.**
    - **AzureResourceManagerTemplateDeployment** stellt die Azure Container Instance (ACI) mithilfe der Bicep-Vorlage bereit und stellt die ACR-Anmeldeparameter bereit, damit ACI das zuvor erstellte Containerimage aus der Azure Container Registry (ACR) herunterladen kann.

1. Ihre Pipeline nimmt einen Namen auf der Grundlage des Projektnamens an. Benennen Sie sie um, um die Pipeline besser zu identifizieren.

1. Wechseln Sie zu **Pipelines > Pipelines**, wählen Sie die zuletzt erstellte Pipeline aus, wählen Sie die Auslassungspunkte und dann **die Option "Umbenennen/Verschieben** " aus.

1. Nennen Sie es **eshoponweb-cd-aci**, und wählen Sie "Speichern" aus****.

### Übung 2: Entfernen der Azure-Lab-Ressourcen.

1. Navigieren Sie im Azure-Portal zu Ihrer Ressourcengruppe, und klicken Sie auf **Ressourcengruppe löschen**.

    ![Screenshot: Schaltfläche „Ressourcengruppe löschen“](media/delete-resource-group.png)

    > [!WARNING]
    > Denken Sie daran, alle neu erstellten Azure-Ressourcen zu entfernen, die Sie nicht mehr verwenden. Durch das Entfernen nicht verwendeter Ressourcen wird sichergestellt, dass keine unerwarteten Kosten anfallen.

## Überprüfung

In diesem Lab werden Sie sehen, wie Sie Azure Key Vault mit Hilfe der folgenden Schritte in eine Azure-Pipeline integrieren können:

- Es wurde ein Azure-Dienstprinzipal erstellt, um den Zugriff auf die Geheimnisse des Azure Key Vault zu ermöglichen und die Azure-Bereitstellung über Azure DevOps zu authentifizieren.
- Führen Sie 2 YAML-Pipelines aus, die aus einem Git-Repository importiert wurden.
- Konfigurieren Sie die Pipeline so, dass das Kennwort von Azure Key Vault abgerufen und an nachfolgende Aufgaben weitergegeben wird.
