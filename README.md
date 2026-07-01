# AR Sehsimulation

Eine WebAR Anwendung, die Sehbehinderungen simuliert, um Nutzer*innen ein Gefühl dafür zu geben, wie Betroffene die Welt wahrnehmen. 
Hauptbestandteil ist ein interaktives AR-Lernspiel, bei dem verschiedene Sehbehinderungen aktiv eine Aufgabe erschweren und zusätzlich gibt es einen Simulationsmodus, in dem die Sehbehinderungen frei erkundet werden kann.

## Motivation
Weltweit leben mehr als 100 Millionen Menschen mit einer chronischen Sehbehinderung, wobei Glaukom, altersbedingte Makuladegeneration und Grauer Star zu den häufigsten Ursachen zählen (Jones et al., 2020).

## Technischer Aufbau

Die Anwendung ist eine einzelne `index.html`, ohne Build Prozess und ohne Framework außer A-Frame und AR.js, beide über Content Delivery Network (CDN) eingebunden. Getrackt wird über einen Hiro Marker.

AR.js legt zwei getrennte Ebenen an: ein Video Element mit dem rohen Kamerabild und einen WebGL Canvas von A-Frame für alle virtuellen Objekte, mit transparentem Hintergrund. Beide Ebenen müssen für die Simulation unabhängig behandelt werden.

Für die Sehsimulation gibt es zwei Mechanismen. Katarakt (Grauer Star) und Rot Grün Schwäche werden über CSS Filter direkt auf Video und Canvas angewendet, das verändert die tatsächlichen Pixel beider Ebenen gleichzeitig. Glaukom und Makuladegeneration laufen über einen Canvas 2D Overlay, der in einer eigenen Render Schleife jeden Frame neu gezeichnet wird und automatisch über allem liegt, egal was darunter passiert.

Die Trennung von Video und Canvas fällt in der reinen Simulation nicht auf, weil dort keine virtuellen Objekte im Bild sind. Relevant wird sie im Lernspiel, dort sitzen die Luftballons auf dem WebGL Canvas, und ohne den Filter auch dort wären sie von der jeweiligen Sehbehinderung unberührt, obwohl sie Teil der Aufgabe sind.

Das Lernspiel wählt pro Runde zufällig eine der vier Sehbehinderungen (oder keine) aus, aktiviert die passende Simulation und lässt Luftballons aufsteigen, die angetippt werden müssen. Rote Ballons zählen als Fehler. Die Erkennung eines Treffers berechnet für jeden Ballon die Position auf dem Bildschirm aus seiner 3D Position und dem Kamerawinkel und sucht den nächsten Treffer in einem festen Radius.

## Bekannte Einschränkungen

CSS Filter lassen sich nicht auf die a-scene als Ganzes anwenden, sie müssen einzeln auf das Video Element und den WebGL Canvas gesetzt werden. Der WebXR dom overlay funktioniert im Oculus Browser nicht zuverlässig, deshalb läuft die Anwendung als normale Webseite im Browser statt als immersive WebXR Session. Das war eine bewusste Entscheidung für plattformübergreifende Kompatibilität ohne native App.

## Quellen

Jones, P. R., Somoskeöy, T., Chow-Wing-Bom, H., & Crabb, D. P. (2020). Seeing other perspectives: evaluating the use of virtual and augmented reality to simulate visual impairments (OpenVisSim). npj Digital Medicine, 3, 32. https://doi.org/10.1038/s41746-020-0242-6

Zwoliński, M. et al. (2025). DOI 10.1007/s10055-025-01150-z