---
lab:
  title: Überprüfen Sie Ihre Lab-Umgebung
  module: 'Module 0: Welcome'
---

# Überprüfen Sie Ihre Lab-Umgebung

Bei der Vorbereitung auf die Labs ist es wichtig, dass Ihre Umgebung richtig eingerichtet ist. Diese Seite führt Sie durch den Einrichtungsprozess und stellt sicher, dass alle Voraussetzungen erfüllt sind.

- Für dieses Lab ist **Microsoft Edge** oder ein von [Azure DevOps unterstützter Browser](https://learn.microsoft.com/azure/devops/server/compatibility?view=azure-devops#web-portal-supported-browsers) erforderlich.

- **Einrichten eines Azure-Abonnements:** Wenn Sie noch kein Azure-Abonnement haben, erstellen Sie ein Abonnement, indem Sie die Anweisungen auf dieser Seite befolgen oder [https://azure.microsoft.com/free](https://azure.microsoft.com/free) besuchen, um sich kostenlos zu registrieren.

- **Einrichten einer Azure DevOps-Organisation**: Wenn Sie nicht bereits eine Azure DevOps-Organisation haben, die Sie für dieses Lab verwenden können, müssen Sie diese erstellen, indem Sie die unter [Erstellen einer Organisation oder Projektsammlung](https://learn.microsoft.com/azure/devops/organizations/accounts/create-organization) beschriebenen Anweisungen befolgen.
  
- [Git for Windows](https://gitforwindows.org/): Downloadseite. Diese wird als Teil der Voraussetzungen für diese Übung installiert.

- [Visual Studio Code](https://code.visualstudio.com/). Diese wird als eine Voraussetzung für dieses Lab installiert.

- [Azure-Befehlszeilenschnittstelle](https://learn.microsoft.com/cli/azure/install-azure-cli). Installieren Sie die Azure CLI auf den selbst gehosteten Agent-Computern.

## Anweisungen zum Erstellen einer Azure DevOps-Organisation (Sie müssen dies nur einmal tun)

> **Hinweis**: Beginnen Sie mit Schritt 3, wenn Sie bereits über ein **persönliches Microsoft-Konto** und ein aktives Azure-Abonnement verfügen, das mit diesem Konto verknüpft ist.

1. Verwenden Sie eine private Browsersitzung, um ein neues **persönliches Microsoft-Konto (MSA)** zu erhalten unter `https://account.microsoft.com`.

1. Registrieren Sie sich mit derselben Browsersitzung für ein kostenloses Azure-Abonnement unter `https://azure.microsoft.com/free`.

1. Öffnen Sie einen Browser, und navigieren Sie zum Azure-Portal unter `https://portal.azure.com`, suchen Sie dann oben auf dem Azure-Portal-Bildschirm nach **Azure DevOps**. Klicken Sie auf der angezeigten Seite auf **Azure DevOps-Organisationen**.

1. Klicken Sie anschließend auf den Link **Meine Azure DevOps-Organisationen** oder navigieren Sie direkt zu `https://aex.dev.azure.com`.

1. Wählen Sie auf der Seite **Wir benötigen noch einige weitere Details** die Option **Weiter** aus.

1. Wählen Sie im Dropdownfeld auf der linken Seite die Option **Standardverzeichnis** anstelle von **Microsoft-Konto** aus.

1. Wenn Sie zu aufgefordert werden (*Wir benötigen noch einige weitere Details*), geben Sie Ihren Namen, Ihre E-Mail-Adresse und Ihren Standort an, und klicken Sie auf **Weiter**.

1. Zurück bei `https://aex.dev.azure.com` mit der Auswahl **Standardverzeichnis** klicken Sie auf die blaue Schaltfläche **Neue Organisation erstellen**.

1. Akzeptieren Sie die *Nutzungsbedingungen*, indem Sie auf **Fortfahren** klicken.

1. Wenn Sie dazu aufgefordert werden (*Fast fertig*), behalten Sie den Namen der Azure DevOps-Organisation standardmäßig bei (es muss sich um einen global eindeutigen Namen handeln), und wählen Sie einen Hostingstandort in Ihrer Nähe aus der Liste aus..

1. Nachdem die neu erstellte Organisation in **Azure DevOps** geöffnet wurde, wählen Sie die Option **Organisationseinstellungen** in der unteren linken Ecke aus.

1. Wählen Sie auf dem Bildschirm **Organisationseinstellungen** die Option **Abrechnung** aus (das Öffnen dieses Bildschirms dauert einige Sekunden).

1. Wählen Sie die Option **Abrechnung einrichten** und auf der rechten Seite des Bildschirms Ihr **Azure-Abonnement** und dann **Speichern** aus, um das Abonnement mit der Organisation zu verknüpfen.

1. Sobald der Bildschirm die verknüpfte Azure-Abonnement-ID oben anzeigt, ändern Sie die Anzahl der **Kostenpflichtigen Parallelaufträge** für **MS Hosted CI/CD** von 0 auf **1**. Klicken Sie anschließend unten auf die Schaltfläche **SPEICHERN**.

1. Sie können **mindestens 3 Stunden warten, bevor Sie die CI/CD-Funktionen** verwenden, damit die neuen Einstellungen im Back-End wiedergegeben werden. Andernfalls wird weiterhin die Meldung *Es wurde keine gehostete Parallelität gekauft oder gewährt* angezeigt.

## Anweisungen zum Erstellen und Konfigurieren des Azure DevOps-Projekts (Sie müssen dies nur einmal tun)

> **Hinweis**: Stellen Sie sicher, dass Sie die Schritte zur Erstellung Ihrer Azure DevOps Organisation abgeschlossen haben, bevor Sie mit diesen Schritten fortfahren.

Um alle Lab-Anweisungen zu befolgen, müssen Sie ein neues Azure DevOps-Projekt einrichten, ein Repository erstellen, das auf der [eShopOnWeb](https://github.com/MicrosoftLearning/eShopOnWeb)-Anwendung basiert, und eine Dienstverbindung mit Ihrem Azure-Abonnement erstellen.

### Teamprojekt erstellen

Zunächst erstellen Sie ein **eShopOnWeb** Azure DevOps-Projekt, das von mehreren Labs verwendet werden soll.

1. Öffnen Sie Ihren Browser, und navigieren Sie zu Ihrer Azure DevOps-Organisation.

1. Wählen Sie die Option **Neues Projekt** aus und verwenden Sie die folgenden Einstellungen:
   - Name: **eShopOnWeb**
   - Sichtbarkeit: **Privat**
   - Erweitert: Versionskontrolle: **Git**
   - Erweitert: Arbeitsaufgabenprozess: **Scrum**

1. Klicken Sie auf **Erstellen**.

   ![Erstellen eines Projekts](media/create-project.png)

### Importieren des eShopOnWeb-Git-Repositorys

Jetzt importieren Sie das eShopOnWeb in Ihr Git-Repository.

1. Öffnen Sie Ihren Browser, und navigieren Sie zu Ihrer Azure DevOps-Organisation.

1. Öffnen Sie das zuvor erstellte, **eShopOnWeb**-Projekt.

1. Wählen Sie **Repos > Dateien**, **Importieren eines Repositorys**, und dann **Importieren** aus.

1. Fügen Sie im Fenster **Git-Repository importieren** die folgende URL `https://github.com/MicrosoftLearning/eShopOnWeb` ein, und wählen Sie **Importieren** aus:

   ![Importieren eines Repositorys](media/import-repo.png)

1. Das Repository ist wie folgt organisiert:

   - Der Ordner **.ado** enthält Azure DevOps-YAML-Pipelines.
   - Der Ordner **.devcontainer** enthält ein Containersetup für die Entwicklung mithilfe von Containern (entweder lokal in VS Code oder über GitHub Codespaces).
   - Der Ordner **.azure** enthält eine Bicep- und ARM-Infrastruktur als Codevorlagen.
   - **.github**-Ordnercontainer-YAML-GitHub-Workflowdefinitionen.
   - Der Ordner **src** enthält die .NET 6-Website, die in den Labszenarien verwendet wird. 

1. Lassen Sie das Webbrowserfenster geöffnet.  

### Dienstprinzipal- und Dienstverbindung für den Zugriff auf Azure-Ressourcen erstellen

Als Nächstes erstellen Sie einen Dienstprinzipal mithilfe der Azure CLI und eine Dienstverbindung in Azure DevOps, mit der Sie Ressourcen in Ihrem Azure-Abonnement bereitstellen und darauf zugreifen können.

1. Starten Sie einen Webbrowser, navigieren Sie zum Azure-Portal unter `https://portal.azure.com`, und melden Sie sich mit Ihrem Benutzerkonto an, das in dem Azure-Abonnement, das Sie in den Labs dieses Kurses verwenden, über die Rolle „Besitzer*in“ und in dem Microsoft Entra-Mandanten, der diesem Abonnement zugeordnet ist, über die Rolle „Globale*r Administrator*in“ verfügt.

1. Wählen Sie im Azure-Portal das **Cloud Shell**-Symbol aus, das sich direkt rechts neben dem Suchtextfeld oben auf der Seite befindet.

1. Wählen Sie bei Aufforderung zur Auswahl von **Bash** oder **PowerShell** die Option **Bash** aus.

   > [!NOTE]
   > Wenn Sie **Cloud Shell** zum ersten Mal starten und die Meldung **Für Sie wurde kein Speicher bereitgestellt.** angezeigt wird, wählen Sie das in diesem Lab verwendete Abonnement und anschließend **Speicher erstellen** aus.

1. Führen Sie an der Eingabeaufforderung **Bash** im Bereich **Cloud Shell** die folgenden Befehle aus, um die Werte der Attribute „Azure-Abonnement-ID“ und „Abonnementname“ abzurufen:

   ```bash
   subscriptionName=$(az account show --query name --output tsv)
   subscriptionId=$(az account show --query id --output tsv)
   echo $subscriptionName
   echo $subscriptionId
   ```

   > [!NOTE]
   > Kopieren Sie beide Werte in eine Textdatei. Sie werden sie in den Labs dieses Kurses benötigen.

1. Führen Sie in der **Bash**-Eingabeaufforderung im **Cloud Shell**-Bereich den folgenden Befehl aus, um einen Dienstprinzipal zu erstellen:

   ```bash
   az ad sp create-for-rbac --name sp-eshoponweb-azdo --role contributor --scopes /subscriptions/$subscriptionId
   ```

   > [!NOTE]
   > Dieser Befehl erstellt eine JSON-Datei. Kopieren Sie die Ausgabe in eine Textdatei. Sie benötigen ihn später.

   > [!NOTE]
   > Notieren Sie den „Wert von“, den „Namen des Sicherheitsprinzipals“, seine „ID“ und die „Mandanten-ID“, die in der JSON-Ausgabe enthalten ist. Sie werden sie in den Labs dieses Kurses benötigen.

1. Wechseln Sie zurück zum Webbrowserfenster, in dem das Azure DevOps-Portal mit dem **eShopOnWeb**-Projekt geöffnet ist, und wählen Sie **Projekteinstellungen** in der unteren linken Ecke des Portals aus.

1. Wählen Sie zunächst unter Pipelines die Option **Dienstverbindungen** und dann die Schaltfläche **Dienstverbindung erstellen** aus.

   ![Screenshot der Schaltfläche zum Erstellen der neuen Dienstverbindung.](media/new-service-connection.png)

1. Wählen Sie im Bildschirm **Neue Dienstverbindung** die Option **Azure Resource Manager** und anschließend **Weiter** aus (Sie müssen möglicherweise scrollen).

1. Wählen Sie dann zunächst **Dienstprinzipal (manuell)** und anschließend **Weiter** aus.

1. Füllen Sie die leeren Felder mit den Informationen aus, die während der vorherigen Schritte gesammelt wurden:

   - Abonnement-ID und -Name.
   - Dienstprinzipal-ID (oder clientId/AppId), Dienstprinzipalschlüssel (oder Kennwort) und TenantId.
   - Geben Sie in **Name der Dienstverbindung** **azure subs** ein. Auf diesen Namen wird in YAML-Pipelines verwiesen, um auf die Dienstverbindung zu verweisen, damit Sie auf Ihr Azure-Abonnement zugreifen können.

   ![Screenshot der Konfiguration der Azure-Dienstverbindung.](media/azure-service-connection.png)

1. Aktiviren Sie nicht das Kontrollkästchen **Allen Pipelines die Zugriffsberechtigung gewähren**. Wählen Sie **Überprüfen und Speichern**.

   > [!NOTE]
   > Die Option **Allen Pipelines die Zugriffsberechtigung gewähren** wird für Produktionsumgebungen nicht empfohlen. Sie wird nur in diesem Lab verwendet, um die Konfiguration der Pipeline zu vereinfachen.

Sie haben nun die erforderlichen erforderlichen Schritte abgeschlossen, um mit den Labs fortzufahren.
