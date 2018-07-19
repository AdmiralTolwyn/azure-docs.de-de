---
title: Verwenden von Terraform mit Azure
description: Einführung in die Verwendung von Terraform zur Versionserstellung und Bereitstellung für die Azure-Infrastruktur.
ms.service: virtual-machines-linux
keywords: Terraform, Devops, Übersicht, plan, apply, automate
author: binderjoe
ms.author: jbinder
ms.date: 10/19/2017
ms.topic: article
ms.openlocfilehash: 5d313bda6a1067e6d023f62fb26704f9aee5c7bf
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/29/2018
ms.locfileid: "37114471"
---
# <a name="terraform-with-azure"></a>Terraform mit Azure

[Hashicorp Terraform](https://www.terraform.io/) ist ein Open Source-Tool für die Bereitstellung und Verwaltung von Cloudinfrastrukturen. Es wird die Infrastruktur in Konfigurationsdateien codiert, die die Topologie von Cloudressourcen wie virtuelle Computern, Speicherkonten und Netzwerkschnittstellen beschreiben. Die Befehlszeilenschnittstelle von Terraform bietet einen einfachen Mechanismus zum Bereitstellen und für die Versionserstellung für Konfigurationsdateien in Azure oder einer beliebigen anderen unterstützten Cloud.

In diesem Artikel werden die Vorteile bei der Verwendung von Terraform zum Verwalten der Azure-Infrastruktur beschrieben.

## <a name="automate-infrastructure-management"></a>Automatisieren der Infrastrukturverwaltung

Die vorlagenbasierten Konfigurationsdateien von Terraform ermöglichen es Ihnen, Azure-Ressourcen in wiederholbarer und vorhersagbarer Weise zu definieren, bereitzustellen und zu konfigurieren. Das Automatisieren der Infrastruktur hat mehrere Vorteile:

- Verringert die Wahrscheinlichkeit von menschlichem Fehlverhalten bei der Bereitstellung und Verwaltung der Infrastruktur.
- Stellt dieselbe Vorlage mehrmals bereit, um identische Entwicklungs-, Test- und Produktionsumgebungen zu erzeugen.
- Verringert die Kosten für Entwicklungs- und Testumgebungen, indem diese bei Bedarf erstellt werden.

## <a name="understand-infrastructure-changes-before-they-are-applied"></a>Verstehen der Infrastrukturänderungen vor deren Anwendung 

Wenn eine Ressourcentopologie komplex wird, kann es schwierig sein, die Bedeutung und Auswirkung von Infrastrukturänderungen zu verstehen.

Terraform bietet eine Befehlszeilenschnittstelle (CLI), mit der Benutzer Infrastrukturänderungen vor deren Bereitstellung überprüfen und in einer Vorschau anzeigen können. Das Anzeigen von Infrastrukturänderungen auf sichere, produktive Weise in einer Vorschau hat mehrere Vorteile:
- Teammitglieder können effektiver zusammenarbeiten, indem sie vorgeschlagene Änderungen und deren Auswirkung schnell verstehen.
- Unbeabsichtigte Änderungen können frühzeitig im Entwicklungsprozess abgefangen werden.


## <a name="deploy-infrastructure-to-multiple-clouds"></a>Bereitstellen der Infrastruktur in mehreren Clouds

Terraform wird häufig als Tool für Szenarios mit mehreren Clouds gewählt, in denen eine ähnliche Infrastruktur für Azure und zusätzliche Cloudanbieter oder für lokale Rechenzentren bereitgestellt wird. Entwicklern wird ermöglicht, dieselben Tools und Konfigurationsdateien zum Verwalten der Infrastruktur für mehrere Cloudanbieter zu verwenden.

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie nun eine Übersicht über Terraform und seine Vorteile haben, folgen hier die empfohlenen nächsten Schritte:

- Erste Schritte durch die [Installation von Terraform und seiner Konfiguration für die Verwendung von Azure](https://docs.microsoft.com/azure/virtual-machines/linux/terraform-install-configure)
- [Erstellen eines virtuellen Azure-Computers mit Terraform](https://docs.microsoft.com/azure/virtual-machines/linux/terraform-create-complete-vm)
- Untersuchen des [Azure Resource Manager-Moduls für Terraform](https://www.terraform.io/docs/providers/azurerm/) 