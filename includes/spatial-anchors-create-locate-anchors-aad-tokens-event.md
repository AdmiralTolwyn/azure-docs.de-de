---
ms.openlocfilehash: 7219a457a2631f9ff6beee06eff34bce0ff5a23f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/24/2020
ms.locfileid: "67177793"
---
Wenn ein Azure AD-Token nicht festgelegt ist, müssen Sie wie bei Zugriffstoken das TokenRequired-Ereignis behandeln oder die tokenRequired-Methode auf dem Delegatenprotokoll implementieren.

Sie können das Ereignis synchron behandeln, indem Sie die Eigenschaft für die Ereignisargumente festlegen.
