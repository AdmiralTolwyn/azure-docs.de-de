---
title: "Richtlinienausdrücke in Azure API Management | Microsoft-Dokumentation"
description: "Erfahren Sie mehr über Richtlinienausdrücke in Azure API Management."
services: api-management
documentationcenter: 
author: vladvino
manager: erikre
editor: 
ms.assetid: ea160028-fc04-4782-aa26-4b8329df3448
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2017
ms.author: apimpm
ms.openlocfilehash: fb50ba3f292a390c45f1afe6259731d2b92cc335
ms.sourcegitcommit: 7edfa9fbed0f9e274209cec6456bf4a689a4c1a6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/17/2018
---
# <a name="api-management-policy-expressions"></a>Richtlinienausdrücke in API Management
Die Syntax für Richtlinienausdrücke entspricht C# 6.0. Jeder Ausdruck besitzt Zugriff auf die implizit bereitgestellte [Kontextvariable](api-management-policy-expressions.md#ContextVariables) und eine zulässige [Teilmenge](api-management-policy-expressions.md#CLRTypes) von .NET Framework-Typen.  
  
> [!TIP]
>  Weitere Informationen zu Richtlinienausdrücken finden Sie im Video zu [Richtlinienausdrücken](https://azure.microsoft.com/documentation/videos/policy-expressions-in-azure-api-management/).  
>   
>  Demonstrationen der Konfiguration von Richtlinien und der Verwendung von Richtlinienausdrücken finden Sie unter [Cloud Cover Episode 177: More API Management Features with Vlad Vinogradsky](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) (Cloud Cover-Episode 177 zu weiteren API Management-Funktionen mit Vlad Vinogradsky). Dieses Video enthält die folgenden Demonstrationen von Richtlinienausdrücken:  
>   
>  -   10:30 – Hier wird das Bereitstellen von Kontextinformationen für Ihren Back-End-Dienst gezeigt. Verwenden Sie die Richtlinien [Abfragezeichenfolgenparameter festlegen](api-management-transformation-policies.md#SetQueryStringParameter) und [HTTP-Header festlegen](api-management-transformation-policies.md#SetHTTPheader), um diese Informationen bereitzustellen. Bei 12:10 wird das Aufrufen eines Vorgangs im Entwicklerportal gezeigt, sodass Sie diese Richtlinien in Aktion sehen können.  
> -   13:50 – Hier wird die Verwendung der Richtlinie [JWT überprüfen](api-management-access-restriction-policies.md#ValidateJWT) gezeigt, um den Zugriff auf Vorgänge basierend auf Tokenansprüchen vorab zu autorisieren. Spulen Sie bis 15:00 vor, um zu sehen, wie die Richtlinien im Richtlinien-Editor konfiguriert werden. Bei 18:50 wird das Aufrufen eines Vorgangs aus dem Entwicklerportal sowohl mit als auch ohne das benötigte Autorisierungstoken gezeigt.  
> -   21:00 – Hier wird die Verwendung einer [API-Inspektor](https://azure.microsoft.com/documentation/articles/api-management-howto-api-inspector/)-Ablaufverfolgung veranschaulicht, um die Auswertung von Richtlinien und die Ergebnisse dieser Auswertungen anzuzeigen.  
> -   25:25 – Hier wird gezeigt, wie Ausdrücke mit den Richtlinien [Aus Cache abrufen](api-management-caching-policies.md#GetFromCache) und [In Cache speichern](api-management-caching-policies.md#StoreToCache) verwendet werden, um das Zwischenspeichern von Antworten für API Management zu konfigurieren. Legen Sie eine Dauer fest, die dem Zwischenspeichern von Antworten des Back-End-Diensts entspricht, wie in der `Cache-Control`-Anweisung des Back-End-Diensts angegeben.  
> -   34:30 – Hier wird gezeigt, wie Inhalte gefiltert werden. Entfernen Sie Datenelemente mit den Richtlinien [Ablaufsteuerung](api-management-advanced-policies.md#choose) und [Text festlegen](api-management-transformation-policies.md#SetBody) aus der vom Back-End empfangenen Antwort. Beginnen Sie bei 31:50, um eine Übersicht über die [API „The Dark Sky Forecast“](https://developer.forecast.io/) zu erhalten, die für diese Demo verwendet wird.  
> -   Informationen zum Herunterladen der in diesem Video verwendeten Richtlinienanweisungen finden Sie im Github-Repository unter [api-management-samples/policies](https://github.com/Azure/api-management-samples/tree/master/policies).  
  
  
##  <a name="Syntax"></a> Syntax  
 Ausdrücke mit einer einzelnen Anweisung werden in `@(expression)` eingeschlossen, wobei `expression` eine wohlgeformte C#-Ausdrucksanweisung ist.  
  
 Ausdrücke mit mehreren Anweisungen werden in `@{expression}` eingeschlossen. Alle Codepfade in Ausdrücken mit mehreren Anweisungen müssen mit einer `return`-Anweisung enden.  
  
##  <a name="PolicyExpressionsExamples"></a> Beispiele  
  
```  
@(true)  
  
@((1+1).ToString())  
  
@("Hi There".Length)  
  
@(Regex.Match(context.Response.Headers.GetValueOrDefault("Cache-Control",""), @"max-age=(?<maxAge>\d+)").Groups["maxAge"]?.Value)  
  
@(context.Variables.ContainsKey("maxAge") ? int.Parse((string)context.Variables["maxAge"]) : 3600)  
  
@{   
  string value;   
  if (context.Request.Headers.TryGetValue("Authorization", out value))   
  {   
    return Encoding.UTF8.GetString(Convert.FromBase64String(value));  
  }   
  else   
  {   
    return null;  
  }  
}  
```  
  
##  <a name="PolicyExpressionsUsage"></a> Verwendung  
 Ausdrücke können als Attributwerte oder Textwerte in beliebigen API Management-[Richtlinien](api-management-policies.md) verwendet werden (sofern in der Richtlinienreferenz nicht anders angegeben).  
  
> [!IMPORTANT]
>  Bei Verwendung von Richtlinienausdrücken erfolgt nur eine begrenzte Überprüfung der Richtlinienausdrücke beim Definieren der Richtlinie. Ausdrücke werden vom Gateway zur Laufzeit ausgeführt, und durch Richtlinienausdrücke generierte Ausnahmen führen zu einem Laufzeitfehler.  
  
##  <a name="CLRTypes"></a> In Richtlinienausdrücken zulässige .NET Framework-Typen  
 Die folgende Tabelle enthält die .NET Framework-Typen und die zugehörigen Mitglieder, die in Richtlinienausdrücken zulässig sind.  
  
|CLR-Typ|Unterstützte Methoden|  
|--------------|-----------------------|  
|Newtonsoft.Json.Linq.Extensions|Alle Methoden werden unterstützt.|  
|Newtonsoft.Json.Linq.JArray|Alle Methoden werden unterstützt.|  
|Newtonsoft.Json.Linq.JConstructor|Alle Methoden werden unterstützt.|  
|Newtonsoft.Json.Linq.JContainer|Alle Methoden werden unterstützt.|  
|Newtonsoft.Json.Linq.JObject|Alle Methoden werden unterstützt.|  
|Newtonsoft.Json.Linq.JProperty|Alle Methoden werden unterstützt.|  
|Newtonsoft.Json.Linq.JRaw|Alle Methoden werden unterstützt.|  
|Newtonsoft.Json.Linq.JToken|Alle Methoden werden unterstützt.|  
|Newtonsoft.Json.Linq.JTokenType|Alle Methoden werden unterstützt.|  
|Newtonsoft.Json.Linq.JValue|Alle Methoden werden unterstützt.|  
|System.Collections.Generic.IReadOnlyCollection<T\>|Alle|  
|System.Collections.Generic.IReadOnlyDictionary<TKey,  TValue>|Alle|  
|System.Collections.Generic.ISet<TKey, TValue>|Alle|  
|System.Collections.Generic.KeyValuePair<TKey,  TValue>|Key, Value|  
|System.Collections.Generic.List<TKey, TValue>|Alle|  
|System.Collections.Generic.Queue<TKey, TValue>|Alle|  
|System.Collections.Generic.Stack<TKey, TValue>|Alle|  
|System.Convert|Alle|  
|System.DateTime|Alle|  
|System.DateTimeKind|UTC|  
|System.DateTimeOffset|Alle|  
|System.Decimal|Alle|  
|System.Double|Alle|  
|System.Guid|Alle|  
|System.IEnumerable<T\>|Alle|  
|System.IEnumerator<T\>|Alle|  
|System.Int16|Alle|  
|System. Int32|Alle|  
|System.Int64|Alle|  
|System.Linq.Enumerable<T\>|Alle Methoden werden unterstützt.|  
|System.Math|Alle|  
|System.MidpointRounding|Alle|  
|System.Nullable<T\>|Alle|  
|System.Random|Alle|  
|System.SByte|Alle|  
|System.Security.Cryptography. HMACSHA384|Alle|  
|System.Security.Cryptography. HMACSHA512|Alle|  
|System.Security.Cryptography.HashAlgorithm|Alle|  
|System.Security.Cryptography.HMAC|Alle|  
|System.Security.Cryptography.HMACMD5|Alle|  
|System.Security.Cryptography.HMACSHA1|Alle|  
|System.Security.Cryptography.HMACSHA256|Alle|  
|System.Security.Cryptography.KeyedHashAlgorithm|Alle|  
|System.Security.Cryptography.MD5|Alle|  
|System.Security.Cryptography.RNGCryptoServiceProvider|Alle|  
|System.Security.Cryptography.SHA1|Alle|  
|System.Security.Cryptography.SHA1Managed|Alle|  
|System.Security.Cryptography.SHA256|Alle|  
|System.Security.Cryptography.SHA256Managed|Alle|  
|System.Security.Cryptography.SHA384|Alle|  
|System.Security.Cryptography.SHA384Managed|Alle|  
|System.Security.Cryptography.SHA512|Alle|  
|System.Security.Cryptography.SHA512Managed|Alle|  
|System.Single|Alle|  
|System.String|Alle|  
|System.StringSplitOptions|Alle|  
|System.Text.Encoding|Alle|  
|System.Text.RegularExpressions.Capture|Index, Length, Value|  
|System.Text.RegularExpressions.CaptureCollection|Count, Item|  
|System.Text.RegularExpressions.Group|Captures, Success|  
|System.Text.RegularExpressions.GroupCollection|Count, Item|  
|System.Text.RegularExpressions.Match|Empty, Groups, Result|  
|System.Text.RegularExpressions.Regex|(Konstruktor), IsMatch, Match, Matches, Replace|  
|System.Text.RegularExpressions.RegexOptions|Compiled, IgnoreCase, IgnorePatternWhitespace, Multiline, None, RightToLeft, Singleline|  
|System.TimeSpan|Alle|  
|System.Tuple|Alle|  
|System.UInt16|Alle|  
|System.UInt32|Alle|  
|System.UInt64|Alle|  
|System.Uri|Alle|  
|System.Xml.Linq.Extensions|Alle Methoden werden unterstützt.|  
|System.Xml.Linq.XAttribute|Alle Methoden werden unterstützt.|  
|System.Xml.Linq.XCData|Alle Methoden werden unterstützt.|  
|System.Xml.Linq.XComment|Alle Methoden werden unterstützt.|  
|System.Xml.Linq.XContainer|Alle Methoden werden unterstützt.|  
|System.Xml.Linq.XDeclaration|Alle Methoden werden unterstützt.|  
|System.Xml.Linq.XDocument|Alle Methoden werden unterstützt.|  
|System.Xml.Linq.XDocumentType|Alle Methoden werden unterstützt.|  
|System.Xml.Linq.XElement|Alle Methoden werden unterstützt.|  
|System.Xml.Linq.XName|Alle Methoden werden unterstützt.|  
|System.Xml.Linq.XNamespace|Alle Methoden werden unterstützt.|  
|System.Xml.Linq.XNode|Alle Methoden werden unterstützt.|  
|System.Xml.Linq.XNodeDocumentOrderComparer|Alle Methoden werden unterstützt.|  
|System.Xml.Linq.XNodeEqualityComparer|Alle Methoden werden unterstützt.|  
|System.Xml.Linq.XObject|Alle Methoden werden unterstützt.|  
|System.Xml.Linq.XProcessingInstruction|Alle Methoden werden unterstützt.|  
|System.Xml.Linq.XText|Alle Methoden werden unterstützt.|  
|System.Xml.XmlNodeType|Alle|  
  
##  <a name="ContextVariables"></a> Kontextvariable  
 Eine Variable namens `context` steht implizit in jedem [Richtlinienausdruck](api-management-policy-expressions.md#Syntax) zur Verfügung. Ihre Mitglieder bieten Informationen zu `\request`. Alle `context`-Mitglieder sind schreibgeschützt.  
  
|Kontextvariable|Zulässige Methoden, Eigenschaften und Parameterwerte|  
|----------------------|-------------------------------------------------------|  
|context|Api: IApi<br /><br /> Bereitstellung<br /><br /> Elapsed: TimeSpan – Zeitintervall zwischen dem Wert des Zeitstempels und der aktuellen Uhrzeit<br /><br /> LastError<br /><br /> Vorgang<br /><br /> Produkt<br /><br /> Anforderung<br /><br /> RequestId: Guid – eindeutiger Bezeichner der Anforderung<br /><br /> response<br /><br /> Abonnement<br /><br /> Timestamp: DateTime – Zeitpunkt des Empfangs der Anforderung<br /><br /> Tracing: bool – gibt an, ob die Ablaufverfolgung aktiviert oder deaktiviert ist <br /><br /> Benutzer<br /><br /> Variables: IReadOnlyDictionary<string, object><br /><br /> void Trace(message: string)|  
|context.Api|Id: string<br /><br /> IsRevisionCurrent: bool<br /><br />  Name: string<br /><br /> Path: string<br /><br /> Revision: string<br /><br /> ServiceUrl: IUrl<br /><br /> Version: string |  
|context.Deployment|Region: string<br /><br /> ServiceName: string<br /><br /> Certificates: IReadOnlyDictionary<string, X509Certificate2>|  
|context.LastError|Source: string<br /><br /> Reason: string<br /><br /> Message: string<br /><br /> Scope: string<br /><br /> Section: string<br /><br /> Path: string<br /><br /> PolicyId: string<br /><br /> Weitere Informationen zu context.LastError finden Sie unter [Fehlerbehandlung](api-management-error-handling-policies.md).|  
|context.Operation|Id: string<br /><br /> Method: string<br /><br /> Name: string<br /><br /> UrlTemplate: string|  
|context.Product|Apis: IEnumerable<IApi\><br /><br /> ApprovalRequired: bool<br /><br /> Groups: IEnumerable<IGroup\><br /><br /> Id: string<br /><br /> Name: string<br /><br /> State: enum ProductState {NotPublished, Published}<br /><br /> SubscriptionLimit: int?<br /><br /> SubscriptionRequired: bool|  
|context.Request|Body: IMessageBody<br /><br /> Certificate: System.Security.Cryptography.X509Certificates.X509Certificate2<br /><br /> Headers: IReadOnlyDictionary<string, string[]><br /><br /> IpAddress: string<br /><br /> MatchedParameters: IReadOnlyDictionary<string, string><br /><br /> Method: string<br /><br /> OriginalUrl:IUrl<br /><br /> Url: IUrl|  
|string context.Request.Headers.GetValueOrDefault(headerName: string, defaultValue: string)|headerName: string<br /><br /> defaultValue: string<br /><br /> Gibt durch Trennzeichen getrennte Anforderungsheaderwerte oder `defaultValue` zurück, wenn der Header nicht gefunden wurde.|  
|context.Response|Body: IMessageBody<br /><br /> Headers: IReadOnlyDictionary<string, string[]><br /><br /> StatusCode: int<br /><br /> StatusReason: string|  
|string context.Response.Headers.GetValueOrDefault(headerName: string, defaultValue: string)|headerName: string<br /><br /> defaultValue: string<br /><br /> Gibt durch Trennzeichen getrennte Antwortheaderwerte oder `defaultValue` zurück, wenn der Header nicht gefunden wurde.|  
|context.Subscription|CreatedTime: DateTime<br /><br /> EndDate: DateTime?<br /><br /> Id: string<br /><br /> Key: string<br /><br /> Name: string<br /><br /> PrimaryKey: string<br /><br /> SecondaryKey: string<br /><br /> StartDate: DateTime?|  
|context.User|Email: string<br /><br /> FirstName: string<br /><br /> Groups: IEnumerable<IGroup\><br /><br /> Id: string<br /><br /> Identities: IEnumerable<IUserIdentity\><br /><br /> LastName: string<br /><br /> Note: string<br /><br /> RegistrationDate: DateTime|  
|IApi|Id: string<br /><br /> Name: string<br /><br /> Path: string<br /><br /> Protocols: IEnumerable<string\><br /><br /> ServiceUrl: IUrl<br /><br /> SubscriptionKeyParameterNames: ISubscriptionKeyParameterNames|  
|IGroup|Id: string<br /><br /> Name: string|  
|IMessageBody|As<T\>(preserveContent: bool = false): Where T: string, JObject, JToken, JArray, XNode, XElement, XDocument<br /><br /> Mit der `context.Request.Body.As<T>`-Methode und der `context.Response.Body.As<T>`-Methode werden ein Anforderungs- und ein Antwortnachrichtentext in einem angegebenen Typ `T` gelesen. Standardmäßig verwendet die Methode den Datenstrom des Originalnachrichtentexts und sorgt dafür, dass er nach seiner Rückgabe nicht mehr verfügbar ist. Wenn dies vermieden werden und die Methode eine Kopie des Textdatenstroms verarbeiten soll, legen Sie den `preserveContent`-Parameter auf `true` fest. Ein Beispiel finden Sie [hier](api-management-transformation-policies.md#SetBody).|  
|IUrl|Host: string<br /><br /> Path: string<br /><br /> Port: int<br /><br /> Query: IReadOnlyDictionary<string, string[]><br /><br /> QueryString: string<br /><br /> Scheme: string|  
|IUserIdentity|Id: string<br /><br /> Provider: string|  
|ISubscriptionKeyParameterNames|Header: string<br /><br /> Query: string|  
|string IUrl.Query.GetValueOrDefault(queryParameterName: string, defaultValue: string)|queryParameterName: string<br /><br /> defaultValue: string<br /><br /> Gibt durch Trennzeichen getrennte Abfrageparameterwerte oder `defaultValue` zurück, wenn der Parameter nicht gefunden wurde.|  
|T context.Variables.GetValueOrDefault<T\>(variableName: string, defaultValue: T)|variableName: string<br /><br /> defaultValue: T<br /><br /> Gibt eine Variablenwertumwandlung in den Typ `T` oder `defaultValue` zurück, wenn die Variable nicht gefunden wird.<br /><br /> Diese Methode löst eine Ausnahme aus, wenn der angegebene Typ nicht mit dem tatsächlichen Typ der zurückgegebenen Variablen übereinstimmt.|  
|BasicAuthCredentials AsBasic(input: this string)|input: string<br /><br /> Wenn der Eingabeparameter einen gültigen Anforderungsheaderwert für die Autorisierung der HTTP-Standardauthentifizierung enthält, gibt die Methode ein Objekt des Typs `BasicAuthCredentials` zurück; andernfalls gibt die Methode NULL zurück.|  
|bool TryParseBasic(input: this string, result: out BasicAuthCredentials)|input: string<br /><br /> result: out BasicAuthCredentials<br /><br /> Wenn der Eingabeparameter einen gültigen Wert für die HTTP-Standardauthentifizierungsautorisierung im Anforderungsheader enthält, gibt die Methode `true` zurück, und der Ergebnisparameter enthält einen Wert des Typs `BasicAuthCredentials`. Andernfalls gibt die Methode `false` zurück.|  
|BasicAuthCredentials|Password: string<br /><br /> UserId: string|  
|Jwt AsJwt(input: this string)|input: string<br /><br /> Wenn der Eingabeparameter einen gültigen JWT-Tokenwert enthält, gibt die Methode ein Objekt des Typs `Jwt` zurück; andernfalls gibt die Methode `null` zurück.|  
|bool TryParseJwt(input: this string, result: out Jwt)|input: string<br /><br /> result: out Jwt<br /><br /> Wenn der Eingabeparameter einen gültigen JWT-Tokenwert enthält, gibt die Methode `true` zurück, und der Ergebnisparameter enthält einen Wert des Typs `Jwt`; andernfalls gibt die Methode `false` zurück.|  
|Jwt|Algorithm: string<br /><br /> Audience: IEnumerable<string\><br /><br /> Claims: IReadOnlyDictionary<string, string[]><br /><br /> ExpirationTime: DateTime?<br /><br /> Id: string<br /><br /> Issuer: string<br /><br /> NotBefore: DateTime?<br /><br /> Subject: string<br /><br /> Type: string|  
|string Jwt.Claims.GetValueOrDefault(claimName: string, defaultValue: string)|claimName: string<br /><br /> defaultValue: string<br /><br /> Gibt durch Trennzeichen getrennte Anspruchswerte oder `defaultValue` zurück, wenn der Header nicht gefunden wurde.|
|byte[] Encrypt(input: this byte[], alg: string, key:byte[], iv:byte[])|input - zu verschlüsselnder Klartext<br /><br />alg - Name eines symmetrischen Verschlüsselungsalgorithmus<br /><br />key - Verschlüsselungsschlüssel<br /><br />iv - Initialisierungsvektor<br /><br />Gibt verschlüsselten Klartext zurück.|
|byte[] Encrypt(input: this byte[], alg: System.Security.Cryptography.SymmetricAlgorithm)|input - zu verschlüsselnder Klartext<br /><br />alg - Verschlüsselungsalgorithmus<br /><br />Gibt verschlüsselten Klartext zurück.|
|byte[] Encrypt(input: this byte[], alg: System.Security.Cryptography.SymmetricAlgorithm, key:byte[], iv:byte[])|input - zu verschlüsselnder Klartext<br /><br />alg - Verschlüsselungsalgorithmus<br /><br />key - Verschlüsselungsschlüssel<br /><br />iv - Initialisierungsvektor<br /><br />Gibt verschlüsselten Klartext zurück.|
|byte[] Decrypt(input: this byte[], alg: string, key:byte[], iv:byte[])|input - zu entschlüsselnder Verschlüsselungstext<br /><br />alg - Name eines symmetrischen Verschlüsselungsalgorithmus<br /><br />key - Verschlüsselungsschlüssel<br /><br />iv - Initialisierungsvektor<br /><br />Gibt Klartext zurück.|
|byte[] Decrypt(input: this byte[], alg: System.Security.Cryptography.SymmetricAlgorithm)|input - zu entschlüsselnder Verschlüsselungstext<br /><br />alg - Verschlüsselungsalgorithmus<br /><br />Gibt Klartext zurück.|
|byte[] Decrypt(input: this byte[], alg: System.Security.Cryptography.SymmetricAlgorithm, key:byte[], iv:byte[])|input - Eingabe – zu entschlüsselnder Verschlüsselungstext<br /><br />alg - Verschlüsselungsalgorithmus<br /><br />key - Verschlüsselungsschlüssel<br /><br />iv - Initialisierungsvektor<br /><br />Gibt Klartext zurück.|

## <a name="video"></a>Video

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Policy-Expressions-in-Azure-API-Management/player] 
>
## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Verwendung von Richtlinien finden Sie unter:

+ [Richtlinien in Azure API Management](api-management-howto-policies.md)
+ [Transform and protect your API](transform-api.md) (Transformieren und Schützen von APIs)
+ Unter [Richtlinien für die API-Verwaltung](api-management-policy-reference.md) finden Sie eine komplette Liste der Richtlinienanweisungen und der zugehörigen Einstellungen.
+ [API Management policy samples](policy-samples.md) (API Management-Richtlinienbeispiele)   
