<properties 
	pageTitle="Referenzmaterial für Analytics in Application Insights" 
	description="Reguläre Ausdrücke in Analytics, dem leistungsfähigen Suchtool von Application Insights." 
	services="application-insights" 
    documentationCenter=""
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/21/2016" 
	ms.author="awills"/>

# Application Insights: Referenzmaterial zu Analytics

[Analytics](app-analytics.md) ermöglicht die Ausführung leistungsstarker Abfragen der von [Application Insights](app-insights-overview.md) gesammelten Telemetriedaten. Auf diesen Seiten wird die Analytics-Abfragesprache beschrieben.


[AZURE.INCLUDE [app-analytics-top-index](../../includes/app-analytics-top-index.md)]

## Reguläre Ausdrücke



[> Allgemeine Beschreibung von regulären Ausdrücken](https://github.com/google/re2/wiki/Syntax).

Auf dieser Seite wird die Syntax für reguläre Ausdrücke aufgelistet, die von RE2 akzeptiert wird. 
Außerdem wird die von PCRE, PERL und VIM akzeptierte Syntax aufgeführt.

||
|---|---
|Einzelne Zeichen: | 
|. |Alle Zeichen, möglicherweise auch Zeilenvorschub (s=true) 
|[xyz] |Zeichenklasse 
|[^xyz] |Negierte Zeichenklasse 
|\\d |Perl-Zeichenklasse 
|\\D |Negierte Perl-Zeichenklasse 
|[[:alpha:]] |ASCII-Zeichenklasse 
|[[:^alpha:]] |Negierte ASCII-Zeichenklasse 
|\\pN |Unicode-Zeichenklasse (Name aus nur einem Buchstaben) 
|\\p{Greek} |Unicode-Zeichenklasse 
|\\PN |Negierte Unicode-Zeichenklasse (Name aus nur einem Buchstaben) 
|\\P{Greek} |Negierte Unicode-Zeichenklasse 
|Zusammensetzungen: | 
|xy |x, gefolgt von y 
|x\|y |x oder y (bevorzugt x) 
| 
|Wiederholungen: | 
| |null oder mehr x, mehr bevorzugt 
|x+ |ein oder mehr x, mehr bevorzugt 
|x? |null oder ein x, eins bevorzugt 
|x{n,m} |n oder n+1 oder ... oder m x, mehr bevorzugt 
|x{n,} |n oder mehr x, mehr bevorzugt 
|x{n} |genau n x 
|x*? |null oder mehr x, weniger bevorzugt 
|x+? |ein oder mehr x, weniger bevorzugt 
|x?? |null oder ein x, null bevorzugt 
|x{n,m}? |n oder n+1 oder ... oder m x, weniger bevorzugt 
|x{n,}? |n oder mehr x, weniger bevorzugt 
|x{n}? |genau n x 
|x{} |(== x*) (NICHT UNTERSTÜTZT) VIM 
|x{-} |(== x*?) (NICHT UNTERSTÜTZT) VIM 
|x{-n} |(== x{n}?) (NICHT UNTERSTÜTZT) VIM 
|x= |(== x?) (NICHT UNTERSTÜTZT) VIM 
|Implementierungseinschränkung: Die Zählformen x{n,m}, x{n,} und x{n} | 
|lehnen Formen ab, die eine minimale oder maximale Wiederholungsanzahl über 1000 erstellen. | 
|Diese Einschränkung gilt nicht für unbegrenzte Wiederholungen. | 
|Possessivwiederholungen: | 
|x*+ |null oder mehr x, possessiv (NICHT UNTERSTÜTZT) 
|x++ |ein oder mehr x, possessiv (NICHT UNTERSTÜTZT) 
|x?+ |null oder mehr x, possessiv (NICHT UNTERSTÜTZT) 
|x{n,m}+ |n oder ... oder m x, possessiv (NICHT UNTERSTÜTZT) 
|x{n,}+ |n oder mehr x, possessiv (NICHT UNTERSTÜTZT) 
|x{n}+ |genau n x, possessiv (NICHT UNTERSTÜTZT) 
|Gruppierung: | 
|(re) |nummerierte Erfassungsgruppe (untergeordnete Übereinstimmung) 
|(?P<name>re) |benannte und nummerierte Erfassungsgruppe (untergeordnete Übereinstimmung) 
|(?<name>re) |benannte und nummerierte Erfassungsgruppe (untergeordnete Übereinstimmung) (NICHT UNTERSTÜTZT) 
|(?'name're) |benannte und nummerierte Erfassungsgruppe (untergeordnete Übereinstimmung) (NICHT UNTERSTÜTZT) 
|(?:re) |Gruppe ohne Erfassung 
|(?flags) |Flags in aktueller Gruppe festlegen; ohne Erfassung 
|(?flags:re) |Flags während re festlegen; ohne Erfassung 
|(?#text) |Kommentar (NICHT UNTERSTÜTZT) 
|(?|x|y|z) |Rücksetzung der Verzweigungsnummerierung (NICHT UNTERSTÜTZT) 
|(?>re) |possessive Übereinstimmung von re (NICHT UNTERSTÜTZT) 
|re@> |possessive Übereinstimmung von re (NICHT UNTERSTÜTZT) VIM 
|%(re) |Gruppe ohne Erfassung (NICHT UNTERSTÜTZT) VIM 
|Flags: | 
|i |ohne Beachtung der Groß-/Kleinschreibung (Standard: false) 
|m |mehrzeiliger Modus: ^ und $ stimmen mit Zeile beginnen/enden neben Text beginnen/enden überein (Standard: false) 
|s |. soll mit \\n übereinstimmen (Standard: false) 
|U |ungreedy: Bedeutung von x* und x*?, x+ und x+? usw. austauschen (Standard: false) 
|Flagsyntax lautet xyz (festlegen) oder -xyz (löschen) oder xy-z (xy festlegen, z löschen). | 
|Leere Zeichenfolgen: | 
|^ |am Anfang von Text oder Zeile (m=true) 
|$ |am Ende von Text (wie \\z nicht \\Z) oder Zeile (m=true) 
|\\A |am Anfang von Text 
|\\b |an ASCII-Wortgrenze (\\w auf der einen Seite und \\W, \\A oder \\z auf der anderen) 
|\\B |nicht an ASCII-Wortgrenze 
|\\G |am Anfang des durchsuchten untergeordneten Texts (NICHT UNTERSTÜTZT) PCRE 
|\\G |am Ende der letzten Übereinstimmung (NICHT UNTERSTÜTZT) PERL 
|\\Z |am Ende von Text oder vor Zeilenvorschub am Ende von Text (NICHT UNTERSTÜTZT) 
|\\z |am Ende von Text 
|(?=re) |vor mit re übereinstimmendem Text (NICHT UNTERSTÜTZT) 
|(?!re) |vor nicht mit re übereinstimmendem Text (NICHT UNTERSTÜTZT) 
|(?<=re) |nach mit re übereinstimmendem Text (NICHT UNTERSTÜTZT) 
|(?<!re) |nach nicht mit re übereinstimmendem Text (NICHT UNTERSTÜTZT) 
|re& |vor mit re übereinstimmendem Text (NICHT UNTERSTÜTZT) VIM 
|re@= |vor mit re übereinstimmendem Text (NICHT UNTERSTÜTZT) VIM 
|re@! |vor nicht mit re übereinstimmendem Text (NICHT UNTERSTÜTZT) VIM 
|re@<= |nach mit re übereinstimmendem Text (NICHT UNTERSTÜTZT) VIM 
|re@<! |nach nicht mit re übereinstimmendem Text (NICHT UNTERSTÜTZT) VIM 
|\\zs |bestimmt Anfang der Übereinstimmung (= \\K) (NICHT UNTERSTÜTZT) VIM 
|\\ze |bestimmt Ende der Übereinstimmung (NICHT UNTERSTÜTZT) VIM 
|\\%^ |Anfang der Datei (NICHT UNTERSTÜTZT) VIM 
|\\%$ |Ende der Datei (NICHT UNTERSTÜTZT) VIM 
|\\%V |auf Bildschirm (NICHT UNTERSTÜTZT) VIM 
|\\%# |Cursorposition (NICHT UNTERSTÜTZT) VIM 
|\\%'m |m-Position markieren (NICHT UNTERSTÜTZT) VIM 
|\\%23l |in Zeile 23 (NICHT UNTERSTÜTZT) VIM 
|\\%23c |in Spalte 23 (NICHT UNTERSTÜTZT) VIM 
|\\%23v |in virtueller Spalte 23 (NICHT UNTERSTÜTZT) VIM 
|Escapesequenzen: | 
|\\a |Glocke (== \\007) 
|\\f |Seitenvorschub (== \\014) 
|\\t |horizontaler Tab (== \\011) 
|\\n |Zeilenvorschub (== \\012) 
|\\r |Wagenrücklauf (== \\015) 
|\\v |vertikales Tabzeichen (== \\013) 
|* |Literal *, für jedes Interpunktionszeichen * 
|\\123 |oktaler Zeichencode (bis zu drei Zeichen) 
|\\x7F |hexadezimaler Zeichencode (genau zwei Zeichen) 
|\\x{10FFFF} |hexadezimaler Zeichencode 
|\\C |Übereinstimmung mit einem einzelnen Byte, selbst im UTF-8-Modus 
|\\Q...\\E |Konstanter Text ... selbst wenn ... über Interpunktion verfügt 
|\\1 |Rückbezug (NICHT UNTERSTÜTZT) 
|\\b |Rückschritt (NICHT UNTERSTÜTZT) (verwenden Sie \\010) 
|\\cK |Steuerungszeichen ^K (NICHT UNTERSTÜTZT) (verwenden Sie \\001 usw.) 
|\\e |Escape (NICHT UNTERSTÜTZT) (verwenden Sie \\033) 
|\\g1 |Rückbezug (NICHT UNTERSTÜTZT) 
|\\g{1} |Rückbezug (NICHT UNTERSTÜTZT) 
|\\g{+1} |Rückbezug (NICHT UNTERSTÜTZT) 
|\\g{-1} |Rückbezug (NICHT UNTERSTÜTZT) 
|\\g{name} |benannter Rückbezug (NICHT UNTERSTÜTZT) 
|\\g<name> |Unterroutinenaufruf (NICHT UNTERSTÜTZT) 
|\\g'name' |Unterroutinenaufruf (NICHT UNTERSTÜTZT) 
|\\k<name> |benannter Rückbezug (NICHT UNTERSTÜTZT) 
|\\k'name' |benannter Rückbezug (NICHT UNTERSTÜTZT) 
|\\lX |kleingeschriebenes X (NICHT UNTERSTÜTZT) 
|\\ux |großgeschriebenes x (NICHT UNTERSTÜTZT) 
|\\L...\\E |kleingeschriebener Text... (NICHT UNTERSTÜTZT) 
|\\K |Anfang von $0 zurücksetzen (NICHT UNTERSTÜTZT) 
|\\N{name} |benanntes Unicode-Zeichen (NICHT UNTERSTÜTZT) 
|\\R |Zeilenumbruch (NICHT UNTERSTÜTZT) 
|\\U...\\E |großgeschriebener Text ... (NICHT UNTERSTÜTZT) 
|\\X |erweiterte Unicode-Sequenz (NICHT UNTERSTÜTZT) 
|\\%d123 |dezimales Zeichen 123 (NICHT UNTERSTÜTZT) VIM 
|\\%xFF |hexadezimales Zeichen FF (NICHT UNTERSTÜTZT) VIM 
|\\%o123 |oktales Zeichen 123 (NICHT UNTERSTÜTZT) VIM 
|\\%u1234 |Unicode-Zeichen 0x1234 (NICHT UNTERSTÜTZT) VIM 
|\\%U12345678 |Unicode-Zeichen 0x12345678 (NICHT UNTERSTÜTZT) VIM 
|Zeichenklassenelemente: | 
|x |einzelnes Zeichen 
|A-Z |Zeichenbereich (inklusive) 
|\\d |Perl-Zeichenklasse 
|[:foo:] |ASCII-Zeichenklasse foo 
|\\p{Foo} |Unicode-Zeichenklasse Foo 
|\\pF |Unicode-Zeichenklasse F (Name aus einem Buchstaben) 
|Benannte Zeichenklassen als Zeichenklassenelemente: | 
|[\\d] |Ziffern (== \\d) 
|[^\\d] |keine Ziffern (== \\D) 
|[\\D] |keine Ziffern (== \\D) 
|[^\\D] |nicht keine Ziffern (== \\d) 
|[[:name:]] |benannte ASCII-Klasse in Zeichenklasse (== [:name:]) 
|[^[:name:]] |benannte ASCII-Klasse in negierter Zeichenklasse (== [:^name:]) 
|[\\p{Name}] |benannte Unicode-Eigenschaft in Zeichenklasse (== \\p{Name}) 
|[^\\p{Name}] |benannte Unicode-Eigenschaft in negierter Zeichenklasse (== \\P{Name}) 
|Perl-Zeichenklassen (alle nur ASCII): | 
|\\d |Ziffern (== [0-9]) 
|\\D |keine Ziffern (== [^0-9]) 
|\\s |Leerraum (== [\\t\\n\\f\\r ]) 
|\\S |kein Leerraum (== [^\\t\\n\\f\\r ]) 
|\\w |Wortzeichen (== [0-9A-Za-z\_]) 
|\\W |keine Wortzeichen (== [^0-9A-Za-z\_]) 
|\\h |horizontales Leerzeichen (NICHT UNTERSTÜTZT) 
|\\H |kein horizontales Leerzeichen (NICHT UNTERSTÜTZT) 
|\\v |vertikales Leerzeichen (NICHT UNTERSTÜTZT) 
|\\V |kein vertikales Leerzeichen (NICHT UNTERSTÜTZT) 
|ASCII-Zeichenklassen: | 
|[[:alnum:]] |alphanumerisch (== [0-9A-Za-z]) 
|[[:alpha:]] |alphabetisch (== [A-Za-z]) 
|[[:ascii:]] |ASCII (== [\\x00-\\x7F]) 
|[[:blank:]] |Leerzeichen (== [\\t ]) 
|[[:cntrl:]] |Steuerung (== [\\x00-\\x1F\\x7F]) 
|[[:digit:]] |Ziffern (== [0-9]) 
|[[:graph:]] |grafisch (== [!-~] == [A-Za-z0-9!"#$%&'()*+,-./:;<=>?@[\\]^\_`{|}~]) 
|[[:lower:]] |lower case (== [a-z]) 
|[[:print:]] |printable (== [ -~] == [ [:graph:]]) 
|[[:punct:]] |punctuation (== [!-/:-@[-`{-~]) 
|[[:space:]] |Leerraum (== [\\t\\n\\v\\f\\r ]) 
|[[:upper:]] |Großschreibung (== [A-Z]) 
|[[:word:]] |Wortzeichen (== [0-9A-Za-z\_]) 
|[[:xdigit:]] |hexadezimale Ziffer (== [0-9A-Fa-f]) 
|Namen der Unicode-Zeichenklassen – allgemeine Kategorie: | 
|C |Sonstiges 
|Cc |Steuerung 
|Cf |Format 
|Cn |nicht zugewiesene Codepunkte (NICHT UNTERSTÜTZT) 
|Co |private Nutzung 
|Cs |Ersatz 
|L |Buchstabe 
|LC |Buchstabe in richtiger Groß-/Kleinschreibung (NICHT UNTERSTÜTZT) 
|L& |Buchstabe in richtiger Groß-/Kleinschreibung (NICHT UNTERSTÜTZT) 
|Ll |kleingeschriebener Buchstabe 
|Lm |Zusatzbuchstabe 
|Lo |sonstiger Buchstabe 
|Lt |erster Buchstabe groß 
|Lu |großgeschriebener Buchstabe 
|M |Markierung 
|Mc |Markierung mit Zwischenraum 
|Me |einschließende Markierung 
|Mn |Markierung ohne Zwischenraum 
|N |Nummer 
|Nd |Dezimalnummer 
|Nl |Buchstabennummer 
|No |sonstige Nummer 
|P |Interpunktion 
|Pc |verbindende Interpunktion 
|Pd |Strichinterpunktion 
|Pe |schließende Interpunktion 
|Pf |abschließende Interpunktion 
|Pi |anfängliche Interpunktion 
|Po |sonstige Interpunktion 
|Ps |öffnende Interpunktion 
|S |Symbol 
|Sc |Währungssymbol 
|Sk |Zusatzsymbol 
|Sm |mathematisches Symbol 
|So |sonstiges Symbol 
|Z |Trennzeichen 
|Zl |Zeilentrennzeichen 
|Zp |Absatztrennzeichen 
|Zs |Leerzeichentrennzeichen 
|Namen der Unicode-Zeichenklassen - Skripte: | 
|Arabic |Arabisch 
|Armenian |Armenisch 
|Balinese |Balinesisch 
|Bamum |Bamum 
|Batak |Batak 
|Bengali |Bangla 
|Bopomofo |Bopomofo 
|Brahmi | Brahmi 
|Braille |Braille 
|Buginese |Buginesisch 
|Buhid |Buhid 
|Canadian\_Aboriginal |kanadische Ureinwohner 
|Carian |Karisch 
|Chakma |Chakma 
|Cham |Cham 
|Cherokee |Cherokee 
|Common |Zeichen, die nicht für ein Skript spezifisch sind 
|Coptic |Koptisch 
|Cuneiform |Cuneiform 
|Cypriot |Zyprisch 
|Cyrillic |Kyrillisch 
|Deseret |Deseret 
|Devanagari |Devanagari 
|Egyptian\_Hieroglyphs |ägyptische Hieroglyphen 
|Ethiopic |Äthiopisch 
|Georgian |Georgisch 
|Glagolitic |Glagolitisch 
|Gothic |Gotisch 
|Greek |Griechisch 
|Gujarati |Gujarati 
|Gurmukhi |Gurmukhi 
|Han |Han 
|Hangul |Hangul 
|Hanunoo |Hanunoo 
|Hebrew |Hebräisch 
|Hiragana |Hiragana 
|Imperial\_Aramaic |Armi 
|Inherited |Skript aus vorherigem Zeichen übernehmen 
|Inscriptional\_Pahlavi |Phli (Inschriften-Pahlavi) 
|Inscriptional\_Parthian |Prti (Inschriften-Parthisch) 
|Javanese |Javanisch 
|Kaithi |Kthi (Kaithi) 
|Kannada |Kannada 
|Katakana |Katakana 
|Kayah\_Li |Kayah Li 
|Kharoshthi |Kharoshthi 
|Khmer |Khmer 
|Lao |Laotisch 
|Latin |Lateinisch 
|Lepcha |Lepcha 
|Limbu |Limbu 
|Linear\_B |Linearschrift B 
|Lycian |Lykisch 
|Lydian |Lydisch 
|Malayalam |Malayalam 
|Mandaic |Mandäisch 
|Meetei\_Mayek |Metei Mayek 
|Meroitic\_Cursive |Meroitisch kursiv 
|Meroitic\_Hieroglyphs |meroitische Hieroglyphen 
|Miao |Miao 
|Mongolian |Mongolisch 
|Myanmar |Birmanisch 
|New\_Tai\_Lue |Neu-Tai-Lue (auch bekannt als Vereinfachtes Tai Lue) 
|Nko |Nko 
|Ogham |Ogham 
|Ol\_Chiki |Ol Chiki 
|Old\_Italic |Altitalisch 
|Old\_Persian |Altpersisch 
|Old\_South\_Arabian |Altsüdarabisch 
|Old\_Turkic |Alttürkisch 
|Oriya |Odia 
|Osmanya |Osmanisch 
|Phags\_Pa |'Phags Pa 
|Phoenician |Phönizisch 
|Rejang |Rejang 
|Runic |Runisch 
|Saurashtra |Saurashtra 
|Sharada |Sharada 
|Shavian |Shaw-Alphabet 
|Sinhala |Singhalesisch 
|Sora\_Sompeng |Sora Sompeng 
|Sundanese |Sundanesisch 
|Syloti\_Nagri |Syloti Nagri 
|Syriac |Syrisch 
|Tagalog |Tagalog 
|Tagbanwa |Tagbanwa 
|Tai\_Le |Tai Le 
|Tai\_Tham |Tai Tham 
|Tai\_Viet |Tavt 
|Takri |Takri 
|Tamil |Tamil 
|Telugu |Telugu 
|Thaana |Thaana 
|Thai |Thailändisch 
|Tibetan |Tibetisch 
|Tifinagh |Tifinagh 
|Ugaritic |Ugaritisch 
|Vai |Vai 
|Yi |Yi 
|Vim-Zeichenklassen: | 
|\\i |Bezeichnerzeichen (NICHT UNTERSTÜTZT) VIM 
|\\I |\\i mit Ausnahme von Ziffern (NICHT UNTERSTÜTZT) VIM 
|\\k |Schlüsselwortzeichen (NICHT UNTERSTÜTZT) VIM 
|\\K |\\k mit Ausnahme von Ziffern (NICHT UNTERSTÜTZT) VIM 
|\\f |Dateinamenzeichen (NICHT UNTERSTÜTZT) VIM 
|\\F |\\f mit Ausnahme von Ziffern (NICHT UNTERSTÜTZT) VIM 
|\\p |druckbares Zeichen (NICHT UNTERSTÜTZT) VIM |\\P 
|\\p mit Ausnahme von Ziffern (NICHT UNTERSTÜTZT) VIM 
|\\s |Leerraumzeichen (== [ \\t]) (NICHT UNTERSTÜTZT) VIM 
|\\S |kein Leerraumzeichen (== [^ \\t]) (NICHT UNTERSTÜTZT) VIM 
|\\d |Ziffern (== [0-9]) VIM 
|\\D |nicht \\d VIM 
|\\x |hexadezimale Ziffern (== [0-9A-Fa-f]) (NICHT UNTERSTÜTZT) VIM 
|\\X |nicht \\x (NICHT UNTERSTÜTZT) VIM 
|\\o |oktale Ziffern (== [0-7]) (NICHT UNTERSTÜTZT) VIM 
|\\O |nicht \\o (NICHT UNTERSTÜTZT) VIM 
|\\w |Wortzeichen VIM 
|\\W |nicht \\w VIM 
|\\h |Kopf von Wortzeichen (NICHT UNTERSTÜTZT) VIM 
|\\H |nicht \\h (NICHT UNTERSTÜTZT) VIM 
|\\a |alphabetisch (NICHT UNTERSTÜTZT) VIM 
|\\A |nicht \\a (NICHT UNTERSTÜTZT) VIM 
|\\l |kleingeschrieben (NICHT UNTERSTÜTZT) VIM 
|\\L |nicht kleingeschrieben (NICHT UNTERSTÜTZT) VIM 
|\\u |großgeschrieben (NICHT UNTERSTÜTZT) VIM 
|\\U |nicht großgeschrieben (NICHT UNTERSTÜTZT) VIM 
|\_x |\\x plus Zeilenvorschub, für jedes x (NICHT UNTERSTÜTZT) VIM 
|Vim-Flags: | 
|\\c |Groß-/Kleinschreibung ignorieren (NICHT UNTERSTÜTZT) VIM 
|\\C |Groß-/Kleinschreibung anpassen (NICHT UNTERSTÜTZT) VIM 
|\\m |magic (NICHT UNTERSTÜTZT) VIM 
|\\M |nomagic (NICHT UNTERSTÜTZT) VIM 
|\\v |verymagic (NICHT UNTERSTÜTZT) VIM 
|\\V |verynomagic (NICHT UNTERSTÜTZT) VIM 
|\\Z |Unterschiede von Unicode-kombinierenden Zeichen ignorieren (NICHT UNTERSTÜTZT) VIM 
|Magic: | 
|(?{code}) |zufälliger Perl-Code (NICHT UNTERSTÜTZT) PERL 
|(??{code}) |zurückgestellter zufälliger Perl-Code (NICHT UNTERSTÜTZT) PERL 
|(?n) |rekursiver Aufruf der regexp-Erfassungsgruppe n (NICHT UNTERSTÜTZT) 
|(?+n) |rekursiver Aufruf der relativen Gruppe +n (NICHT UNTERSTÜTZT) 
|(?-n) |rekursiver Aufruf der relativen Gruppe -n (NICHT UNTERSTÜTZT) 
|(?C) |PCRE-Popup (NICHT UNTERSTÜTZT) PCRE 
|(?R) |rekursiver Aufruf der gesamten regexp (== (?0)) (NICHT UNTERSTÜTZT) 
|(?&name) |rekursiver Aufruf der benannten Gruppe (NICHT UNTERSTÜTZT) 
|(?P=name) |benannter Rückbezug (NICHT UNTERSTÜTZT) 
|(?P>name) |rekursiver Aufruf der benannten Gruppe (NICHT UNTERSTÜTZT) 
|(?(cond)true|false) |konditionale Verzweigung (NICHT UNTERSTÜTZT) 
|(?(cond)true) |konditionale Verzweigung (NICHT UNTERSTÜTZT) 
|(*ACCEPT) |regexps mehr wie Prolog machen (NICHT UNTERSTÜTZT) 
|(*COMMIT) |(NICHT UNTERSTÜTZT) 
|(*F) |(NICHT UNTERSTÜTZT) 
|(*FAIL) |(NICHT UNTERSTÜTZT) 
|(*MARK) |(NICHT UNTERSTÜTZT) 
|(*PRUNE) |(NICHT UNTERSTÜTZT) 
|(*SKIP) |(NICHT UNTERSTÜTZT) 
|(*THEN) |(NICHT UNTERSTÜTZT) 
|(*ANY) |set newline convention (NICHT UNTERSTÜTZT) 
|(*ANYCRLF) |(NICHT UNTERSTÜTZT) 
|(*CR) |(NICHT UNTERSTÜTZT) 
|(*CRLF) |(NICHT UNTERSTÜTZT) 
|(*LF) |(NICHT UNTERSTÜTZT) 
|(*BSR_ANYCRLF) |set \R convention (NICHT UNTERSTÜTZT)  PCRE 
|(*BSR_UNICODE) |(NICHT UNTERSTÜTZT)  PCRE 




[AZURE.INCLUDE [app-analytics-footer](../../includes/app-analytics-footer.md)]

<!----HONumber=AcomDC_0330_2016-->