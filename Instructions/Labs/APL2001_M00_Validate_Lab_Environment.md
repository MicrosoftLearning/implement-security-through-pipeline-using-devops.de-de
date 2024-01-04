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
  
- [Git for Windows](https://gitforwindows.org/): Downloadseite. Diese wird als eine Voraussetzung für dieses Lab installiert.

- [Visual Studio Code](https://code.visualstudio.com/). Diese wird als eine Voraussetzung für dieses Lab installiert.

- [Azure-Befehlszeilenschnittstelle](https://learn.microsoft.com/cli/azure/install-azure-cli). Installieren Sie die Azure CLI auf den selbst gehosteten Agent-Computern.

## Anweisungen zum Erstellen einer Azure DevOps-Organisation (Sie müssen dies nur einmal tun)

> **Hinweis**: Beginnen Sie mit Schritt 3, wenn Sie bereits über ein **persönliches Microsoft-Konto** und ein aktives Azure-Abonnement verfügen, das mit diesem Konto verknüpft ist.

1. Verwenden Sie eine private Browsersitzung, um ein neues **persönliches Microsoft-Konto (MSA)** zu erhalten unter `https://account.microsoft.com`.

1. Registrieren Sie sich mit derselben Browsersitzung für ein kostenloses Azure-Abonnement unter `https://azure.microsoft.com/free`.

1. Öffnen Sie einen Browser, und navigieren Sie zum Azure-Portal unter `https://portal.azure.com`, suchen Sie dann oben auf dem Azure-Portal-Bildschirm nach **Azure DevOps**. Klicken Sie auf der angezeigten Seite auf **Azure DevOps-Organisationen**.

1. Klicken Sie als Nächstes auf den Link mit der Bezeichnung **Meine Azure DevOps-Organisationen**, oder navigieren Sie direkt zu `https://aex.dev.azure.com`.

1. Wählen Sie auf der Seite **Wir benötigen noch einige weitere Details** die Option **Weiter** aus.

1. Wählen Sie im Dropdownfeld auf der linken Seite die Option **Standardverzeichnis** anstelle von **Microsoft-Konto** aus.

1. Wenn Sie zu aufgefordert werden (*Wir benötigen noch einige weitere Details*), geben Sie Ihren Namen, Ihre E-Mail-Adresse und Ihren Standort an, und klicken Sie auf **Weiter**.

1. Zurück auf `https://aex.dev.azure.com` mit ausgewähltem **Standardverzeichnis** klicken Sie auf die blaue Schaltfläche  **Neue Organisation erstellen**.

1. Akzeptieren Sie die *Vertragsbedingungen*, indem Sie auf **Weiter** klicken.

1. Wenn Sie dazu aufgefordert werden (*Fast fertig*), behalten Sie den Namen der Azure DevOps-Organisation standardmäßig bei (es muss sich um einen global eindeutigen Namen handeln), und wählen Sie einen Hostingstandort in Ihrer Nähe aus der Liste aus..

1. Nachdem die neu erstellte Organisation in **Azure DevOps** geöffnet wurde, wählen Sie die Option **Organisationseinstellungen** in der unteren linken Ecke aus.

1. Wählen Sie auf dem Bildschirm **Organisationseinstellungen** die Option **Abrechnung** aus (das Öffnen dieses Bildschirms dauert einige Sekunden).

1. Wählen Sie die Option **Abrechnung einrichten** und auf der rechten Seite des Bildschirms Ihr **Azure-Abonnement** und dann **Speichern** aus, um das Abonnement mit der Organisation zu verknüpfen.

1. Sobald der Bildschirm die verknüpfte Azure-Abonnement-ID oben anzeigt, ändern Sie die Anzahl der **Kostenpflichtigen Parallelaufträge** für **MS Hosted CI/CD** von 0 auf **1**. Klicken Sie anschließend unten auf die Schaltfläche **SPEICHERN**.

1. Sie können **mindestens 3 Stunden warten, bevor Sie die CI/CD-Funktionen** verwenden, damit die neuen Einstellungen im Back-End wiedergegeben werden. Andernfalls wird weiterhin die Meldung *Es wurde keine gehostete Parallelität gekauft oder gewährt* angezeigt.

## Anweisungen zum Erstellen des Azure DevOps-Beispielprojekts (Sie müssen dies nur einmal tun)

> **Hinweis**: Stellen Sie sicher, dass Sie die Schritte zum Erstellen Ihrer Azure DevOps-Organisation abgeschlossen haben, bevor Sie mit diesen Schritten fortfahren.

Um alle Lab-Anweisungen zu befolgen, müssen Sie ein neues Azure DevOps-Projekt einrichten und ein Repository erstellen, das auf der [eShopOnWeb](https://github.com/MicrosoftLearning/eShopOnWeb)-Anwendung basiert.

### Erstellen und Konfigurieren des Teamprojekts

Zunächst erstellen Sie ein **eShopOnWeb** Azure DevOps-Projekt, das von mehreren Labs verwendet werden soll.

1. Öffnen Sie Ihren Browser, und navigieren Sie zu Ihrer Azure DevOps-Organisation.

1. Wählen Sie die Option **Neues Projekt** aus und verwenden Sie die folgenden Einstellungen:
   - Name: **eShopOnWeb**
   - Sichtbarkeit: **Privat**
   - Erweitert: Versionssteuerung: **Git**
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
    
    - Der Ordner **.ado** enthält Azure DevOps-YAML-Pipelines.
    - Der Ordner **.devcontainer** enthält ein Containersetup für die Entwicklung mithilfe von Containern (entweder lokal in VS Code oder über GitHub Codespaces).
    - Der Ordner **.azure** enthält eine Bicep- und ARM-Infrastruktur als Codevorlagen.
    - **.github**-Ordnercontainer-YAML-GitHub-Workflowdefinitionen.
    - Der Ordner **src** enthält die .NET 6-Website, die in den Labszenarien verwendet wird.

Sie haben nun die erforderlichen erforderlichen Schritte abgeschlossen, um mit den Labs fortzufahren.
