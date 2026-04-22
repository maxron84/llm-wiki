---
title: "LLM Wiki Tutorial - So baut KI dir eine Wissensbasis"
source: "https://www.youtube.com/watch?v=52LoAEo1C_M"
author:
  - "[[Mit Mario]]"
published: 2026-04-17
created: 2026-04-22
description: "Es gibt ein fundamentales Problem mit der Art wie wir KI für Recherche und Wissensaufbau nutzen. Alles landet in einem Chat. Unstrukturiert. Nicht verlinkbar. Und irgendwann läuft das Context Window ü"
tags:
  - "clippings"
---
![](https://www.youtube.com/watch?v=52LoAEo1C_M)

Es gibt ein fundamentales Problem mit der Art wie wir KI für Recherche und Wissensaufbau nutzen. Alles landet in einem Chat. Unstrukturiert. Nicht verlinkbar. Und irgendwann läuft das Context Window über und die ganze Arbeit ist weg.  
  
Andrej Karpathy hat dafür eine Lösung vorgestellt: das LLM Wiki. Die Idee: statt Wissen in einem Chat verschwinden zu lassen, baut die KI eine echte Wissensbasis aus verlinkten Markdown-Dateien. Jede neue Quelle wird einmal gelesen, die wichtigsten Konzepte werden extrahiert, bestehende Seiten werden aktualisiert und neue Verbindungen entstehen automatisch.  
  
In diesem Video erkläre ich das Konzept, baue ein LLM Wiki von Grund auf mit Claude Cowork und Obsidian und zeige live wie das Wiki mit jeder neuen Quelle wächst.  
  
Was ihr braucht:  
Obsidian (kostenlos): https://obsidian.md  
Claude Code/Cowork oder einen anderen Agent  
Eine CLAUDE.md Schema-Datei  
Obsidian Web Clipper: https://chromewebstore.google.com/detail/obsidian-web-clipper/cnjifjpddelmedmihgijeibhnjfabmlf  
  
🎓 Mein kostenloser 5-Tage-Kurs – vom Prompt-Tipper zum systematischen KI-Entwickler  
↪︎ https://mitmario.dev/kurse/ai-coding-partner  
  
📧 Mein Newsletter um nichts zu verpassen  
↪︎ https://mitmario.dev/newsletter  
  
🌟 Vorteile  
Kanalmitglied werden und exklusive Vorteile erhalten:  
↪︎ https://www.youtube.com/channel/UCMcbmRHNV1AwU7jLcK1x2Pw/join  
  
📌 Über diesen Kanal und mich  
Webentwicklung effizient und einfach. Alles rundum Webentwicklung, Webdesign und angrenzende Themen. Mein Name ist Mario und ich bin bereits seit vielen Jahren beruflich als Web Entwickler tätig. Einige Tutorials auf diesem Kanal sind für absolute Anfänger ohne Vorkenntnisse geeignet, und andere für erfahrene Entwickler.  
  
Starte Jetzt!  
https://www.youtube.com/channel/UCMcbmRHNV1AwU7jLcK1x2Pw?sub\_confirmation=1  
  
#ProgrammierenM #llmwiki #KI

## Transcript

**0:00** · Es gibt ein Problem mit der Art, wie wir gerade KI benutzen. Und wenn du es einmal siehst, kannst du es nicht mehr ignorieren. Stell dir vor, du ladest ein paar PDFateien in Chat GPT oder Cloud.

**0:18** · Du stellst Fragen, die KI durchsucht deine Dateien, zieht sich die relevanten Stellen raus und gibt dir Antworten.

**0:27** · Funktioniert kein Problem. soweit und klar, solange du im gleichen Chat bleibst, ist der Kontext noch da. Du kannst Folge Fragen stellen, alles gut, aber überleg mal, was passiert, wenn du wirklich anfängst damit zu arbeiten über Tage, über Wochen, immer mehr Quellen, immer mehr Fragen. Irgendwann wird der Chat so lang, dass die KI den Anfang vergisst. Das Kontextwind ist begrenzt.

**1:01** · Alles fällt raus und selbst wenn alles noch drin wäre, hast du am Ende einen riesigen unstrukturierten Chat. Du findest nichts wieder. Es gibt keine Querverweise, kein System, nur eine endlose Konversation, die immer schwerer zu navigieren wird. Und dann machst du einen neuen Chat auf, weil der Alte zu lang geworden ist.

**1:28** · Und dann start du tatsächlich wieder bei null. Die ganzen Zusammenhänge, die du dir erarbeitet hast, sind weg. Du kannst natürlich die Dokumente noch mal hochladen, aber die Synthese fehlt, die Verbindungen fehlen. Das Verständnis, dass ich über 20 Fragen aufgebaut hat, ist nicht mehr da. Das eigentliche Problem ist nicht, dass KI kein Gedächtnis hat. Das eigentliche Problem ist, das Wissen in einem Chat verschwindet.

**1:59** · Es wird nicht aufgebaut, es wird nicht strukturiert, es wird nicht verknüpft. Es ist einfach ein langer Chat, der irgendwann abläuft. Wel Corpy hat dafür eine Lösung vorgestellt.

**2:16** · Corpy kennt ihr vielleicht. Mitgründer von Open AI, ehemaliger KI Direktor bei Tesla, einer der wenigen Leute in der Branche, bei denen ich tatsächlich zuhöre, wenn sie was sagen. Seine Idee heißt LM Wiki und sie dreht das ganze Konzept um. Statt bei jeder Frage die rohen Dokumente zu durchsuchen, lässt die KI deine Quellen einmal lesen und ein strukturiertes Wiki daraus bauen.

**2:51** · Markdown Dateien verlinkt, organisiert, persistent. Wenn du eine neue Quelle hinzufügst, wird sie nicht einfach abgelegt. Die KI liest sie, extrahiert die wichtigen Konzepte, erstellt neue Seiten oder aktualisiert bestehende, verknüpft verwandte Ideen und wenn die neue Quelle etwas widerspricht, was schon im Wiki steht, dann wird's markiert. Das Wiki wächst also mit jeder Quelle, die du reinsteckst.

**3:21** · Die Verbindungen sind schon da. Die Zusammenfassung ist schon geschrieben.

**3:29** · Wenn du dann eine Frage stellst, arbeitet die KI nicht mehr mit deinen Roaddokumenten, sondern mit einer vorbereiteten Wissensbasis, die du selbst aufgebaut hast. Ich habe das letzte Woche zum ersten Mal ausprobiert und es hat mich ehrlich überrascht, nicht weil die Idee so revolutionär wäre, sondern weil es so offensichtlich besser ist als das, was wir vorher gemacht haben.

**4:00** · Ich erkläre euch kurz, wie das Ganze aufgebaut ist. Es gibt drei Ebenen und die sind simpel. Ebene 1, deine Roaten, PDFs, Artikel, Notizen, was auch immer du als Quelle hast, die kommen in einen Ordner und werden nie verändert.

**4:23** · Die KI liest daraus, aber fasst die Dateien nicht an. Ebene 2, das Wiki selbst ein Ordner voller Markdown Dateien, die die KI erstellt und pflegt.

**4:37** · Da gibt es eine Indexseite, Konzeptseiten, Vergleiche, alles verlinkt, alles strukturiert. Das ist die eigentliche Wissensbasis. Das baut die KI für dich. Ebene 3, das Schema.

**4:51** · Das ist ein Regelwerk, dass der KI sagt, wie sie das Wiki strukturieren soll.

**4:58** · Bei Cloud Code ist das die Cloud MDI.

**5:02** · Da steht drin, was der Zweck des Wigis ist, wie neue Quellen verarbeitet werden, wie Seiten formatiert werden und wie die KI Fragen beantworten soll.

**5:15** · Carpathy beschreibt das so: Obsidian ist die ID, KI ist der Programmierer und das Wiki ist die Codebase. Du schreibst das Wiki nicht selbst, die KI macht das. Du bestimmst, was reinkommt und welche Fragen du stellst. Und das finde ich als Entwickler besonders interessant, weil das im Kern das gleiche ist, was wir mit Cloud Code machen.

**5:43** · Du gibst Kontext vor, du definierst Regeln und die KI arbeitet innerhalb dieser Struktur.

**5:54** · Nur dass es hier nicht um Code geht, sondern um Wissen. Und das funktioniert für alles mögliche.

**6:02** · \[schnauben\] Technologierche, Projektdokumentation, persönliche Wissenssammlung. Ich habe es mit dem Thema Kochen und Ernährung ausprobiert, weil einfach weil ich mal was machen wollte, das nichts mit Code zu tun hat und es funktioniert genauso gut. Nun, was braucht ihr jetzt dafür? Als allererstes Obsidian. Ja, dieses kostenlose Tool.

**6:28** · Schärfe dein Denken. Kostenlos flexible App für deine privaten Gedanken. Und darin kannst du auch dein Wiki selbst schreiben, also die Dateien anlegen, Ordner anlegen und so weiter. Das werden wir aber nicht machen, weil das ist ja im Prinzip Aufgabe der KI. Was wir machen müssten, ist im Endeffekt nur das Projekt vorzubereiten.

**6:51** · Ähm, was ich darüber hinaus ganz gerne verwende, ist die Browserweiterung, die passend dazu ist, der Obsidian Webclipper. Ja, damit kannst du dann, wenn du auf äh dich, wenn du wenn du dich auf Seiten befindest, kannst du hier die Artikel, wenn du Blogartikel liest und so weiter, kannst du diese Seiten als Ressource ablegen auf deinem Rechner, einfach runterziehen. Ist super simpel, super einfach, werde ich dann auch mal zeigen.

**7:22** · Das nächste, womit ich jetzt arbeiten werde, ist Cloud Cowork. Ja, das ist die die Desktop App von Clord. Ähm, du könntest natürlich auch Cloud Code verwenden. Ja, das ist auch möglich.

**7:36** · Dann müsstest du dann aber mit der KI im Terminal arbeiten. Äh, für dieses Projekt nutze ich Cloud Cowork. Ist für mich einfacher und für dich vielleicht auch, wenn du nicht aus der Programmierung kommst.

**7:49** · Und was habe ich bisher gemacht? Ich habe hier äh Obsidian installiert. Ich habe äh das Projekt schon angelegt. Das ist sind einfach nur äh drei Ordner, die ich hier angelegt habe. Und die Cloud MDI, die werden wir uns ebenfalls gleich noch mal anschauen und zwar den Clippingsordner. Das ist der Ordner, wo dann im Endeffekt die äh von diesem Webclipper die Source Dateien abgelegt werden.

**8:20** · Dann habe ich hier einmal den ROner angelegt. Das ist, wenn ich manuell selbst persönlich Dateien da reinkopieren möchte von anderen Verzeichnissen, die ich schon auf dem Rechner habe oder sonst woher und einen Ordner für das eigentliche Wiki.

**8:38** · So, drei Ordner. und schon eine Markdown Datei namens Cloud. Wichtig ist für die KI, dass diese Datei ähm großgeschrieben ist in allen Buchstaben. Und was hier in der Datei drinne steht, ist im Endeffekt das, was jetzt die KI machen soll. Ja, das ist der Kontext für die KI und daran muss ich die KI dann auch im Endeffekt halten und da wird beschrieben, ja, was für ein Projekt ist es hier überhaupt?

**9:09** · Dann habe ich hier hingeschrieben eine persönliche Wissensdatenbank gepflegt von Cloud Code basierend auf Andre Corpties LM Wiki Muster. Dann gibst du den Zweck an. Dann habe ich geschrieben, dieses Wiki ist eine strukturierte vernetzte Wissensdatenbank für Rezepte und Ernährung. Und das ist vollkommen flexibel für was das ist.

**9:33** · Wichtig ist es nur, dass du es auch hier hinschreibst und wichtig wäre, dass du nur ein Thema hast, dass du nicht verschiedene, also komplett verschiedene Bereiche misst, ja, dass du für jedes Thema dir ein Wiki aufbaust, z.B. für Rezepte und Ernährung. Das kann aber auch sein, du planst eine Reise in ein äh äh Land, z.B. Japan, da könnst du z.B. auch Artikel sammeln, Wissen sammeln.

**10:03** · Ja, z.B. äh welche Orte gibt es in einem bestimmten Gebiet, äh wo kannst du wie was essen und so weiter. Also du planst und recherchierst und alle Artikel, die du dazu findest, packst du in das Wiki rein und könnst die KI fragen, ja, was kann ich dort und dort jetzt essen und wie lange braucht das, um dorthinzufahren und und so weiter. Ja, also da gibt's sehr viele Möglichkeiten.

**10:31** · Ich halte mich mal an Rezepte in Ernährung.

**10:34** · Ja, das dieses Wiki wird von Cloud gepflegt und der Mensch, also ich koratiert Quellen, stellt Fragen und leitet die Analyse. So, dann äh gehst du auf die Ordnerstruktur ein. Ja, ich habe hier meine drei Ordner, wo ich dann immer sage hier äh für was die da sind

**10:54** · und das soll darüber hinaus eine Indexdatei anlegen, ein Inhaltsverzeichnis für das gesamte Wiki und eine Logdatei als Anlagenprotokoll aller Vorgänge, das KI und auch ich nachvollziehen kann was alles an dem Wiki geändert wurde, was hinzukam und vor allen Dingen wann. Und das ist auch für die KI hilfreich zu schauen, wie was wann mal geändert wurde.

**11:23** · So, dann beschreibe ich den Aufnahmeworkflow.

**11:26** · Wenn der Benutzer eine neue Quelle zu Raw oder Clippings hinzufügt und dich bittet sie aufzunehmen, soll folgendes passieren. Ja, liest das vollständige Quelldokument. Ganz wichtig, erster Schritt. Zweiter, übersetze ins Deutsche, wenn die Quelle in einer anderen Sprache ist, ohne Information inhaltlich zu verändern. Für mich persönlich sehr wichtig.

**11:50** · Also, da bist du vollkommen frei, was du hier reinschreibst. Ja, das kannst du auch selbst festlegen. Dann bespreche die wichtigsten Erkenntnisse mit dem Benutzer, bevor du etwas schreibst.

**12:02** · Erstelle eine Zusammenfassungsseite in Wiki, benannt nach der Quelle. Erstelle oder aktualisiere Konzeptseiten für jede wichtige Idee oder Entität und dann füge Wikilinks hinzu, um verwandte Seiten zu verbinden und Wissen zu verknüpfen. Das ist ja überhaupt Sinn der Sache. Ja, kopiere Wikisseiten, die thematisch zusammengehören in Ordner und Unterordner, weil sonst würde er unter Wiki einfach alle Markdown Dateien einfach äh ja unorganisiert ablegen.

**12:39** · Wenn du da am Ende hunderte Artikel hast, wird das recht unübersichtlich.

**12:44** · Und dann aktualisiere Wiki Index mit neuen Seiten und einzeiligen Beschreibungen. Und als letztes hänge einen Eintrag an Wiki Log an mit dem Datum, Quellennamen und was sich geändert hat. Ja, eine einzelne Quelle kann 10 bis 15 Wiki Seiten betreffen.

**13:03** · Das ist normal.

**13:05** · Dann äh gebe ich das Seitenformat vor, das grundlegende. Ja, jede Wikisseite hat ein Seitentitel, eine kurze Zusammenfassung, die Quellen und was zuletzt aktualisiert wurde.

**13:18** · Hauptinhalt kommt mit klaren Überschriften und kurzen Absätzen. Verlinke verwandte Konzepte mit Hilfe von Wikilinks im gesamten Text und dann noch eine Bereich verwandte Seiten. Dann gibt's Zitierregeln, die kannst du hier definieren. Ähm, Fragen Beantwortung, wenn der Benutzer eine Frage stellt.

**13:41** · Liest zuerst Wiki Index, um relevante Seiten zu finden. Also, das ist der der Plan für die KI, wie diese vorgehen soll und machen soll, wenn du der KI eine Frage stellst. Ja, dann li diese Seiten und fasse eine Antwort zusammen. Zitiere spezifische Wigiten in deiner Antwort.

**14:01** · Wenn die Antwort nicht im Wiki steht, sage dies klar.

**14:06** · Wenn die Antwort wertvoll ist, biete an, sie als neue Wiki Seite zu speichern, weil gute Antworten sollten zurück ins Wiki eingetragen werden, damit sie sich über die Zeit weit, also damit sich über die Zeit weiteres Wissen aufbaut. Und das ist der Knackpunkt.

**14:24** · Das geht ja sonst, wenn du normal dich nur in dem Chat befindest und mit der KI arbeitest und der Chat läuft irgendwann voll, dann geht ja das Wissen verloren, wenn du einen neuen Chat aufmachst und wieder von vorne anfangen musst und mit diesem Wiki hast du die Lösung. So ganz dann gibt's noch Prüfregeln. Ja, wenn der Benutzer dich bitte das Wigel zu prüfen.

**14:49** · Prüfe auf Widersprüche zwischen Seiten, finde verweiste Seiten, identifiziere Konzepte, die in Seiten erwähnt werden, aber keine eigene Seite haben. Markiere Behauptungen, die aufgrund neuer Quellen veraltet sein könnten. Prüfe, ob alle Seiten dem obigen Seitenformat folgen. Berichte die Ergebnisse als nummerierte Liste mit Korrekturvorschlägen. Ja, und dann gibt's noch ein paar allgemeine Regeln.

**15:16** · Verändere niemals etwas äh in diesen Source äh Ordnern RA und Clippings in meinem Fall. Aktualisiere immer die Indexseite und die Logseite nach Änderung. Haltest Seitennamen in Kleinbuchstaben mit Bindestrichen. Z.B. maschinelles Lernen, also hier in klein geschrieben mit Bindestrich. Schreibe in klarer einfacher Sprache. Der Ton ist locker, freundlich und kollegial.

**15:44** · Wenn du dir unsicher bist, wie etwas kategorisiert werden soll, frage den Benutzer. Ja, das können für dich auch ganz andere Regeln sein. Du bestimmst, wie das Wiki aufgebaut werden soll und das musst du dann in dieser Datei reinschreiben. Ganz wichtig, sonst macht die KI irgendwas, was du nicht möchtest.

**16:06** · Ja, Planung ist in dem Fall alles.

**16:10** · So, und wie schaut's denn jetzt hier aus? Ja. Ich habe hier auch schon Cloud Cowork bei mir installiert. Ja, und in dem neuen Chatfenster habe ich hier gesagt hier in welchem Ordner der hier arbeiten soll. In dem Fall habe ich bei mir unter Laufwerk Csidien Ordner angelegt mit dem Ordner LM Wiki und die

**16:34** · KI arbeitet jetzt hier in diesem Ordner und bei diesem äh Obsidian, ja, das dient nur dafür, das ist nur dafür da, um sich das Ganze mal anzuschauen. Ja, und wenn du hier äh das zum ersten Mal öffnest, dann erscheint hier erstmal dieses Fenster und du ebenfalls dir eine In diesem Fall wird es hier Wort genannt. Ja, du ich habe es hier LM Wiki genannt. Das ist quasi die Wort ist der Ordner.

**17:06** · Du könnt natürlich auch Wort 101 reinschreiben oder 76 oder je nachdem.

**17:12** · Ja, da bist du vollkommen frei, wenn du das kennst. Und wenn du diesen Webklipper benutzt, was wir dann mal machen werden, äh speichert die Ressourcen dann hier automatisch in dem Clippingsordner und zwar nur in das Projekt, was du hier in Obsidien im Hintergrund offen hast. Ja, das ist ganz wichtig zu wissen.

**17:34** · Und wir könnten jetzt der KI jetzt mal folgende Frage stellen. Ähm, schau dir das Projekt an. Ja, undterlegt er los. Es ist halt noch nichts drin, aber der analysiert erstmal das, was da ist. Ja, was für ein Kontext da ist. Da guckt in die Anweisung der Clort MDI wahrscheinlich, hoffe ich mal.

**18:03** · Ja, was hat er jetzt hier? Hat jetzt sich das Projekt angesehen. Hier ist der aktuelle Stand. Projektstruktur Cloud MD Datei. Die Projektanweisungen enthalten. Der Rohrordner ist leer. Der Klippingsordner ist leer. Wiki leer, noch keine Index oder Logdatei und er hat ein Obsidian Konfigurationsordner gefunden.

**18:26** · Dann gibt er hier zurück, was der Zweck des Projektes ist. Ja, persönliche Wissensdatenbank.

**18:33** · M der Workflow ist klar definiert.

**18:36** · Das ist gut äh aktuell zu tun. Das Wiki ist noch völlig leer. Es gibt keine Quellen und keine Wikisseiten. Hat er gut erkannt und sobald Dokumente da sind, kann ich mit der Aufnahme beginnen, schreibt er hier.

**18:51** · Und jetzt fragt er mich, möchtest du, dass ich schon mal eine leere Gerüchtstruktur anlege? Also eine initiale Indexdatei und Logdatei?

**19:00** · Ja, mach das mal. Ja, los geht's.

**19:04** · Schreibe ich jetzt hier.

**19:07** · Undter legt er mir die fehlende Grundstruktur schon mal an.

**19:12** · Index MD erstellt. Ja, Logd erstellt.

**19:18** · Gründngerüst steht.

**19:23** · Alles klar. Und ich könnte mir das jetzt hier im äh hier schon im Wiki anschauen.

**19:30** · Habe ich hier eine Indexdatei mit Grundgerüst und ich hatte schon erste Sachen angelegt. Themenbereiche, Rezepte, Zutaten, Ernährung, Nährstoffe, Techniken, Methoden, verwandte Seiten, Log. Ja, hat das schon befüllt. Super.

**19:48** · Und das Schöne an Obsidien ist, du hast hier auch eine Grafansicht.

**19:54** · wo du schon sehen kannst, wie jetzt Artikel miteinander verbunden sind. Ja, Index mit Log z.B. Ja, und das wird dich nach und nach füllen. Das wird dann noch recht spannend werden, wie das ganze aussieht.

**20:09** · Jetzt brauchen wir natürlich Inhalt und ich habe mir da meine Seite rausgesucht, ja, Syries Eats, ja, äh wo ich mir mal paar Rezepte rausziehen möchte und auch andere Sachen, weil hier kannst du auch ähm noch Equipment gucken, da gibt's Artikel oder nach ähm Inhaltsstoffen, verschiedene Howosols, Techniken und so weiter.

**20:35** · Ja, und ich gucke mal nach Rezepten, ne? z.B. kannst du hier ähm halt Rezepte nach einer bestimmten Zutat dir anzeigen lassen. Ich mache mal nach einer bestimmten Diät und \[räuspern\] da gibt's z.B. glutenfreie Rezepte. Ja, das ist ja mal was wissenswertes.

**20:55** · Und was suchen wir uns denn mal schönes raus? Was gibt's denn hier alles?

**21:03** · Ah, z.B. Hier ein paar Kekse, glutenfreie Kekse mit M&amp;Ms.

**21:10** · Und jetzt habe ich hier die Seite und ich habe den Webclipper schon installiert bei mir.

**21:16** · Da kann ich jetzt hier sagen zu Obsidien hinzufügen. Ja, den Inhalt der Seite mache ich das mal. Zack, Obsidien öffnen.

**21:27** · Und jetzt habe ich hier neuen Artikel mit dem Rezept hier drinne im Ordner Clippings hier.

**21:37** · Und jetzt könnte ich sagen, der KI ähm es gibt eine neue Source Datei zum Verarbeiten.

**21:55** · So \[schnauben\] und es müsste der die finden. Ist da was Neues dazuekommen?

**22:03** · hat er schon erkannt. Hier glutenfreie M&amp;Ms, Zuckercookies, Soft Zucker Cookies, also sanfter Zucker. Gibt's sowas überhaupt? Ja, gefunden. \[räuspern\] Leg hatte richtig erkannt, die Mehlmischung Pflanzenfett stattbter.

**22:32** · Gut, ich nehme lieber lieber Butter eigentlich. Ja. Heller Maisissirup, niedrige Backtemperatur, Zitronenextrakt, zwei Methoden: Küchenmaschine oder Handmethode, Vorschlag für Wig Seiten. Also, er hat noch nichts umgesetzt. Ja, er macht erstmal nur ein Vorschlag. Das hat man die Anweisung steht in der in der Cloud MDI Rezeptseite einmal Wiki Rezepte glutenfreie M&amp;M Zuckerkekse.

**23:01** · Dann schlägt davor Wiki Seiten anzulegen für die Techniken. Ja, glutenfreies Backen, glutenfreie Mehlmischung und dann verschiedene Zutaten für Xantan, Reismehl, äh süßes Reismehl, Maisstärke, Maissirup, Pflanzenfett und so weiter.

**23:20** · Okay, meine Frage an dich, bevor ich loslege.

**23:24** · Wie tief willst du gehen? Minimal nur die Rezeptseite. Eine Übersichtsseite.

**23:28** · Glutenfreies Backen. Mittel mein Vorschlag. Rezeptseite Konzept Rezeptseite Konzeptseite glutenfreie Mehlmischung vollständig alles oben genannt. Jede Zutat ihre eigene Seite. Also ich brauche nicht hier für jede Zus Z Z Z Z Zutat eine eigene Seite. Das wäre ein bisschen too much.

**23:49** · Also nehme ich Antwort B.

**23:52** · B.

**23:54** · So. Und jetzt müsste der das anlegen.

**23:57** · Mal gucken. Und während der hier das macht, könn ich ja wieder auf der auf die Seite gehen und noch mal anderen Sachen gucken. Ja, hier werden z.B. zwei Eier verwendet und hier gibt's auch Eiergeids.

**24:15** · Da könn ich mir auch noch mal was rausziehen, z.B. Ähm, z.B. das hier, wie man einhändig Eier aufschlägt. Ich persönlich kann das nicht. Keine Ahnung. Ich brauche da immer, ich ich ich schlag das Ei immer am Schlüsselrand auf und äh benutzt dann mit zwei Händen quasi, um das die Schale dann aufzumachen. Ja, das wäre auch noch mal äh was cooles.

**24:45** · Das könnte ich mir noch mal ziehen mit dem Obsidian Webklipper. Drückst du drauf. Ja, dann ist das hinzugekommen.

**24:57** · Und was gibt's noch Schönes, was man nehmen könnt, ne?

**25:11** · Gibt's denn bei Techniken \[schnauben\] der sicherste Weg, um Eier aufzubewahren. Das könnte ich mal auch noch mal ziehen. Ja, ist bestimmt auch was wissenswertes.

**25:31** · So.

**25:33** · Jetzt schauen wir mal, wie äh wie weit Cloud ist. Ist immer noch am Erstellen.

**25:42** · Ja, der hat jetzt hier schon aber was angelegt, wie ich gesehen habe. Hier den Artikel glutenfreie Rezepte.

**25:51** · Bei Techniken steht schon was drinne.

**25:56** · Ja, bei Zutaten stehen einige Sachen drinne. Wie sieht's denn hier im Graf aus? Oh, da ist schon ein bisschen was dazu gekommen. Schaut mal.

**26:06** · Und da ist das ganz gut zu sehen, die Vernetzung der Artikel. Ja, hier der der die Rezeptseite ist ja verlinkt mit Reismilch, Santan, Maissirup und so weiter.

**26:21** · Da hat er schon gut aufgebaut und du kannst hier drin auch z.B. Artikel anklicken und dir anschauen und der hat einfrei ins Deutsche übersetzt. Das ist auch sehr wichtig.

**26:37** · Zubereitung.

**26:39** · Ja, wie weit ist er? Scheint fertig zu sein. Ja, genau. Fertig.

**26:44** · Und hat Prüfbericht. Der hat hier aktualisiert die Logdatei, die Indexdatei.

**26:50** · Bewusst offene passt zu Option B. Zwei Wiggillings zeigen ins Leere und sind als Platzhalter für später gedacht.

**26:59** · Reismehl und Maisstärke.

**27:01** · In Obsidien erscheinen sie als rote Links. Wenn wir in einem späteren Rezept wieder auf sie stoßen, legen wir sie an.

**27:08** · Falls dich das stört, kann ich sie auch jetzt schon als Stabs anlegen oder die Links in Klartext umwandeln. Sag Bescheid. Ja, sehr freundlich.

**27:18** · Und ich schreibe jetzt hier, es gibt neue Soces.

**27:23** · Es gibt neue Source Dateien.

**27:31** · Bitte verarbeite diese.

**27:37** · \[schnauben\] So, legt er wieder los und schaut nach neuen Artikeln. Jetzt hat er hier zwei gefunden, ne? Ja. wie man die Eier einhändig aufschlägt und wie man die Eier sicher aufbewahrt, vermutlich, damit sie nicht zu schnell verderben.

**28:03** · Jetzt geht's weiter. Mal schauen.

**28:06** · Es müsste da jetzt erstmal wieder vorschlagen, äh was da jetzt anlegt.

**28:10** · Also er bespricht mit mir, die KI bespricht mit mir, was dann als nächstes zu tun ist.

**28:22** · Wum es bei Quelle 1 geht. Einhandtechnik Crack and Reach. Eine Hand schlägt auf, die andere greift schon das nächste Ei.

**28:33** · Ja, für rechts und linkshänder mit dominanter Hand. Smash and Bash. Zwei Eier gleichzeitig eins in jeder Hand.

**28:41** · Leichter fürs Gehirn, wenn beide Hände ähnlich geschickt sind.

**28:47** · Kerngriff, Ringfinger und Kleinerfinger halten die breite Basis. Daumenzeige Mittelfinger hebeln die obere Hälfte auf. Eignet sich für geschlagene Eier, Rührei, Omelette, Fritata. Weniger für Spiegeleier. Bonustipp: bei wirklich vielen Eiern direkt in Sieb aufschlagen und mit einem Kellenboden durchdrücken.

**29:10** · Fängt Schalenstückchen ab und wird nebenbei erwähnt, aber nicht ausgeführt.

**29:15** · Alles klar.

**29:19** · Na gut, das hat ja den Vorschlag gemacht.

**29:23** · eine Zutatenseite, eine Technikene und noch eine Technikeneite.

**29:37** · Ich schreib mal ja, das passt.

**29:41** · Bitte umsetzen.

**29:47** · Na ja, da weiß schon, was ich meine.

**29:49** · Habe ich verschrieben. Nicht so schlimm.

**29:53** · Wir benutzen hier übrigens schon Opus 4.7. Kam gerade frisch raus. Ja, habe ich vom Krabbeltisch gepflückt. Nächste Woche ist das dann auch schon wieder altbacken. Ja, so schnell wie das immer geht.

**30:09** · Und aber diese Art von Aufgaben, da kannst du auch ein ein kleineres ein schwächeres Modell nehmen. Ja, da äh geht auch Clart Sonnet ja einmal frei.

**30:26** · Wenn du da Tokens sparen möchtest, ja, da musst du nicht gleich das hier Opos 4.7 Sieben nehmen.

**30:38** · So, der erstellt die Dateien. Gucken wir mal, ob sich hier schon was getan hat.

**30:42** · Ja. Eier.

**30:46** · Aufbau eines Eis. Hm. Frische Zimmertemperature Eier.

**30:53** · Ja, wie sieht der Graf aus? Da müsste jetzt wieder gewachsen sein. Ja, der hat ja auch schon verknüpft.

**31:03** · äh Eier mit der mit der glutenfreien mit den glutenfreien Zuckeräh Keksen mit dem Rezept, weil da werden ja Eier verwendet.

**31:16** · Wenn ich jetzt mal reinschaue, müssten jetzt hier die Eier verlinkt sein irgendwo.

**31:25** · Sehe es noch nicht. Ach, andersrum.

**31:27** · Okay, die die der Eierartikel ist verlinkt mit dem Rezept konkretes Rezept, das Eier verwendet. Okay, beide Quellen sind aufgenommen, übersetzt und verlinkt. Super. Neu angelegte Seiten.

**31:42** · Dann hat er Index und Lock aktualisiert und die Rezeptseite Zuckerkekse.

**31:50** · Super.

**31:53** · Und jetzt könnten wir eigentlich mal die KI prüfen lassen, ob es irgendwo welch welche Fehler gibt. Ja. Ähm, prüfe das Wiki auf Fehler.

**32:09** · Ja, vielleicht liegen ja irgendwelche äh Dateileichen schon rum oder irgendwelche Verknüpfungen sind fehlerhaft oder ja, mal schauen, ob er hier irgendwas findet. schon noch drei Artikeln, welche wir hier als Source hinzugefügt haben.

**32:25** · Währenddessen können wir selber mal in die Indexdatei reinschauen. Ja, hier hat er schon Themenbereiche gehabt, Rezepte mit jetzt haben wir gerade nur ein Rezept, Techniken und Methoden und Zutaten, Ernährung und Nährstoffe, da haben wir noch nichts.

**32:42** · Und im Lockintrag haben wir hier überall äh was er bisher gemacht hat. Heute Wiki initialisiert, die Quelle aufgenommen, dann noch mal zwei Quellen aufgenommen.

**32:53** · Ja, also step by Step und was er da angelegt hat oder bearbeitet hat.

**33:00** · Ja, super.

**33:03** · Hat er schon was gefunden? Nee, immer noch nicht.

**33:07** · Komm, mach mal schneller.

**33:09** · Wir haben alle keine Zeit. Ja, das kann unter anderem mehrere Minuten dauern.

**33:14** · Das ist Nummer Zeit für ein Kaffee. Ja.

**33:17** · gibst da k eine Anweisung, ähm gehst erstmal in die Küche und zapfst dir ein frischen Kaffee. Du kannst dir auch die Befehle dir mal anzeigen lassen, was da hier im Hintergrund alles macht. Ja, das ist auch immer mal ganz interessant zu lesen, welche Befehle der hier ausführt und so weiter.

**33:42** · Ja.

**33:48** · Ergebnis. Okay. Prüfung abgeschlossen.

**33:51** · Das Wiki ist in guter Form. Ein echter Fehler. Ein paar Hygienepunkte.

**33:57** · Okay. Fehlender Wikilink. Ah ja, im Rezept. Echter Fehler. Die Zutatenliste Eier existiert seit heute, aber im Rezept wird sie nirgends verlinkt. Richtig erkannt. Haben wir auch schon gesehen.

**34:12** · Vorschlag.

**34:15** · Teil 31 zu zwei große Eier ändern und Eier unter verwandte Seiten ergänzen.

**34:22** · Mhm. \[räuspern\] Richtig.

**34:29** · Was haben wir noch gefunden? Brote Wigilinks. Ah, das ist ja das, was er meinte hier, was vielleicht in Zukunft ähm ähm dazu kommen könnte.

**34:40** · erwähnte Konzepte ohne Links potenziell eine Seite die potenziell eine Seite verdienen.

**34:47** · Verweiste Seiten keine aller Seiten haben eingehende Links ist schon mal super Widersprüche zwischen Seiten keine.

**34:57** · Okay. Seitenformat. Alle zehn Seiten folgen dem Template super. Veraltte Behauptungen keine. \[schnauben\] Alles klar. zusammengefasst nur Punkt 1 ist ein echter Fehler. Soll ich den Link im Rezept ändern?

**35:11** · Ja, mach mal.

**35:14** · Okay, jetzt packt er den Link dazu.

**35:17** · Also, es ist ratsam, wenn du äh immer neue Sachen hinzugefügt hast, nach ähm ja, nach bestimmten Abständen mal das Wiki durch KI prüfen zu lassen, damit das alles konsistent bleibt und Feder behoben werden. Ja, jetzt hat er das erledigt. Jetzt können wir mal gucken ins Rezept.

**35:39** · Ja, jetzt hat er hier Eier verlinkt.

**35:43** · Schon mal super. Und hier unten ebenfalls hier als verwandte Seiten.

**35:49** · Und jetzt haben wir natürlich ähm Wissen hinzugefügt. Wir haben das Wiki geprüft und jetzt könnst du äh der KI ja Fragen stellen. Z.B. Ich brauche ein glutenfreies Rezept.

**36:10** · Zudem habe ich Probleme beim Eier aufschlagen.

**36:23** · So, mal schauen, was die KI jetzt mir antwortet.

**36:32** · Beides hat das Wigi abgedeckt. Schon mal gut. Das kann ich das kann ich dich direkt hinschicken. Glutenfreies Rezept.

**36:42** · Im Wigi steht genau eins. Glutenfreie Zuckerkekse. Weiche Zuckerkekse mit Schoko. 36 Stück auf aus einem Blech. C.

**36:52** · eine Stunde gesamt. Hmm. Okay. Wenn du etwas herzhaftes oder ein anderes äh glutenfreies Gebäck brauchst, hat das Wiki noch hatte hat das Wiki das noch nicht. Das war bisher die einzige Rezeptquelle. Eier aufschlagen.

**37:12** · Alles super. Ja.

**37:14** · Und was macht er hier? der guckt in das Wigi, ob er da Antworten findet und generiert daraus eine Zusammenfassung, eine eine Antwort auf auf meine Frage und der sucht der durchsucht hier nicht das Internet nach einer Antwort und äh ähm antwortet dir dann irgendwas, was er da findet, sondern der nutzt als Ressource deine eigene Wissensdatenbank, die du dir aufgebaut hast.

**37:46** · Ja, und der Wissensdatenbank kannst du ja immer wieder äh äh neue Artikel, neue Sachen hinzufügen, die für dich essentiell und wichtig sind für dieses Thema. Ja, wenn du selber im Internet recherchierst und sagst, ja, und dieser Artikel, der ist so gut, der passt bei mir rein. Ja, fügst du das hinzu und dann sagst du der KI äh, dass die das neue Wissen mit ins Wiki aufnehmen soll.

**38:13** · Ja, und dann kannst du damit weiterarbeiten und baust du damit was auf und ja, ich finde es super damit zu arbeiten.

**38:26** · Wenn dir das Video gefallen hat, gib mir doch einen Daumen nach oben. Ich würde mich freuen. Ansonsten empfehle ich für immer das nächste Video. Bis gleich.